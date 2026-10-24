Credit Behaviour Analysis and Risk Assessment
A Python-based exploratory data analysis (EDA) project that examines client credit activity, credit enquiries, loan applications, spending patterns, and approval outcomes. The aim is to identify patterns that may help a lending team better understand customer behaviour and develop more informed questions for credit-risk assessment.
Scope note: This is an exploratory analysis project, not a validated credit-scoring or default-prediction model. The available tables contain credit activity and application/approval counts, but no repayment, delinquency, or default outcome. The analysis can describe relationships in these records; it cannot establish causation or prove that a rule identifies borrowers who will default.

Project objectives
- Validate client coverage and uniqueness across three source datasets.
- Identify missing values, inconsistent time-window counts, and basic data-quality issues.
- Describe distributions of tradelines, enquiries, applications, approvals, and maximum recent spending.
- Explore relationships between credit activity, enquiries, spending, and loan application/approval behaviour.
- Create derived features and rule-based client segments for exploratory comparison.
- Document the findings, assumptions, and limitations that should be addressed before using the analysis for business decisions.
Tools and libraries
- Python
- Jupyter Notebook
- Pandas
- NumPy
- Matplotlib
- Seaborn
Data overview
The notebook expects these three CSV files in its working directory:
File	Main fields	Recorded row count
base_file.csv	Client_ID, Applied, Approved	24,800
tradeline_data.csv	Client_ID, Total_Tradelines_6m_In, Total_Tradelines_1y_In, Total_Tradelines_2y_In, Max_Trade_3m_Out	25,000
enquiry_data.csv	Client_ID, Total_Enquiry_6m_In, Total_Enquiry_1y_In, Total_Enquiry_2y_In	24,500


Client_ID is the key used to join the datasets. The notebook's initial inspection recorded 25,000 distinct IDs across the union of the three datasets and 24,304 IDs common to all three. That means 696 IDs (2.78% of the union) were not present in every dataset. Inner joins therefore reduce the sample to clients present in all joined tables; the size and composition of the merged sample should be checked before interpreting group comparisons.

Data availability: The CSVs are not included in this project package. Place the original, authorised dataset files beside the notebook before running it. Do not publish client-level or confidential data to a public repository without permission.
Workflow
1. Data understanding and validation
The notebook checks shapes, unique client IDs, exact duplicate rows, missing values, value counts for Applied and Approved, non-negative values in selected fields, temporal consistency between six-month and longer-window counts, and whether Approved exceeds Applied.

Recorded checks include:
- No exact duplicate rows were found in the three source tables.
- Initial missing values included 200 in Total_Tradelines_6m_In, 367 in Total_Enquiry_6m_In, and 49 in Applied.
- No records were initially found where Approved exceeded Applied.
- No negative values were found in the selected six-month count/spending checks.
- After the notebook's correction step, no six-month tradeline counts exceeded the corresponding one-year counts, and no six-month enquiry counts exceeded the one-year counts.
2. Data cleaning
The saved notebook fills missing six-month tradeline and enquiry counts with each column's median, and fills missing Applied values from Approved when Approved is available. It also corrects some six-month values that exceed one-year values.

These decisions are part of the recorded workflow, but they should be revisited before production use. In particular, Applied = Approved is an assumption, not a fact guaranteed by the data. The notebook also calculates 99th-percentile thresholds for selected features but does not visibly cap values at those thresholds in the saved cells. These details should be documented and validated, not treated as established best practice.
3. Univariate analysis
Histograms, count plots, mean, median, standard deviation, skewness and an IQR-based outlier check are used to understand the individual variables.
4. Bivariate analysis
The notebook uses inner joins, correlations, scatter plots and grouped averages to explore how credit activity and enquiries relate, whether tradelines associate with application counts, and whether spending relates to applications or approvals.
5. Feature engineering and segmentation
Derived fields include a client-level approval_rate, a weighted enquiry score, an application-frequency field, activity segments and rule-based risk/engagement segments. The rules provide exploratory groupings and should not be interpreted as validated credit-risk classifications.
6. Advanced comparisons
The notebook compares recorded lending outcomes across rule-based risk groups, spending groups, application-frequency groups and engagement groups. Stage 6 Question 2 from the case-study PDF (application behaviour by credit activity) is not implemented as a separate executed analysis in the saved notebook.
Key recorded findings
The values below are taken from the outputs saved in the notebook. They describe the analysed data under the notebook's cleaning and metric definitions; they are not universal conclusions about credit behaviour.
Analysis	Recorded result	Interpretation
Client coverage	25,000 unique IDs across all tables; 24,304 common to all three	696 IDs (2.78%) are absent from at least one table, so joined samples may be smaller.
Six-month tradelines	Mean 9.49; median 10; standard deviation 5.72; skewness approximately -0.003	Mean and median are close, and the recorded skewness is near zero.
Six-month enquiries	Mean 7.04; median 7	Typical six-month enquiry count is about seven. The notebook's printed standard deviation and skewness for this field are incorrect because they reuse a variable from the tradeline calculation.
Loan applications	79.7% of rows have Applied > 0; 20.3% have Applied = 0	Most clients in this table have a positive application count. This is a share of clients, not an approval rate.
Approved count	63.9% have Approved > 0; 36.1% have Approved = 0	This is the share with at least one recorded approval, not the proportion of applications approved.
Maximum three-month spending	Mean 10,471.49; median 10,429; standard deviation 5,498.74; zero IQR outliers were flagged	The central values are close. Zero flagged IQR outliers only means no values exceeded the calculated bounds.
One-year tradelines	Mean 18.13; median 18; standard deviation 6.28	The average and median counts are similar.
One-year enquiries	Mean 14.37; median 14; standard deviation 5.53	The average and median counts are similar.
Six-month tradelines vs enquiries	Correlation 0.01	Almost no linear relationship was observed in the analysed sample.
More vs fewer tradelines	Average Applied 1.97 vs 1.99	Difference is negligible under the notebook's median split. These are average application counts, not rates.
Spending vs approvals among Applied > 0	Correlation -0.003	Almost no linear association was observed between the selected spending field and Approved in this subset.
One-year enquiries vs one-year tradelines	Correlation rounded to 0.00	Almost no linear association was observed in the saved output.
Spending vs applications	Correlation rounded to 0.00	Almost no linear association was observed in the saved output.
Mean client-level approval ratio	0.52	The notebook calculates Approved / Applied and replaces undefined/infinite ratios with zero; this is a mean of client-level ratios, not the aggregate approval rate.
Weighted enquiry score	Mean 9.24	Score = 0.7 × Total_Enquiry_6m_In + 0.3 × Total_Enquiry_1y_In. This is a custom feature; the time windows overlap.
Credit-activity segments	Low: 7,419; Medium: 12,689; High: 4,892	Medium activity is the largest segment (about 50.8% of the tradeline table).
Combined risk profile	Low: 2,894; Medium: 16,795; High: 4,811	Medium Risk is the largest segment under the specified rule. The labels are rule-defined, not validated against default outcomes.
Stage 6 spending groups	Mean Approved: Low 1.29; Medium 1.18; High 1.19	These are means of raw approval counts, even though the notebook labels the chart as an approval rate. They should not be described as approval probabilities.
Stage 6 application-frequency groups	Mean Approved: High frequency 1.90; Low frequency 0.73	These are average approval counts, not approval rates; more applications can create more opportunities for approvals.
Engagement groups	Mean client-level approval ratio: Engaged 0.65; Less Engaged 0.66	The means are close under this definition. Clients in neither group are classified as Other in the notebook and are excluded from this comparison.


