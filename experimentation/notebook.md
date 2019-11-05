---
title: "Toy example with ggnetworkx"
author: "CSL"
date: "2019-11-5"
output:
  html_document:
    keep_md: true
---

[genenetworkx](https://github.com/KlausVigo/ggnetworx)

To install:

```r
devtools::install_github("GuangchuangYu/ggtree")
devtools::install_github("KlausVigo/ggnetworx")
install.packages("rgl")
```



```r
library(ggnetworx)
```

```
## Loading required package: ggplot2
```

```
## Loading required package: ggtree
```

```
## Registered S3 method overwritten by 'treeio':
##   method     from
##   root.phylo ape
```

```
## ggtree v2.1.1  For help: https://yulab-smu.github.io/treedata-book/
## 
## If you use ggtree in published research, please cite the most appropriate paper(s):
## 
## [36m-[39m Guangchuang Yu, Tommy Tsan-Yuk Lam, Huachen Zhu, Yi Guan. Two methods for mapping and visualizing associated data on phylogeny using ggtree. Molecular Biology and Evolution 2018, 35(12):3041-3043. doi: 10.1093/molbev/msy194
## [36m-[39m Guangchuang Yu, David Smith, Huachen Zhu, Yi Guan, Tommy Tsan-Yuk Lam. ggtree: an R package for visualization and annotation of phylogenetic trees with their covariates and other associated data. Methods in Ecology and Evolution 2017, 8(1):28-36, doi:10.1111/2041-210X.12628
```

```r
?ggnetworkx  ## we get the examples
```

```
## No documentation for 'ggnetworkx' in specified packages and libraries:
## you could try '??ggnetworkx'
```

There are two options: 1) split networks, and 2) explicit networks (from extended newick).

## Split networks


```r
### split networks
data(yeast, package="phangorn")
yeast ## sequences
```

```
## 8 sequences with 127026 character and 8899 different site patterns.
## The states are a c g t
```

```r
dm <- phangorn::dist.ml(yeast) 
dm  ## distance matrix
```

```
##            Scer       Spar       Smik       Skud       Sbay       Scas
## Spar 0.08678458                                                       
## Smik 0.13763679 0.12360062                                            
## Skud 0.16108716 0.14928416 0.15537064                                 
## Sbay 0.17717456 0.16637524 0.17339543 0.15679569                      
## Scas 0.34537380 0.34396524 0.34511183 0.35141142 0.34592156           
## Sklu 0.38167995 0.38072459 0.38103854 0.38397653 0.38096004 0.39014046
## Calb 0.53723427 0.53742766 0.53787911 0.54839913 0.54884541 0.52768392
##            Sklu
## Spar           
## Smik           
## Skud           
## Sbay           
## Scas           
## Sklu           
## Calb 0.54213286
```

```r
nnet <- phangorn::neighborNet(dm)
nnet
```

```
## 
## Phylogenetic tree with 8 tips and 37 internal nodes.
## 
## Tip labels:
## 	Skud, Sbay, Sklu, Calb, Scas, Scer, ...
## 
## Unrooted; includes branch lengths.
```

```r
ggnetworx(nnet) + geom_tiplab2()
```

