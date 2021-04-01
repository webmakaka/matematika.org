---
layout: page
title: Building Recommender Systems with Machine Learning and AI
description: Building Recommender Systems with Machine Learning and AI
keywords: Video Courses, Data Science
permalink: /videos/ds/ml/building-recommender-systems-with-machine-learning-and-ai/en/
---

# Building Recommender Systems with Machine Learning and AI

https://sundog-education.com/recsys/

Установили anaconda

https://www.anaconda.com/

    $ conda install -c anaconda anaconda-navigator
    $ conda install -c conda-forge scikit-surprise
    $ anaconda-navigator

<br/>

### Запускаем Spyder

    $ anaconda-navigator > Spyder

http://media.sundog-soft.com/RecSys/RecSys-Materials.zip

http://media.sundog-soft.com/RecSys/ml-latest-small.zip

Разархивизовать каталоги и ml-latest-small поместить в RecSys-Materials

    GettingStarted/GettingStarted.py

<br/>

### 03 Evaluating Recommender Systems

    RUN --> Evaluating/TestMetrics.py

<br/>

### 04 A Recommender Engine Framework

SVD - Singular Value Decomposition

    RUN --> Framework/RecsBakeOff.py

```

Loading movie ratings...

Computing movie popularity ranks so we can measure novelty later...
Estimating biases using als...
Computing the cosine similarity matrix...
Done computing similarity matrix.
Evaluating  SVD ...
Evaluating accuracy...
Evaluating top-N with leave-one-out...
Computing hit-rate and rank metrics...
Computing recommendations with full data set...
Analyzing coverage, diversity, and novelty...
Computing the cosine similarity matrix...
Done computing similarity matrix.
Analysis complete.
Evaluating  Random ...
Evaluating accuracy...
Evaluating top-N with leave-one-out...
Computing hit-rate and rank metrics...
Computing recommendations with full data set...
Analyzing coverage, diversity, and novelty...
Computing the cosine similarity matrix...
Done computing similarity matrix.
Analysis complete.


Algorithm  RMSE       MAE        HR         cHR        ARHR       Coverage   Diversity  Novelty
SVD        0.9034     0.6978     0.0298     0.0298     0.0112     0.9553     0.0445     491.5768
Random     1.4385     1.1478     0.0089     0.0089     0.0015     1.0000     0.0719     557.8365

Legend:

RMSE:      Root Mean Squared Error. Lower values mean better accuracy.
MAE:       Mean Absolute Error. Lower values mean better accuracy.
HR:        Hit Rate; how often we are able to recommend a left-out rating. Higher is better.
cHR:       Cumulative Hit Rate; hit rate, confined to ratings above a certain threshold. Higher is better.
ARHR:      Average Reciprocal Hit Rank - Hit rate that takes the ranking into account. Higher is better.
Coverage:  Ratio of users for whom recommendations above a certain threshold exist. Higher is better.
Diversity: 1-S, where S is the average similarity score between every possible pair of recommendations
           for a given user. Higher means more diverse.
Novelty:   Average popularity rank of recommended items. Higher means more novel.
```

<br/>

### 05 Content-Based Filtering

    RUN --> ContentBased/ContentRecs.py

