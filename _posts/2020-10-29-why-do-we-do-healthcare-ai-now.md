---
layout: post
title: Why do we do healthcare AI, now?
date: '2020-10-29 12:00:00 +0800'
categories: Healthcare-Analytics
published: true

---

<style>body {text-align: justify}</style>

*Thoughts on Machine Learning for Healthcare, MIT 6.S897, Lecture 1*

------

Since 1970's, there were already efforts on applying the early machine learning or artificial intelligence algorithms on the healthcare system [1]. 

However, over all these years, we didn't actually see many ML or AI technologies adopted and implemented in the hospitals or other healthcare institutes. 

A few challenges that were blocking the way:

- Collecting and accessing the clinical data - laborious manual chart reviews, small cohort size and number of variables
- Fitting into the clinical workflow - busy clinicians, poor IT infrastructure
- Generalizing to the new times/places - algorithms learn from single data source, challenging logitudinal/external validation

------

The invention and rising adoption of Electronic Health Records (EHR) - represented by a nine-fold increase since 2008 in the US - brought the change.

<center><img src="/assets/ehr-adoption.png" style="zoom:50%;" /></center>

<center style="font-size:14px">Source: <a href="https://dashboard.healthit.gov/evaluations/data-briefs/non-federal-acute-care-hospital-ehr-adoption-2008-2015.php">The Office of the National Coordinator for Health Information Technology</a></center>

<br/>

Now, hospitals are getting their data stored and organized thanks to the EHR system, and ultimately it leads to large research datasets, and some freely available like [MIMIC](https://mimic.mit.edu/) and [eICU](https://eicu-crd.mit.edu/). 

Cohorts and variables can be extracted from a database with SQL queries. 

Medical concepts like diagnosis codes and laboratory tests are standardized and stored in ontologies ([ICD](https://www.cdc.gov/nchs/icd/index.htm), [SNOMED CT](http://www.snomed.org/)) and knowledge bases ([UMLS](https://www.nlm.nih.gov/research/umls/index.html)). 

Standardization of healthcare data exchange format ([HL7 FHIR](https://www.hl7.org/fhir/)) and database schemas ([i2b2](https://community.i2b2.org/wiki/display/ServerSideDesign/I2B2+DATA+MART)/[OMOP-CDM](https://www.ohdsi.org/data-standardization/)) allow different organizations to share and integrate their data sources. 

Breakthrough in ML and AI models (learning from high-dimensional features, fewer labels and more modailities) and high quality open-source softwares ([scikit-learn](https://scikit-learn.org/), [TensorFlow](https://www.tensorflow.org/), [PyTorch](https://pytorch.org/)) have been made and ready to be applied to the healthcare domain. 

Last but not least, the public is demanding for better healthcare and higher quality of life. All these factors contribute to the increasing influx of research/industry funding towards healthcare AI. 

Everything is all set except one thing - the researchers, us, who will pick up all the resources and revolutionize the healthcare with AI. 

That's why we do healthcare AI, now.

<hr/>

[1] Edward H Shortliffe and Stanton G Axline. Computer-based consultations in clinical therapeutics: Explanation and rule acquisition capabilities of the mycin. 1975.