1) Core Problem Statement

Primary Health Centres (PHCs) across India struggle with:

Unpredictable demand

20–40% avoidable wastage from expiry

Irregular physical stock verification

Duplicate or inconsistent entries in registers

No automated Reorder Level (ROL) alerts

Lack of FEFO compliance

Manual calculations for buffer and safety stock

Non-moving (FSN) inventory choking storage

No visibility on cold chain breaches

This results in stockouts of Vital drugs, wastage of Desirable items, and inefficient ordering of Essential medicines.

The current system is reactive, not predictive.
Your project fixes this.

2) What This System Does

This system builds a complete digital brain for PHC inventory control:

✔ A. Demand Forecasting (14–30 Days)

Forecast per-medicine daily consumption

Incorporates seasonality, admissions data, and FSN behavior

✔ B. NHSRC-Compliant Inventory Control Logic

Implements REAL PHC formulas (not generic ML formulas):

ADC = Total consumption / Number of days

ROL = Max Daily Consumption × Max Lead Time + Safety Stock

MSL = ROL – (ADC × Avg Lead Time)

Total Estimated Demand = (ADC × Days) + (LT × ADC) + Buffer – Stock

✔ C. VED Classification Engine

Vital → zero tolerance for stockouts → high safety stock

Essential → optimized ROP

Desirable → low ROP, expiry monitoring

✔ D. FSN Classification Engine

Based on consumption velocity:

Fast → high buffer

Slow → routine review

Non-moving → expiry risk + redistribution

✔ E. FEFO Expiry Surveillance

Predict which batches will expire in the next 180 / 90 / 30 days
Recommend redistribution across PHCs.

✔ F. Pilferage & Data Quality Anomaly Detection

Detects:

duplicate entries

mismatched opening–closing stocks

unexplained consumption spikes

zero-issue days with falling stock

disposal without entry

fake stock updates

✔ G. Cold-Chain Risk Modeling

Temperature breach → risk score → expiry acceleration.

✔ H. PHC Dashboard (Power BI)

Metrics include:

VED stockout risk

FEFO compliance

FSN distribution

NHSRC ROL vs AI ROP

Near expiry alerts

Pilferage risk index

Lead-time variability

Facility performance ranking

3) Final Architecture

🟦 A. Ingestion Layer

Facility stock registers

Daily issue-entry registers

Supplier GRNs

Batch & expiry data

PHC admissions

DVDMS/e-Aushadhi endpoints

Cold-chain temp logs

🟩 B. Processing Layer

Cleaning

Drug name standardization (NLP)

FSN → consumption velocity

VED → priority classification

FEFO → expiry horizon

🟧 C. Modeling Layer

Forecasting (Prophet + LightGBM)

ROP calculator (NHSRC + ML hybrid)

Expiry probability model

Pilferage anomaly model

Redistribution recommendation model

🟨 D. Orchestration

Airflow/ADF DAG: ingest → clean → classify → forecast → optimize → alert

🟥 E. Serving Layer

Model artifacts

Power BI refreshable datasets

Alerts (SMS/Email/WhatsApp hooks)

API endpoints if needed

4) Final Data Schema

Medicine_Master:
medicine_id  
medicine_name  
unit  
strength  
VED_category     # Vital / Essential / Desirable  
FSN_category     # Fast / Slow / Non-moving  
storage_type      # cold-chain / room / controlled  

Inventory_Transactions:
date  
facility_id  
medicine_id  
opening_stock  
received_qty  
issued_qty  
closing_stock  
batch_no  
expiry_date  
supplier_id  
temperature excursion flag  

Forecasting_Input
ADC  
Max daily consumption  
Avg daily consumption  
Avg lead time  
Max lead time  
Safety stock  
ROL (NHSRC formula)  
MSL  
Buffer stock days  

Quality Flags
duplicate_entry_flag  
pilferage_risk_flag  
near_expiry_flag  
non_moving_flag  
physical_mismatch_flag  
temperature_excursion_flag  

5) Notebook Structure

01_EDA.ipynb

Basic exploration + NHSRC indicators:

VED distribution

FSN velocity curves

Expiry horizon distribution

02_CLEANING_STANDARDIZATION.ipynb

drug name normalization

missing stock adjustment

duplicate entry detection

03_FEATURE_ENGINEERING_NHSRC.ipynb

ADC

daily consumption velocity

FSN classification

FEFO horizon

lead-time modeling

04_FORECASTING_BASELINES.ipynb

ARIMA/ETS and seasonal naive.

05_FORECASTING_ML.ipynb

LightGBM/Prophet hybrid model.

06_ROP_CALCULATOR_NHSRC.ipynb

Implement official formulas + ML correction.

07_EXPIRY_MODEL_FEFO.ipynb

Predict probability of expiry in next 180/90/30 days.

08_PILFERAGE_MODEL.ipynb

Anomaly detection using:

entry mismatch

sudden drops

duplicate names

09_REDISPATCH_RECOMMENDER.ipynb

Suggest PHC-to-PHC redistribution to avoid expiry.

10_DASHBOARD_DATA_PREP.ipynb

Prepare output files for Power BI.

6) Final Dashboard KPIs (NHSRC-Compliant)

Include these visuals:

🔴 Stockout Risk (VED segmented)

Vital | Essential | Desirable
Predicted shortage date | Days of stock

🟡 Expiry Risk (FEFO)

180-day
90-day
30-day
Batch visualizations

🔵 FSN Analysis

Pie chart + trend
Top non-moving items

🟣 Lead-Time Variability

Supplier reliability curve
Avg LT vs Max LT
ROP risk

🟤 Pilferage Risk

Score heatmap
Suspicious facilities
Mismatch logs

🟥 NHSRC ROL vs AI ROP

Comparison table
AI savings vs traditional policy
