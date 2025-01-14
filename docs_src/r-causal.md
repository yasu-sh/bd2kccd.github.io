# r-causal
[R Wrapper](https://github.com/bd2kccd/r-causal) for Tetrad Library

Note 2023-03-06: This version of RCausal uses an older version of Tetrad from at least 5 years ago. However, we have updated our Python integration to a much better version--see <https://github.com/cmu-phil/py-tetrad>. Updating our R integration is one of the next projects we will take up.

News 2023-04-05: We have put forward a proposal to replace the r-causal functionality using the py-tetrad functionality, here: <https://github.com/cmu-phil/py-tetrad/tree/main/pytetrad/R>. The installation procedure for this is still somewhat complicated, and we will try to simplify it. If you try it and and have difficulties, please let us know. Once you have it installed, it is very easy and intuitive to use.

By the way, rcausal has not been maintained for some time now, as the tireless maintainer has since moved on to different work :-)... but going back through some of the issues posted for r-causal gives some hints as to additional functionality that pytetrad/R should have. We'll try to get caught up.

## R Library Requirement
R >= 3.3.0, 
[stringr](https://cran.r-project.org/web/packages/stringr/),
[rJava](https://cran.r-project.org/web/packages/rJava/index.html), 

## Docker
As an alternative to installing the library and getting rJava working with your installation (i.e., does not work well on mac) we have a [Docker image](https://hub.docker.com/r/chirayukong/r-causal-rstudio/)

## Installation

- Install the R library requirements:
```R
install.packages("stringr")
install.packages("rJava")
```
- Install r-causal from github:

```R
library(devtools)
install_github("bd2kccd/r-causal")
```

## Example
### Continuous Dataset
```R
library(rcausal)
data("charity")   #Load the charity dataset

tetradrunner.getAlgorithmDescription(algoId = 'fges')
tetradrunner.getAlgorithmParameters(algoId = 'fges',scoreId = 'fisher-z')
#Compute FGES search
tetradrunner <- tetradrunner(algoId = 'fges',df = charity,scoreId = 'fisher-z',
dataType = 'continuous',alpha=0.1,faithfulnessAssumed=TRUE,maxDegree=-1,verbose=TRUE)

tetradrunner$nodes #Show the result's nodes
tetradrunner$edges #Show the result's edges
```
### Discrete Dataset
```R
library(rcausal)
data("audiology")    #Load the charity dataset

tetradrunner.getAlgorithmParameters(algoId = 'fges',scoreId = 'bdeu')
#Compute FGES search
tetradrunner <- tetradrunner(algoId = 'fges',df = audiology,scoreId = 'bdeu',dataType = 'discrete',
alpha=0.1,faithfulnessAssumed=TRUE,maxDegree=-1,verbose=TRUE)

tetradrunner$nodes #Show the result's nodes
tetradrunner$edges #Show the result's edges
```

### Prior Knowledge

#### Create PriorKnowledge Object
```R
forbid <- list(c('TangibilityCondition','Impact')) # List of forbidden directed edges
require <- list(c('Sympathy','TangibilityCondition')) # List of required directed edges
forbiddenWithin <- c('TangibilityCondition','Imaginability')
class(forbiddenWithin) <- 'forbiddenWithin' # Make this tier forbidden within
temporal <- list(forbiddenWithin, c('Sympathy','AmountDonated'),c('Impact')) # List of temporal node tiers
prior <- priorKnowledge(forbiddirect = forbid, requiredirect = require, addtemporal = temporal)
fgs <- fgs(df = charity, penaltydiscount = 2, depth = -1, ignoreLinearDependence = TRUE, 
heuristicSpeedup = TRUE, numOfThreads = 2, verbose = TRUE, priorKnowledge = prior)
```

#### Load Knowledge File
```R
# knowledge file: audiology.prior
# /knowledge
# forbiddirect
# class tymp
# class age_gt_60
# class notch_at_4k
# 
# requiredirect
# history_noise class
#
# addtemporal
# 0* bser late_wave_poor tymp notch_at_4k o_ar_c ar_c airBoneGap air bone o_ar_u airBoneGap
# 1 history_noise history_dizziness history_buzzing history_roaring history_recruitment history_fluctuating history_heredity history_nausea
# 2 class

prior <- priorKnowledgeFromFile('audiology.prior')
fgs.discrete <- fgs.discrete(df=audiology,structurePrior=1.0,samplePrior=1.0, 
depth = -1, heuristicSpeedup = TRUE, numOfThreads = 2,verbose = TRUE, priorKnowledge = prior)
```

### Plot a DOT graph
```R
library(DOT)
graph_dot <- tetradrunner.tetradGraphToDot(tetradrunner$graph)
dot(graph_dot)
```

## Useful `rJava` Trouble-shooting Installation in Mac OS X Links

1. [http://stackoverflow.com/questions/26948777/how-can-i-make-rjava-use-the-newer-version-of-java-on-osx/32544358#32544358](http://stackoverflow.com/questions/26948777/how-can-i-make-rjava-use-the-newer-version-of-java-on-osx/32544358#32544358)
2. [http://andrewgoldstone.com/blog/2015/02/03/rjava/](http://andrewgoldstone.com/blog/2015/02/03/rjava/)
