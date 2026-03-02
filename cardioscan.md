---
layout: page
title: CardioScan
permalink: /projects/cardioscan/
---

**Dec 2025 · Data Science and ML**
[cardiac-assessment.onrender.com](https://cardiac-assessment.onrender.com) · [github.com/Chenry513/Readmeify](https://github.com/Chenry513/cardiac_assessment)

## The Project

CardioScan is a clinical web app that screens patients for cardiac disease risk using machine learning. Clinicians can enter a patient's measurements and get an instant risk score along with a breakdown of which clinical factors are driving that score up or down. It is built on all four UCI Heart Disease datasets combined into a single 920-patient cohort.

## The Data

The raw data comes from four different institutions — Cleveland Clinic, the Hungarian Institute of Cardiology, University Hospital Zurich, and a VA Medical Center in Long Beach. Each site collected the data independently which means inconsistent encodings, different missing value patterns, and up to 66% missing values for some features across the non-Cleveland sites.

The ETL pipeline handles all of this. It loads all four CSVs, standardises the column encodings across institutions, binarises the target variable, and imputes missing values using per-column medians which are robust to outliers. The result is a clean 920-patient cohort ready for training.

## The Model

The classifier is a Gradient Boosting model using sklearn's native implementation, which is the same algorithm as XGBoost. It was chosen over Random Forest and Logistic Regression because it handles the mixed feature types and class distribution better without needing a lot of extra preprocessing.

It achieves an AUC of 0.892 on the test set and 0.872 with 5-fold stratified cross-validation, with 84.2% accuracy. The top predictive features are chest pain type, max heart rate, ST depression, number of major vessels, and age.

## Explainability

Each patient gets a per-feature contribution score. The way it works is by replacing one feature at a time with the median value and measuring how much the predicted probability changes. This gives clinicians a readable breakdown of why the model flagged a patient as high risk, similar to how SHAP values work.

## Stack

- **scikit-learn** — Gradient Boosting Classifier, cross-validation, ROC/AUC
- **pandas / numpy** — ETL pipeline, imputation, feature engineering
- **Flask** — backend and REST API
- **PostgreSQL** — production database (SQLite fallback for local dev)
- **Chart.js** — ROC curve, feature importances, population analytics
- **Render.com** — deployment
