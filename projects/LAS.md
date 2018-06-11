---
layout: page
title: LAS - Anticipatory Thinking
---

## Project Overview

One key skill of a good analyst is the ability to engage in anticipatory thinking. Anticipatory thinking is our ability to anticipate the many possible outcomes of an event, or multiple paths that may connect to a desired outcome. This may seem similar to prediction on the surface, however, a key difference is that the success of anticipatory thinking is imagining the breadth of possible futures that can take place, while the success of a prediction is stating the most probable outcome. Anticipatory thinking can be used to create indicators of future scenarios of interest and identify conditioning events. In other words, prediction maximizes precision while anticipatory thinking maximizes recall. There are a number of analytical techniques used to help analysts engage in anticipatory thinking, such as alternative futures analysis or a red team analysis, however, there has been little to no validation of these techniques and the construct of anticipatory thinking remains poorly understood.

The team has developed a novel assessment for anticipatory thinking based on information gathered from expert analysts and previous literature on divergent thinking. The new assessment is currently being tested for construct validity through testing its relationship with factors that are hypothesized to be related (convergent validity) and unrelated (divergent validity) using Structural Equation Modeling.

## Methods

### Anticipatory Thinking Task

The anticipatory thinking task involves providing participants with a prompt for which they create a list of uncertainty and impact pairings. For example, for the prompt "How will smarthome technology influencing the aging population in 30 years" an uncertainty could be the development of cheap sensor technology, with an impact of improving the speed of medical assistance following a fall. Participants were given a series of examples and then a practice prompt in order to learn the task. Then the participant completed two full-length scenarios in which they were given 10 minutes to generate as many uncertainty and impacts as possible.

This data was then rated by three different coders in terms of the specificity, uniqueness, and novelty of each response. The top two response per participant were aggregated across raters and produced a single score for the task. Only the top two responses were used in order to prevent punishing the participant with generating many uninteresting responses.

### Data Collection

The survey was administered using Amazon's Mechanical Turk with a total of 210 participants.

### Constructs

| Scale | Subscale | Description |
| ---------------- | -------- | ------------------------------------------------------ |
| Need for Cognition | - | Individual desire for cognitive processes.
| Verbal Reasoning | - | Often highly correlated with intelligence, this is the ability to engage grammatical reasoning.
| Need for Close |  -  | The need for cognitive closure in well one deals with ambiguous solutions.    
|   | Order | Preference for order and structure.
|   | Predict | Preference for predictable, reliable information.
|   | Decisive | Tendency to act quickly rather than cautiously.
|   | Ambiguity | Comfort with ambiguous situations.
|   | Closed-minded | General unwillingness to be challenged by other opinions or evidence.
| Mindfulness | - | One's ability to maintain their attention to the present moment.   
|    | Observe |  Actively attending to or noticing one's external or internal environment.
|    | Describe | Ability to describe one's experiences.
|    | Act | Actively being a part of one's current environment, rather than being on "autopilot."
|    | Nonjudge | Not being judgemental of the content of one's thoughts/experiences.
|    | Nonreact | Ability to detach from one's thoughts or emotions and act in a collected manner.

## Exploratory Data Analysis

Data cleaning and analysis are currently underway. The data displayed here is only from one of the two anticipatory thinking tasks (smart home), and results may change when the full dataset is available. Before testing the measurement model, all items were plotted in order to identify any bad items and whether the scale displayed sufficient variance. Only the Order subscale for the Need for Cognition construct is displayed here for brevity.

### Need for Cognition

All items appeared well-behaved, with reasonable variance and no clear outliers.  

<img src="/projects/img/LAS_orderplot.png" width = '100%' class = "center" style="padding:10px"/>

### Correlogram

Constructs were aggregated into single scores per participant in order to investigate general patterns of correlation and spread. Diversity appears to be slightly bimodal, and mindfulness is skewed with some ceiling effects present.

<img src="/projects/img/LAS_correlation.png" width = '100%' class = "center" style="padding:10px"/>

### TSNE: Anticipatory Thinking Clustering

The TSNE was used to search for nonlinear clustering of the anticipatory thinking measures. No clear patterns emerged, supporting the use of analytical techniques assuming a hypersphere.

<img src="/projects/img/LAS_tsne.png" width = '100%' class = "center" style="padding:10px"/>