Important metric and methodology caveats
Several points must be fixed or explicitly explained before drawing strong conclusions:
1. Counts are not rates. The Stage 6 spending and application-frequency sections average Approved and label it as an approval rate. The mean of a count can exceed 1. To compare approval likelihood, report an appropriately defined rate such as total approved applications divided by total applications, if the fields represent compatible counts.
2. Client-level vs aggregate approval rate. The mean of Approved / Applied across clients is not the same as sum(Approved) / sum(Applied). State which metric is used and how clients with zero applications are handled.
3. Utilization feature needs redesign. The notebook uses Max_Trade_3m_Out / (Max_Trade_3m_Out + Total_Tradelines_6m_In). This mixes a spending amount with a tradeline count, so it is not a meaningful credit-utilization ratio. A proper utilisation calculation would require a comparable credit balance and available-credit limit.
4. Enquiry descriptive statistics bug. The notebook's enquiry summary reuses the earlier col variable for standard deviation and skewness. Recalculate with enq_col.std() and enq_col.skew() before publishing those statistics.
5. Missing-value assumptions. Filling Applied from Approved may create an unjustified value. Verify the business meaning of missing application counts first.
6. Outlier treatment. The notebook calculates the 99th percentile but does not apply a cap in the saved cells. The tradeline outlier code also uses a lower-bound multiplier of 1.8 rather than the conventional 1.5; confirm the intended rule.
7. Correlation is not causation. Near-zero correlations indicate weak linear relationships in this sample. They do not rule out non-linear relationships, subgroup effects or links with outcomes that were not available.
8. Risk labels are exploratory. The high-, medium- and low-risk labels are driven by hand-set thresholds and are not tested against defaults, delinquencies or repayment performance.
How to run the notebook
1. Install Python and Jupyter Notebook (or use JupyterLab / VS Code).
2. Install the required packages:
   pip install pandas numpy matplotlib seaborn jupyter
3. Place the three CSV files in the same directory as the notebook, using these exact filenames:
   base_file.csv
   enquiry_data.csv
   tradeline_data.csv
4. Open Risk Credit Analysis-checkpoint.ipynb and run the cells from top to bottom. Running in order matters because some later cells depend on DataFrames and features created earlier.
5. Before publishing numerical results, correct the metric caveats above and rerun the affected cells.
Suggested repository structure
credit-behaviour-analysis/
├── README.md
├── Risk Credit Analysis-checkpoint.ipynb
├── reports/
│   └── Credit_Behaviour_Analysis_Interview_Guide.pdf
└── data/                 # Keep private or ignored by Git unless authorised
    ├── base_file.csv
    ├── enquiry_data.csv
    └── tradeline_data.csv
Add a .gitignore rule for private data if needed. For example:
data/*.csv
.ipynb_checkpoints/
__pycache__/
Business value
The project demonstrates a structured workflow for validating data, exploring distributions, joining multiple tables, engineering features, comparing segments and communicating analytical caveats. Its current results suggest that the selected linear relationships are weak and that simple credit-activity or spending thresholds do not clearly separate approval outcomes in the recorded analysis. That is a useful exploratory finding, but not evidence that these features have no predictive value or that the risk categories are reliable. A stronger next version would correct the metrics, use consistent sample definitions, examine group sizes and confidence intervals, and evaluate outcomes such as repayment, delinquency or default if those data can be lawfully obtained.
Deliverables and next steps
- Re-run the notebook with the original authorised CSV files.
- Fix the incorrect enquiry standard-deviation/skewness calculation.
- Redesign or remove the current utilization_rate feature.
- Distinguish application counts, approval counts, client-level ratios and aggregate approval rates throughout the notebook and charts.
- Complete Stage 6 Q2 explicitly.
- Add validated repayment or default outcomes before presenting the segments as credit-risk indicators.