![](notebook_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

```r
plot(nnet)
```

```
## Error in dyn.load(file, DLLpath = DLLpath, ...) : 
##   unable to load shared object '/Library/Frameworks/R.framework/Versions/3.6/Resources/library/rgl/libs/rgl.so':
##   dlopen(/Library/Frameworks/R.framework/Versions/3.6/Resources/library/rgl/libs/rgl.so, 6): Library not loaded: /opt/X11/lib/libGLU.1.dylib
##   Referenced from: /Library/Frameworks/R.framework/Versions/3.6/Resources/library/rgl/libs/rgl.so
##   Reason: image not found
```

```
## Warning in plot.networx(nnet): type='3D' requires the package 'rgl', plotting in '2D' instead!
```

![](notebook_files/figure-html/unnamed-chunk-3-2.png)<!-- -->

## Explicit networks


```r
### explicit networks
enet <- ape::read.evonet(text="((a:2,(b:1)#H1:1):1,(#H1,c:1):2);")
enet  ## explicit network
```

```
## 
##     Evolutionary network with 1 reticulation
## 
##                --- Base tree ---
## Phylogenetic tree with 3 tips and 4 internal nodes.
## 
## Tip labels:
## [1] "a" "b" "c"
## Node labels:
## [1] ""    ""    "#H1" ""   
## 
## Rooted; includes branch lengths.
```

```r
ggevonet(enet) + geom_tiplab() ## from ggnetworkx
```

![](notebook_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
plot(enet)  ## from ape
```

![](notebook_files/figure-html/unnamed-chunk-4-2.png)<!-- -->


Now, playing around, how do branch lengths affect:

```r
### just adding branch length to minor edge (#H1,c)
enet1 <- ape::read.evonet(text="((a:2,(b:1)#H1:1):1,(#H1:1,c:1):2);")
ggevonet(enet1) + geom_tiplab() ##nothing happens
```

![](notebook_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

```r
## apparently, you do need ultrametric networks, bc the following does not work
enet2 <- ape::read.evonet(text="((a:0.22,(b:0.4)#H1:0.3):0.12,(#H1,c:0.15):0.2);")
ggevonet(enet2) + geom_tiplab()
```

![](notebook_files/figure-html/unnamed-chunk-5-2.png)<!-- -->

```r
plot(enet2) ## but with ape it works
```

![](notebook_files/figure-html/unnamed-chunk-5-3.png)<!-- -->

Maybe not entirely ultrametric, but at least the donor and recipient of gene flow should have similar length:

```r
enet3 <- ape::read.evonet(text="((a:0.22,(b:0.4)#H1:0.3):0.12,(#H1,c:0.45):0.2);")
ggevonet(enet3) + geom_tiplab()
```

![](notebook_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

```r
plot(enet3)
```

![](notebook_files/figure-html/unnamed-chunk-6-2.png)<!-- -->

```r
plot(enet3, type="c")
```

![](notebook_files/figure-html/unnamed-chunk-6-3.png)<!-- -->

## ggtree

There is an option in `ggtree` to plot as cladogram: `branch.length="none"`, can we try?


```r
ggevonet(enet3,branch.length="none") + geom_tiplab()
```

![](notebook_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

But still does not work with the weird branches. The time starts at the root, and on one side there is `0.12+0.3=0.42`, but on the other side, there is `0.2+0.15=0.35`, so the tip `c` is drawn before the hybrid edge.
I believe that ultrametric should not be the default.


```r
ggevonet(enet2,branch.length="none") + geom_tiplab()
```

![](notebook_files/figure-html/unnamed-chunk-8-1.png)<!-- -->


```r
ggevonet(enet2,layout="slanted") + geom_tiplab()
```

![](notebook_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

Thus, we still need to figure out why this network is weird.

### Does it allow inheritance probs?

Yes, it does not break, but it does not matter if they don't add up to one:

```r
enet4 <- ape::read.evonet(text="((a:2,(b:1)#H1:1::0.99):1,(#H1:1::0.2,c:1):2);")
ggevonet(enet4) + geom_tiplab()
```

![](notebook_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
## Trying with bigger networks


```r
wnet <- ape::read.evonet(text="(Mno00,Bph03,(((Bmu,(Bbo01,Bbo02):9.41949690722463):1.6484045144986545,(Bac00,((balMys,Egl00):9.055782863234407)#H13:5.268095830633961::0.8331903627712247):1.100383484076166):0.066043783465627,((Erob02,Erob01):9.997600013276905,#H13:0.19346605673125924::0.16680963722877526):0.35164828246609625):2.146642742731017);")
ggevonet(wnet) + geom_tiplab()
```

```
## Warning in fortify.phylo(model, ladderize = ladderize): 'edge.length' contains NA values...
## ## setting 'edge.length' to NULL automatically when plotting the tree...
```

![](notebook_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

We get an error/warning that some NA values in `edge.length`.

We can compare to ape:

```r
plot(wnet)
```

```
## Warning in plot.phylo(x, ...): 10 branch length(s) NA(s): branch lengths
## ignored in the plot
```

![](notebook_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

The plot from ape looks weird because hybridization starts on internal nodes.

Now, if we try the `slanted` option, the hybrid edge still looks horizontal. This is different behavior to the plot of `enet2`.

```r
ggevonet(wnet, layout="slanted") + geom_tiplab()
```

```
## Warning in fortify.phylo(model, ladderize = ladderize): 'edge.length' contains NA values...
## ## setting 'edge.length' to NULL automatically when plotting the tree...
```

![](notebook_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

### Now more than one hybridization:


```r
fishnet <- ape::read.evonet(text="(Xalvarezi,Xmayae,((Xsignum,((Xmonticolus,(Xclemenciae_F2,#H25:::0.032485577051040423):0.979116879342896):1.4858786525152954,(((((((((Xgordoni,Xmeyeri):0.26111415598145865,Xcouchianus):3.5325554148283564,Xvariatus):0.6445452395880061,Xevelynae):0.41219807813960146,(Xxiphidium,#H24:0.0::0.16538333427567822):1.4020042304402076):0.2921320715527958,Xmilleri):0.4688709909949548,Xandersi):0.6492623942884177,Xmaculatus):1.0200542801748702,(((Xmontezumae,(Xcortezi,(Xbirchmanni_GARC,Xmalinche_CHIC2):0.8943004029517725):0.6903622798656042):0.4926275928330048,((Xnigrensis,Xmultilineatus):1.4508210422790195,(Xpygmaeus,Xcontinens):1.9686498919562234):2.428065080719157):2.0435103751744865)#H24:0.6476450483433508::0.8346166657243218):0.7816554661308311):1.9841381634366537):0.31614675520900004,(Xhellerii)#H25:::0.9675144229489596):0.28226883225049243);")
ggevonet(fishnet) + geom_tiplab()
```

```
## Warning in fortify.phylo(model, ladderize = ladderize): 'edge.length' contains NA values...
## ## setting 'edge.length' to NULL automatically when plotting the tree...
```

![](notebook_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

```r
plot(fishnet)
```

```
## Warning in plot.phylo(x, ...): 24 branch length(s) NA(s): branch lengths
## ignored in the plot
```

![](notebook_files/figure-html/unnamed-chunk-14-2.png)<!-- -->

Black hybrid edges look confusing as they appear as polytomies.

We can compare in the backgroung with julia plot:
```
using PhyloNetworks
using PhyloPlots # to visualize networks
using RCall      # to send additional commands to R like this: R"..."

net = readTopology("(Xalvarezi,Xmayae,((Xsignum,((Xmonticolus,(Xclemenciae_F2,#H25:::0.032485577051040423):0.979116879342896):1.4858786525152954,(((((((((Xgordoni,Xmeyeri):0.26111415598145865,Xcouchianus):3.5325554148283564,Xvariatus):0.6445452395880061,Xevelynae):0.41219807813960146,(Xxiphidium,#H24:0.0::0.16538333427567822):1.4020042304402076):0.2921320715527958,Xmilleri):0.4688709909949548,Xandersi):0.6492623942884177,Xmaculatus):1.0200542801748702,(((Xmontezumae,(Xcortezi,(Xbirchmanni_GARC,Xmalinche_CHIC2):0.8943004029517725):0.6903622798656042):0.4926275928330048,((Xnigrensis,Xmultilineatus):1.4508210422790195,(Xpygmaeus,Xcontinens):1.9686498919562234):2.428065080719157):2.0435103751744865)#H24:0.6476450483433508::0.8346166657243218):0.7816554661308311):1.9841381634366537):0.31614675520900004,(Xhellerii)#H25:::0.9675144229489596):0.28226883225049243);")

R"name = function(x) file.path('.', x)" # function to create file name in appropriate folder
R"pdf(name('snaqplot.pdf'), width=4, height=3)"
R"par"(mar=[0,0,0,0]) # to reduce margins (no margins at all here)
plot(net, :R, showGamma=true); # network is plotted & sent to file
R"dev.off()"; # wrap up and save image file

knitr::include_graphics("snaqplot.pdf")
```

