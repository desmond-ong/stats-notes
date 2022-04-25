--- 
title: "Statistics and Analytics for the Social and Computing Sciences"
author: "Desmond C. Ong"
date: "Draft Dated: 2022-04-25"
site: bookdown::bookdown_site
output: 
  bookdown::gitbook:
    lib_dir: assets
    split_by: section
    css: "css/style.css"
    config:
      toolbar:
        position: static
      toc:
        before: |
          <li><a href="./">Statistics and Analytics for the Social and Computing Sciences</a></li>
        after: |
          <li><a href="https://github.com/rstudio/bookdown" target="blank">Published with bookdown</a></li>
documentclass: book
bibliography: [stats-references.bib]
biblio-style: apalike
link-citations: yes
github-repo: desmond-ong/stats-notes
description: "Statistics and Analytics for the Social and Computing Sciences"
---

# Preface {-}

This is a working collection of notes on statistics and data analytics that I am compiling, with two goals:

1) To serve as a supplement to the courses I teach. This will be targeted at aspiring data scientists!

2) To discuss material that would be useful for graduate-level researchers in the social and computing sciences.

I hope to cover several important and useful statistical tools (such as multivariate regression and simulations), as well as discuss issues like data visualization best practices. I also plan to write several chapters on applying statistics in the computing sciences (for example, proper statistics when analyzing machine learning models). And finally, if I have time, I would like to transition to teaching statistics in a more Bayesian tradition.


My philosophy in teaching statistics and analytics is to focus on helping students to achieve a conceptual understanding, and develop their own intuition for data. Yes, students will need some mathematical background to appreciate statistics, and yes, students will need to learn some programming (in R) to actually implement modern statistical calculations, but these are means to an end. The end is an appreciation of data, and especially, how data exists in the real world. Real data rarely conforms to the assumptions we make in our analyses. The job of the analyst is to understand the data, which involves "troubleshooting" confusing statistical output, modifying statistical models and their underlying assumptions, and perhaps even inventing new ones.


As some background, I am a computational cognitive psychologist, so I tend to favor regression and simulation approaches, and my examples may default to examples common in the social sciences. 


This is a work-in-progress that is inspired by Russ Poldrack's Psych10 book here: http://statsthinking21.org/, which is another undergraduate Introduction to Statistics course. This set of notes is hosted on [GitHub](https://github.com/desmond-ong/stats-notes) and built using [Bookdown](https://github.com/rstudio/bookdown).


Feedback can be sent to dco (at) comp (dot) nus (dot) edu (dot) sg. 


This material is shared under a [Creative Commons Attribution Share Alike 4.0 International (CC-BY-SA-4.0) License](https://creativecommons.org/licenses/by-sa/4.0/). What this means is that you are free to copy, redistribute, and even adapt the material in this book, in any format or for any purpose, even commercial. Basically, this is a freely-available educational resource that you can share and use. The only conditions are (i) you must give appropriate credit, and if you made any changes, you must indicate so and not in any way that suggests that I endorse your changes, and (ii) if you transform or build upon the material here, you must also distribute this contributions under the same license.


#### About the Author {-}

[Desmond Ong](https://desmond-ong.github.io/) is an Assistant Professor in the Department of Information Systems and Analytics, in the School of Computing at the National University of Singapore. He also holds a concurrent appointment as a Research Scientist at the Institute of High Performance Computing (IHPC), A\*STAR.

Professor Ong received his Ph.D. in Psychology and his M.Sc. in Computer Science from Stanford University, and his undergraduate degrees in Economics and Physics from Cornell University.



## Outline of notes {-}

- Getting Started
    a. R, RStudio, R Markdown 
    b. Coding Best Practices
- Introduction
- Handling Data
    a. Basics of Data Wrangling
    b. Wrangling in the tidyverse
    c. A data cleaning pipeline for research projects
- <i>[In Progress] Descriptive Statistics <!-- -  Mean / Median / Mode, Types of Variables, Variance / Covariance / Correlation--></i> 
- <i>[In Progress] Data Visualization</i>
- The Linear Model 
    a. Linear Regression
    b. Logistic Regression
    c. <i>[In Progress] Interactions </i>
    d. <i>[In Progress] Model Selection </i>
    e. <i>[In Progress] Mixed-effects linear models </i>
- <i>[In Progress] A discussion on probabilities and probability distributions </i>
- <i>[In Progress] Simulations (I) </i>
    a. <i>Monte Carlo Simulations </i>
    b. <i>The Bootstrap </i>
- <i>[In Progress] Simulations (II): Statistics in Machine Learning </i>
    a. <i>Understanding the statistics of machine learning models </i>
- <i>[In Progress] Data Mining </i>
- Introduction to Time Series Data
    a. Time Series Basics
    b. Smoothing-based models
    c. Time-Series Regression
- Optimization 
    a. Linear Optimization 
    b. Integer-valued Optimization


...