```
Loading movie ratings...

Computing movie popularity ranks so we can measure novelty later...
Estimating biases using als...
Computing the cosine similarity matrix...
Done computing similarity matrix.
Evaluating  ContentKNN ...
Evaluating accuracy...
Computing content-based similarity matrix...
0  of  8211
100  of  8211
200  of  8211
300  of  8211
400  of  8211
500  of  8211
600  of  8211
700  of  8211
800  of  8211
900  of  8211
1000  of  8211
1100  of  8211
1200  of  8211
1300  of  8211
1400  of  8211
1500  of  8211
1600  of  8211
1700  of  8211
1800  of  8211
1900  of  8211
2000  of  8211
2100  of  8211
2200  of  8211
2300  of  8211
2400  of  8211
2500  of  8211
2600  of  8211
2700  of  8211
2800  of  8211
2900  of  8211
3000  of  8211
3100  of  8211
3200  of  8211
3300  of  8211
3400  of  8211
3500  of  8211
3600  of  8211
3700  of  8211
3800  of  8211
3900  of  8211
4000  of  8211
4100  of  8211
4200  of  8211
4300  of  8211
4400  of  8211
4500  of  8211
4600  of  8211
4700  of  8211
4800  of  8211
4900  of  8211
5000  of  8211
5100  of  8211
5200  of  8211
5300  of  8211
5400  of  8211
5500  of  8211
5600  of  8211
5700  of  8211
5800  of  8211
5900  of  8211
6000  of  8211
6100  of  8211
6200  of  8211
6300  of  8211
6400  of  8211
6500  of  8211
6600  of  8211
6700  of  8211
6800  of  8211
6900  of  8211
7000  of  8211
7100  of  8211
7200  of  8211
7300  of  8211
7400  of  8211
7500  of  8211
7600  of  8211
7700  of  8211
7800  of  8211
7900  of  8211
8000  of  8211
8100  of  8211
8200  of  8211
...done.
Analysis complete.
Evaluating  Random ...
Evaluating accuracy...
Analysis complete.


Algorithm  RMSE       MAE
ContentKNN 0.9375     0.7263
Random     1.4385     1.1478

Legend:

RMSE:      Root Mean Squared Error. Lower values mean better accuracy.
MAE:       Mean Absolute Error. Lower values mean better accuracy.

Using recommender  ContentKNN

Building recommendation model...
Computing content-based similarity matrix...
0  of  9066
100  of  9066
200  of  9066
300  of  9066
400  of  9066
500  of  9066
600  of  9066
700  of  9066
800  of  9066
900  of  9066
1000  of  9066
1100  of  9066
1200  of  9066
1300  of  9066
1400  of  9066
1500  of  9066
1600  of  9066
1700  of  9066
1800  of  9066
1900  of  9066
2000  of  9066
2100  of  9066
2200  of  9066
2300  of  9066
2400  of  9066
2500  of  9066
2600  of  9066
2700  of  9066
2800  of  9066
2900  of  9066
3000  of  9066
3100  of  9066
3200  of  9066
3300  of  9066
3400  of  9066
3500  of  9066
3600  of  9066
3700  of  9066
3800  of  9066
3900  of  9066
4000  of  9066
4100  of  9066
4200  of  9066
4300  of  9066
4400  of  9066
4500  of  9066
4600  of  9066
4700  of  9066
4800  of  9066
4900  of  9066
5000  of  9066
5100  of  9066
5200  of  9066
5300  of  9066
5400  of  9066
5500  of  9066
5600  of  9066
5700  of  9066
5800  of  9066
5900  of  9066
6000  of  9066
6100  of  9066
6200  of  9066
6300  of  9066
6400  of  9066
6500  of  9066
6600  of  9066
6700  of  9066
6800  of  9066
6900  of  9066
7000  of  9066
7100  of  9066
7200  of  9066
7300  of  9066
7400  of  9066
7500  of  9066
7600  of  9066
7700  of  9066
7800  of  9066
7900  of  9066
8000  of  9066
8100  of  9066
8200  of  9066
8300  of  9066
8400  of  9066
8500  of  9066
8600  of  9066
8700  of  9066
8800  of  9066
8900  of  9066
9000  of  9066
...done.
Computing recommendations...

We recommend:
Presidio, The (1988) 3.841314676872932
Femme Nikita, La (Nikita) (1990) 3.839613347087336
Wyatt Earp (1994) 3.8125061475551796
Shooter, The (1997) 3.8125061475551796
Bad Girls (1994) 3.8125061475551796
The Hateful Eight (2015) 3.812506147555179
True Grit (2010) 3.812506147555179
Open Range (2003) 3.812506147555179
Big Easy, The (1987) 3.7835412549266985
Point Break (1991) 3.764158410102279

Using recommender  Random

Building recommendation model...
Computing recommendations...

We recommend:
Sleepers (1996) 5
Beavis and Butt-Head Do America (1996) 5
Fear and Loathing in Las Vegas (1998) 5
Happiness (1998) 5
Summer of Sam (1999) 5
Bowling for Columbine (2002) 5
Babe (1995) 5
Birdcage, The (1996) 5
Carlito's Way (1993) 5
Wizard of Oz, The (1939) 5
```
