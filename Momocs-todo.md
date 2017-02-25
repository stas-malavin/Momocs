THINGS TO DISCUSS
=================

- Why separate function for everything?? So difficult to remember, looks odd.

- StereoMorph, shiny, own digitizer for Momocs

- ... argument in functions using basic procedures

- "coo_" functions: coo_ should be in the object's part, if implementing object-oriented programming


Bugs
====

plotting functions (probably 'panel' or 'stack') alternate mar parameter
------------------------------------------------------------------------

'mutate' doesn't work with objects where 'fac' is initially absent
------------------------------------------------------------------

Rostrum <- importOneCurveSM('SHAPES/He/', 'Front', scaled = T) %>% Opn %>%
  coo_sample(100) %>% coo_bookstein(1, 100)
mutate(Rostrum, Sp = as.factor(sub('\\d', '', names(Rostrum))))
# Error: wrong result size (58), expected 0 or 1

Rostrum <- importOneCurveSM('SHAPES/He/', 'Front', scaled = T) %>%
	Opn(fac = 1:58) %>%
  coo_sample(100) %>% coo_bookstein(1,100)
mutate(Rostrum, Sp = as.factor(sub('\\d', '', names(Rostrum))))
# An Opn object with: 
# --------------------
#	 - $coo: 58 open outlines (100 +/- 0 coordinates)
#  - $fac: 2 classifiers:
#	     'Opn.fac' (numeric): mean: 29.5, sd: 16.9.
#			      'Sp' (factor 11): AES, CAS, CHU, DIL, DYL, KER, KRI, LAC, LAP, OST, TYP.


'mshapes.Coo' implicitely considers nb.pts = 120
------------------------------------------------

This causes error if an object contains less than 120 points in each outline:
# Error in coo_sample.default(X[[i]], ...) : 
#  less coordinates than n, try coo_interpolate

In the function's description nothing is explained, though it's said about OpnCoe:
mshapes(x, fac, FUN = mean, nb.pts = 120, ...)


char matrix -> Ldk -> Ldk object with CHAR MATRICES inside (no error) -> panel -> error
PCA.LdkCoe doesn't work (prcomp doesn't understand lists)

A1.pca <- prcomp(A1.M)
		A1.pca %<>% as.PCA
		plot(A1.pca, ~Sp, morphospace = F,
     ellipses = T, ellipsesax = F, conf.ellipses = .95)
		Error: evaluation nested too deeply: infinite recursion / options(expressions=)?
		Error during wrapup: evaluation nested too deeply: infinite recursion / options(expressions=)?

> panel(Opn, ~Sp)
Error in rep(cols[1], length(Coo)) : 
  attempt to replicate an object of type 'language'

in plot.PCA, shp.pos='xy' and other don't always work. Usually shapes are just ploted all over the plot. A good way would be to make another variant: a mean shape of the group, plotted near the group label.

When morphospace is plotted the canvas of the plot moves (blank space on two margins occurs)

In Opn (and probably Out) when fac length is less than coo, no error returned. It's sometimes returned later in calls like opoly.

Some examples in mshapes work with warnings (in tps_arr)

bug in tFourier ("Error in `colnames<-`(`*tmp*`, value = c("x", "y")) : 
  attempt to set 'colnames' on an object with less than two dimensions")
	Function works only with individual shapes, not with the Coo in whole.
	Actually, it doesn't work at all.

Structure of Ldk object incorrectly represented in RStudio's Environment window

coo_sample (and probably other coo utilities) when applied to the Coo, Opn, or Out objects doesn't return the object of corresponding class, but just a list

OOP
===

mshapes uses classes OutCoe and OpnCoe. Are they different from Out and Opn?

plot, points, text, ordiplot, other...

ggplot (if it's needed at all...)

pca: use princomp (?) -- allows to deal with corr or vcv matrices. Maybe other solutions?

aov, lm, manova

methods::show

c.Coo method (instead of combine() )

predict method : efourier_i, efourier_shape

summary.MANOVA inconsistent with summary.manova. Also '$Residuals' instead of '$residuals'.

coo_draw and panel for mean shapes (mshapes result)

Out|Coo class can be subsetted with [number], but not with [logical]


Typos
=====

- coo_sample

- coo_slice

- coo_ldk

- coo_excentricityeigen

- coo_oscillo

- coo_perimpts

- coo_rev

- eFourier (integer whether to..., no point after class name) and other typos

- eFourier_norm

- plot.PCA argument is called pos.shp in man, whereas it's pos.shapes really
mshapes


FUSE
====

Reduce the number of fuctions, make sructure of the package more consistent:
coo_rotate and coo_rotatecenter
coo_smooth and coo_smoothcurve
many other such cases
all functions with x and y


TODO
====

plot.PCA: @param loadings.mult scalar multiplication factor for loadings' arrows
make it automatic like in vegan

add [ and [<- methods to Coo, Coe, and other objects

[think of] implementation of shape representation on PCA axes so that it represents the most distant deviations from the mean shape by just this particular axis holding all others constant (this would allow to more easily enterpreting the axis)

add ... to PCA etc. Ex.: A1.pca <- A1.M %>% PCA(na.action = na.omit)

add possibility to include names of measurements in 'measure'

add 'na.omit' and 'na.exclude' methods for TraCoe objects

rewrite coo_theta3() so that it accepts numbers of points (like d() ) and operates on Coo classes

panel and plot don't work properly with par(mfrow = ...)

other dimension reducing techniques like nMDS

str.Coo : not so many rows in $coo (truncate earlier)

MANOVA doesn't return fitted values and residuals!

improve message in MANOVA

Improve descriptions of the functions. Description shouldn't be the continuation of the title.

coo_alignminradius closes the shape

stack problem (is it a method of stack for Coo objects or a separate function?)

mess with autocompletion of Coo lists

make coo_rotate (and many others) work with parts of Coo objects

remove . from the title of the plot (because of magrittr)

In "Out(Ldk, fac = ...)" fac doesn't work

str(Coo) shows other names than e.g. RSudio -- what for??

l2a etc. should work with the OBJECT, not OBJECT$coo!

in 'panel' 'fac=' should be the second argument, and the formula argument should be possible

Is $fac field really needed as a part of the Coo object?..

in Opn(x, fac) fac data.frame should consist of factors, not char vectors. In such case nothing happens, but then plot(..., ~fac) doesn't plot any factors, without any warning or so.

After applying 'filter' print method on Coo still shows all levels in the factor used for filtering, although some levels are empty

Function to correctly exclude one or more configurations from Coo object is needed (expecially if $fac is present!)

Apply panel to EVERYTHING!!!

mshapes should work with "native" coordinates also, not only transformed ones

Automatically remove non-complete rows, with a warning:
#> plot(He.front.dfour.pca, ~Sex, morpho = T, pos.shp = 'range_axes',
#+ ellipses = T, ellipsesax = T)
Error in svd(var(coo)) : infinite or missing values in 'x'

HELP
====

All generic functions working with main objects. ], ] <-, names, etc.
