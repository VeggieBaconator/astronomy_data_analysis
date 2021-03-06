# astronomy-data-analysis
# Table of contents
1. [Introduction](#introduction)
    1. [Why is this work relevant?](#subparagraph1)
    2. [Libraries Used](#subparagraph2)
2. [Data, Catalogs, and Background Info](#paragraph1)
3. [Notable Algorithms](#notablealgorithms)
    1. [Cross-Matching](#crossmatching)
	2. [BinApprox](#binapprox)
	3. [Galaxy Classfication](#galaxyclass)
    	1. [Discussion and Challenges](#challenges)

## Introduction <a name="introduction"></a>
A small library to work with astronomy catalogs and prepare fits image files for analysis. Note that in general, more details will be in examples.ipynb.

Sample fits files for image_stacking.py and smaller verions of both star catalogs for cross-matching can be found in the sample_data folder!

This notebook will default to using the sample data folder.

### Why is this work relevant? <a name="subparagraph1"></a>
For astronomy, there is an incredible amount of noise from everything you could imagine. From the limitations of our cameras to tiny flucations in the atmosphere, our only way of interacting with our field of study is through images and simulations. Eliminating as much bias as possible and increasing the signal to noise ratio for our measurements is of the utmost importance.

Small errors in the data can lead to centuries long condundrums as our physics based simulations consistently don't match observations. Determining whether our understanding of phyiscs is wrong or if we're getting bad data is a very hard thing. Currently, the [Hubble Constant condundrum](https://www.aps.org/publications/apsnews/201805/hubble.cfm) is a huge debate in the astronomy community - our measurements of the Hubble constant don't match. Is the way we infer distance in astronomy systematically under or over estimating distances? Do we not understand the evolution of the universe? Or are our predictions from the Standard Model flawed? Or something else?

For those reasons, it is extremely important to ensure we have the best data processing methods possible. Like the majority of data scientists, most of an astronomer's time is spend processing data and ensuring statistically significant results.

One of the most universal things in astronomy research is collecting all relevant observations of the objects we want to study. These observations will come from every manner of data sources, each with their own formatting and, oftentimes, file type. Integrating these large catalogs of data into one cohesive source or, almost harder, cross-matching observations in these catalogs with their counterparts in other catalogs can be quite challenging and certainly time intensive. It's very common to have catalogs of over one million rows, so even finding the matches of a small object catalog in a larger one can take an extraordinarily long time if done naively.

### Libraries Used: <a name="subparagraph2"></a>
*	astropy
*	numpy
*	scipy
*	matplotlib
*	os
*	time

For easy install of all necessary libraries:
`pip install astropy numpy scipy matplotlib`

##  Data, Catalogs, and Background Info <a name="paragraph1"></a>
*	Full superCOSMOS catalog data can be found [here](http://ssa.roe.ac.uk/allSky) under the Downloads heading in the first .gz file.
*	Full BSS catalog data can be found [here](http://cdsarc.u-strasbg.fr/viz-bin/Cat?J/MNRAS/384/775) under the FTP heading in table2.dat.

For more info on the Equatorial coordinate system, see [this](https://en.wikipedia.org/wiki/Right_ascension#/media/File:Ra_and_dec_on_celestial_sphere.png) and [this](https://en.wikipedia.org/wiki/Equatorial_coordinate_system).

## Notable Algorithms <a name="notablealgorithms"></a>
### Cross matching <a name="crossmatching"></a>
It's harder than you might think to find the nearest neighbor for a point on a sphere. Most popular nearest neighbor algorithms rely on nice, flat Euclidean space and do not respect the "wrapping" of closed surfaces.

Here I use a two dimensional k-d tree to find nearest neighbors of catalog objects, with special modifications to account for the fact that our data lives on a sphere. More details are in examples.ipynb.

### Binapprox - Why was this hard? <a name="binapprox"></a>
- Median finding algorithms usually require loading the entire dataset into working memory. Obviously this poses problems for larger datasets. We typically want the median of a set of observations, since it is a [robust statistic](https://en.wikipedia.org/wiki/Robust_statistics). We typically deal with a lot of noise and bias in astronomy, from a stray gamma ray oversaturating a pixel to poor readout to atmospheric conditions.
- Implementing this numpythonically for image data.
- Binapprox is a median algorithm that is detailed [here](http://www.stat.cmu.edu/~ryantibs/median/).

### Galaxy Classification with Decision Trees <a name="galaxyclass"></a>
There is a LOT of data in astronomy. And there are a LOT of galaxies. The current classification system for galaxies is inherently a little bit arbitrary, since the line between two different galaxies just beginning to collide vs a merger galaxy (the result of colliding galaxies) is a continuous one. Similar problems at the boundary lines exist for the other categories as well. Here, we explore, clean, and analyze data from Galaxy Zoo for galaxy classification.

- Expert feature selection credit goes to Dr. Tara Murphy of the University of Sydney.
	- Features include:
    	- color (correlated with galaxy distance and type)
    	- eccentricity (shape)
    	- adaptive moments (shape)
    	- concentrations of light (also shape information)
- Achieves 87% accuracy with 10-fold cross validation

#### Discussion: What Challenges Remain? <a name="challenges"></a>
- Data was curated from galaxy zoo and only contains objects that were classified with 80% or more agreement amoung contributors. That will present an inherent bias. The spiral and elliptical galaxies included were 100% agreement. Merging galaxies are two or more galaxies that collided to form a new one. These mergers include everything from almost two separate galaxies just beginning to collide to a messy blob with bits of shredded galaxies haloing it.
- There is subjectiveness in the classification
- The features trained on are not the features used to classify the dataset (done by human eye).
- Features trained on will be influenced by redshift (how far away a galaxy is), but the visual classification will not be. There is going to be a limit to classifying galaxies by color and the other features used.
- TODO add references to the other papers using these color and shape-based features for the curious.

Misc TODO:
- clean up examples.ipynb
	- add more documentation to scalable algorithms section
- think about how to store star catalogs
- remove all .npy files, add python scripts to fetch and store data from SDSS instead
- finish photometric redshift regression cleanup to add to repo
- clean up and fill out this readme
- consider limitations of current galaxy classification model
	- visualize the misclassified points: could I expect a human to classify them?
	- use boosted decision trees in tensorflow instead