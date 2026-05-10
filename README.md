An end-to-end ML pipeline that predicts student academic performance from behavioral and demographic data, paired with an interactive Power BI dashboard for educators to monitor at-risk students in real time.


🎯 Problem Statement
Colleges and schools often identify failing students only after the semester ends — too late to intervene. This system predicts, at the start of the semester, which students are at risk of poor performance based on attendance patterns, past grades, engagement scores, and socioeconomic factors. Educators can act early: offer mentoring, flag for counseling, or adjust teaching pace.
Why it matters: Early intervention studies show academic support improves pass rates by up to 22% when applied 6+ weeks before exams.

✨ Features

🔮 Performance Prediction — Classifies students as: At-Risk / Average / High Performer
📊 Power BI Dashboard — Live visual reports for department heads with drill-down by subject, batch, and gender
🧪 Model Comparison — Benchmarks 5 algorithms; selects best-performing one automatically
📋 Feature Importance Report — Ranks which factors (attendance, assignments, etc.) matter most
🌐 Flask Web App — Teachers input student data and get instant prediction + explanation
📤 Batch Prediction — Upload a CSV of 500+ students and get results in seconds
🔁 Automated Retraining — Model retrains each semester with new data via cron job


🧠 ML Pipeline
Raw Student Data (CSV / MySQL)
        ↓
Data Cleaning & EDA
        ↓
Feature Engineering
        ↓
Model Training (5 algorithms benchmarked)
        ↓
Best Model Selected (F1-score)
        ↓
Flask API + Power BI connector
        ↓
Educator Dashboard / Predictions
Features Used in the Model
FeatureTypeDescriptionattendance_pctNumeric% of classes attendedassignment_completionNumeric% of assignments submitted on timemid_term_scoreNumericMidterm exam score (0–100)study_hours_per_weekNumericSelf-reported weekly study timeparent_education_levelCategoricalHighest parent qualificationinternet_accessBinaryHome internet availabilityextracurricularBinaryParticipation in clubs/sportsprevious_gpaNumericPrior semester GPA
Model Benchmark Results
ModelAccuracyF1 ScoreAUC-ROCLogistic Regression76.4%0.740.81Decision Tree79.1%0.770.83Random Forest86.3%0.850.91SVM83.7%0.820.88XGBoost85.9%0.840.90
✅ Random Forest selected as final model (best F1 score + AUC-ROC)

🗄️ Database Schema
sqlCREATE TABLE students (
    student_id          INT PRIMARY KEY AUTO_INCREMENT,
    name                VARCHAR(100),
    batch               VARCHAR(20),
    department          VARCHAR(50),
    semester            INT,
    attendance_pct      FLOAT,
    assignment_pct      FLOAT,
    mid_term_score      FLOAT,
    study_hours         FLOAT,
    parent_edu          VARCHAR(50),
    internet_access     BOOLEAN,
    extracurricular     BOOLEAN,
    previous_gpa        FLOAT,
    predicted_category  VARCHAR(20),   -- At-Risk / Average / High
    prediction_date     TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

📁 Project Structure
student-performance-predictor/
│
├── data/
│   ├── raw/
│   │   └── student_data.csv          # Raw dataset
│   ├── processed/
│   │   └── cleaned_data.csv          # After preprocessing
│   └── data_description.md           # Column definitions
│
├── notebooks/
│   ├── 01_EDA.ipynb                  # Exploratory data analysis
│   ├── 02_Feature_Engineering.ipynb
│   ├── 03_Model_Training.ipynb       # All 5 models benchmarked
│   └── 04_Evaluation.ipynb           # Confusion matrices, ROC curves
│
├── ml/
│   ├── preprocess.py                 # Cleaning + encoding pipeline
│   ├── train.py                      # Model training + serialization
│   ├── predict.py                    # Inference for single/batch
│   ├── evaluate.py                   # Metrics and comparison
│   └── model.pkl                     # Saved Random Forest model
│
├── app/
│   ├── main.py                       # Flask app entry point
│   ├── templates/
│   │   ├── index.html                # Input form
│   │   └── result.html               # Prediction result page
│   └── static/
│       └── style.css
│
├── powerbi/
│   ├── dashboard.pbix                # Power BI report file
│   ├── dashboard_preview.png         # Screenshot of dashboard
│   └── README_powerbi.md             # How to connect Power BI to MySQL
│
├── database/
│   ├── schema.sql
│   └── db_connect.py
│
├── tests/
│   ├── test_model.py
│   └── test_api.py
│
├── retrain_cron.sh                   # Cron job script for auto-retraining
├── requirements.txt
└── README.md

⚙️ Setup & Installation
bash# 1. Clone the repo
git clone https://github.com/tamanna47/student-performance-predictor.git
cd student-performance-predictor

# 2. Set up virtual environment
python -m venv venv
source venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Initialize the database
mysql -u root -p < database/schema.sql

# 5. Train the model
python ml/train.py

# 6. Run the Flask app
python app/main.py
# Open http://localhost:5000
Batch Prediction via CSV
bashpython ml/predict.py --input data/new_students.csv --output results/predictions.csv

🌐 Flask API Reference
POST /predict
Single Student Prediction
json{
  "attendance_pct": 78,
  "assignment_pct": 85,
  "mid_term_score": 62,
  "study_hours": 3.5,
  "parent_edu": "Graduate",
  "internet_access": true,
  "extracurricular": false,
  "previous_gpa": 7.2
}
Response
json{
  "prediction": "Average",
  "confidence": 0.82,
  "top_risk_factors": [
    "Low mid-term score (62 < 70 threshold)",
    "Below-average study hours (3.5 hrs/week)"
  ],
  "recommendation": "Recommend academic counseling and study group enrollment"
}

📊 Power BI Dashboard
The /powerbi folder contains the .pbix dashboard file with:

Overview Page: Pass/fail ratio by department and semester
At-Risk Students Page: Filterable table of flagged students with contact info
Factor Analysis Page: Which features drive poor performance (bar charts)
Trend Page: Semester-on-semester performance comparison


See powerbi/README_powerbi.md for steps to connect the dashboard to your MySQL instance.


📦 Tech Stack
LayerTechnologyMachine LearningScikit-learn (Random Forest, XGBoost)Web FrameworkFlask 3.0BI DashboardPower BI DesktopDatabaseMySQL 8.0Data ProcessingPandas, NumPyVisualizationMatplotlib, Seaborn (in notebooks)SerializationJoblib (model saving)

🚀 Future Improvements

 NLP analysis of student feedback forms to add sentiment as a feature
 Real-time dashboard with WebSocket updates
 Mobile app for students to self-assess their risk score
 Integration with college ERP systems (SAP, Oracle)
