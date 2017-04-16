---
layout: page
title: SAS - Improving Learnability of Technical Documentation
---

## Project Overview

The purpose of the project was to increase the number of SAS “power-users” through improving the online support documentation. The backwards compatibility of SAS syntax prevents changes to the structure of the software, requiring improvements to the usability of the software to be either structural or related to the documentation. The high cost of technical writing for the scale of this project was taken into consideration when proposing modifications to the current design. In order to determine what the limiting design element was in the documentation the content, readability, and structure of the information was assessed. The results were used to propose a modification, then tested compared to the original design for usability.

### Naturalistic Observation

Participants unfamiliar with the software were told to perform a series of simple tasks in SAS using any online sources they wanted. A think-aloud procedure was used along with usability questions on the perceived difficulty of the tasks, and the issues they ran in. The two notable results were that the majority of users described the SAS support documentation as overwhelming, and looked at it only briefly (6-10s) before switching to other sources. This suggested that the content was not the issue and that structural modifications were needed to make the information appear more accessible.

### Readability Assessment

The structure of the website appeared to be the limiting agent, however this could be due to either the information architecture, the legibility, or the readability of the text. Comparisons were made for the typeface, font, and spacing of the site compared to the most used source (UCLA idre) during observation with limited differences found. Readability was measured using multiple tools (Flesch reading ease, Flesch-Kincaid grade level, Gunning-Fog, Coleman-Loau, and SMOG index) which were compared internally and externally to the second and third most popular information sources on Google searches. The pages compared were either examples of a particular situation, or overviews for a procedure. The SAS overview was significantly less readable than its counterparts, however there was no significant difference between the examples. Most participants in the observation searched for specific examples on their task, and so readability was dropped as a factor, leaving the information architecture to likely be the main issue.

<img src="/projects/img/sas_read1.png" width="600" style="padding:20px"/>

### Heuristic Evaluation

A heuristic evaluation was then conducted on the SAS statistical documentation based on Gerhardt-Powal's cognitive engineering principles. A short summary of the main results of the heuristic evaluation are summarized in the table below.

| Principle  | Primary Finding |
| ------------- | ------------- |
| Automate unwanted workload  | Explanations for syntax should be on the syntax page |
| Reduce uncertainty  | Not clear which syntax is required for procs |
| Fuse data  | Provide links when advising information in a different location |
| Present new information with meaning aids  | Describe new information when introduced, not after |
| Practice judicious redundancy | Often needed information is provided elsewhere, instead of being repeated where needed |

### Literature Review

The information from the naturalistic observation, readability assessment, and heuristic evaluation were used to conduct a literature review. This literature review was meant to display a number of advised changes, their theoretical backing, and how they would be implemented. This information was used to discuss the next direction with the SAS personnel. 

| Document Element  | Description | Application | Source |
| ------------- | ------------- | -------------- | ------- |
| Information Type | Different information types; procedural and declarative. Separation allows users to seek desired information. | Currently mostly declarative. Include a page with simplistic information for procedural learners. | Wright, 1983 |
| Common Issues | Unnecessary technical detail, complex instructions, few examples. | Have a general overview with simple language in addition to the detailed version. | Guillemette, 1989 |
| Examples | Large number of examples in accessible format improved learning. | Provide more examples. | Tilbury and Messner, 1997 |
| Guided vs. Unguided | Novices prefer worked examples and guided learning, experts want unguided. | Have some examples detailed step-by-step, some simply annotated. | Kirschner, Sweller, and Clark, 2006 |
| Visual Clutter | Visual clutter can cause decreased recognition and masking decreasing search performance. | Menu display local location rather than global links. | Rosenholtz, Li, & Nakano, 2007 |
| Eliminate Horizontal Scrolling | Horizontal scrolling is slow and frustrating. When halving SAS webpage, horizontal scrolling is required. | When dragged to the side, SAS web pages require horizontal scrolling. Realign for half-screen size. | Nielson & Tahir, 2002 |
| Expandable Link Columns | Expandable link columns increase navigability at the cost of increased errors and search time | Compressible menu bar, also will reduce visual clutter. | Bernard, 2003 |


### Proposed Modifications

The name of the examples were complex and difficult to understand, and the explanation for the syntax were distant from the syntax itself. Modifications aimed to improve the searchability of relevant information through placing desired information above the first page fold and using clear names with in-depth descriptions for examples.

<img src="/projects/img/sas_examples.jpeg" width="800" style="padding:20px"/>

### Usability Testing

The original and modified design were compared using the 5 second test, SUS, and a brief interview. The 5 second test was chosen as previous web usability research has found that people typically assess the usability of a webpage in the first 6 seconds of arriving, and that the majority of the time they do not go below the first page fold. Participants were asked to look for a particular piece of information, and rate the pages displayed on their usefulness. In order to have a more representative sample graduate students in the Psychology department were used. Prior experience was measured, with all participants familiar with SPSS, but little experience in SAS. No significant differences were found in the 5 second test or the SUS, however the vast majority of participants indicated a preference for the modified design during the interview. The difference may have been due to the large methodological experience of the sample. A follow-up with a different sample will be necessary, however the results were promising. The changes proposed would be easily applied to the entirety of the SAS documentation at little cost, and these changes appeared to have a large impact on page preference from the UCLA idre page to the SAS modified page.
