--- 
title: "Statistics and Analytics for the Social and Computing Sciences"
author: "Desmond C. Ong"
date: "Draft Dated: 2020-06-17"
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

This is a collection of notes on statistics and data analytics that I am compiling, with two goals:

1) To serve as a supplement to a course that I teach at the National University of Singapore (BT1101: Introduction to Business Analytics), which is a statistics course in R targeted at first-year undergraduate students who are aspiring data scientists. These sections will cover introductory material, and will be marked with <span class="badge badge-bt"> BT1101 </span>. 

2) To discuss material that would be useful for graduate-level researchers in the social and computing sciences. This material will build upon the introductory level, and will be marked with <span class="badge badge-adv"> Advanced </span>


I hope to cover quite broadly several important and useful statistical tools (e.g. my top priorities right now are to cover multivariate regression and simulations), as well as discuss issues like data visualization best practices. I also plan to write several chapters on applying statistics in the computing sciences (for example, proper statistics when analyzing machine learning models). And finally, if I have time, I would like to transition to teaching statistics in a more Bayesian tradition.



As some background, I am a computational cognitive psychologist, with a little bit of training in econometrics, so I tend to favor regression and simulation approaches, and my examples may default to examples common in the social sciences. 



> Disclaimer: For students taking BT1101, please refer to these notes **only if you are taking this course under me**. If you are taking the course under a different instructor, that instructor's lecture notes take precedence as to whether something is in syllabus or not (and hence, testable on assessments/exams). We are always making improvements to the syllabus, and so for different offerings of the course, instructors may cover slightly different material. So if you are taking it under a different instructor, do not assume that concepts covered here will show up on the exam, or assume that concepts not covered here will not show up on the exam. I've indicated sections that were covered the last time I taught BT1101 with a <span class="badge badge-bt"> BT1101 </span> label).



This is a work in progress that is inspired by Russ Poldrack's Psych10 book here: http://statsthinking21.org/, which is another undergraduate Introduction to Statistics course. This set of notes is hosted on [GitHub](https://github.com/desmond-ong/stats-notes) and built using [Bookdown](https://github.com/rstudio/bookdown).


Feedback can be sent to dco (at) comp (dot) nus (dot) edu (dot) sg. 


This material is shared under a [Creative Commons Attribution Share Alike 4.0 International (CC-BY-SA-4.0) License](https://creativecommons.org/licenses/by-sa/4.0/). What this means is that you are free to copy, redistribute, and even adapt the material in this book, in any format or for any purpose, even commercial. Basically, this is a freely-available educational resource that you can share and use. The only conditions are (i) you must give appropriate credit, and if you made any changes, you must indicate so and not in any way that suggests that I endorse your changes, and (ii) if you transform or build upon the material here, you must also distribute this contributions under the same license.


#### About the Author {-}

[Desmond Ong](https://desmond-ong.github.io/) is an Assistant Professor in the Department of Information Systems and Analytics, in the School of Computing at the National University of Singapore. He also holds a concurrent appointment as a Research Scientist at the Institute of High Performance Computing (IHPC), A\*STAR.

Professor Ong received his Ph.D. in Psychology and his M.Sc. in Computer Science from Stanford University, and his undergraduate degrees in Economics and Physics from Cornell University.



## Outline of notes {-}

- Getting Started - R, RStudio, R Markdown. 
    a. Coding Best Practices. 
    b. Analysis Best Practices.
- [In Progress] Introduction - detective analogy
- Handling Data
    a. Basics of Data Wrangling <span class="badge badge-bt"> BT1101 </span>
    b. Wrangling in the tidyverse <span class="badge badge-bt"> BT1101 </span>
    c. [In Progress] A data cleaning pipeline for research projects <span class="badge badge-adv"> Advanced </span>
- [In Progress] Descriptive Statistics - Mean / Median / Mode, Types of Variables, Variance / Covariance / Correlation
- [In Progress] Probability ? - Normal distribution?
- [In Progress] Data Visualization
- The Linear Model 
    a. Linear Regression <span class="badge badge-bt"> BT1101 </span>
    b. Logistic Regression <span class="badge badge-bt"> BT1101 </span>
    c. [In Progress] Interactions <span class="badge badge-bt"> BT1101 </span>
    d. [In Progress] Model Selection <span class="badge badge-bt"> BT1101 </span>
    e. [In Progress] Mixed-effects linear models <span class="badge badge-adv"> Advanced </span>
- [In Progress] Simulations (I)
    a. Monte Carlo Simulations <span class="badge badge-bt"> BT1101 </span>
    b. The Bootstrap <span class="badge badge-adv"> Advanced </span>
- [In Progress] Simulations (II): Statistics in Machine Learning 
    a. Understanding the statistics of machine learning models <span class="badge badge-adv"> Advanced </span>
- [In Progress] Data Mining
- Optimization
    a. Linear Optimization <span class="badge badge-bt"> BT1101 </span>
    b. [In Progress] Integer-valued Optimization <span class="badge badge-bt"> BT1101 </span>


...
