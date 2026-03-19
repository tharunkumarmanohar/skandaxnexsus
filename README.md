# skandaxnexsus
ICDS Child Development Intelligence Dashboard - Andhra Pradesh
cat > README.md << 'EOF'
# ICDS Child Development Intelligence Dashboard - Andhra Pradesh

## 🎯 Project Overview
AI-enabled Early Detection & Personalization system for children aged 0-6 years using predictive risk stratification, dynamic intervention planning, and caregiver empowerment.

## ✨ Core Features
- **Predictive Risk Stratification**: Generate scores for cognitive, motor, and socio-emotional domains
- **Dynamic Intervention**: Automated, child-specific activity plans
- **Caregiver Empowerment**: Low-literacy guidance via audio/visual nudges in local languages

## 📊 Mandatory Dashboard KPIs
- Screening & Coverage metrics
- Risk Stratification (Low/Medium/High/Critical)
- Referral & Action Support
- Workforce & System Performance

## 🚀 Quick Start
```bash
pip install -r requirements.txt
python app.py
 
Project Lead: THARUN KUMAR MANOHAR Email: tharunkumarmanohar@gmail.com EOF


#### **3.2 - requirements.txt**
```bash
cat > requirements.txt << 'EOF'
flask==3.0.0
pandas==2.0.0
numpy==1.24.0
openpyxl==3.10.0
python-dotenv==1.0.0
Werkzeug==3.0.0
EOF

3.3 - baseline_risk_logic.py
cat > baseline_risk_logic.py << 'EOF'
"""
Baseline Risk Scoring Logic - AI-enabled Risk Stratification
Domain Scoring: Cognitive, Language, Motor (GM/FM), Socio-Emotional
"""

class BaselineRiskLogic:
    def __init__(self):
        self.domain_weights = {
            'cognitive': 0.25,
            'language': 0.20,
            'gross_motor': 0.15,
            'fine_motor': 0.15,
            'socio_emotional': 0.25
        }
        
        self.risk_thresholds = {
            'critical': (80, 100),
            'high': (60, 79),
            'medium': (40, 59),
            'low': (0, 39)
        }
    
    def calculate_domain_score(self, assessments):
        """Calculate weighted score for each domain (0-100)"""
        scores = {}
        
        # Cognitive Domain
        cognitive_items = [
            assessments.get('problem_solving', 0),
            assessments.get('memory', 0),
            assessments.get('attention', 0),
            assessments.get('learning_speed', 0)
        ]
        scores['cognitive'] = sum(cognitive_items) / len(cognitive_items) if cognitive_items else 0
        
        # Language Domain
        language_items = [
            assessments.get('expressive_language', 0),
            assessments.get('receptive_language', 0),
            assessments.get('vocabulary', 0)
        ]
        scores['language'] = sum(language_items) / len(language_items) if language_items else 0
        
        # Gross Motor Domain
        motor_gm_items = [
            assessments.get('balance', 0),
            assessments.get('coordination', 0),
            assessments.get('strength', 0)
        ]
        scores['gross_motor'] = sum(motor_gm_items) / len(motor_gm_items) if motor_gm_items else 0
        
        # Fine Motor Domain
        motor_fm_items = [
            assessments.get('dexterity', 0),
            assessments.get('grasp', 0),
            assessments.get('hand_eye_coordination', 0)
        ]
        scores['fine_motor'] = sum(motor_fm_items) / len(motor_fm_items) if motor_fm_items else 0
        
        # Socio-Emotional Domain
        socio_items = [
            assessments.get('social_interaction', 0),
            assessments.get('emotional_regulation', 0),
            assessments.get('self_awareness', 0)
        ]
        scores['socio_emotional'] = sum(socio_items) / len(socio_items) if socio_items else 0
        
        return scores
    
    def calculate_baseline_risk_score(self, domain_scores):
        """Calculate weighted overall risk score"""
        weighted_score = sum(
            domain_scores.get(domain, 0) * weight
            for domain, weight in self.domain_weights.items()
        )
        return round(weighted_score, 2)
    
    def classify_risk_level(self, risk_score):
        """Classify child into risk category"""
        for level, (min_val, max_val) in self.risk_thresholds.items():
            if min_val <= risk_score <= max_val:
                return level
        return 'unknown'
    
    def get_risk_details(self, assessments):
        """Generate complete risk profile"""
        domain_scores = self.calculate_domain_score(assessments)
        baseline_score = self.calculate_baseline_risk_score(domain_scores)
        risk_level = self.classify_risk_level(baseline_score)
        
        # Identify domains with highest risk
        at_risk_domains = [
            domain for domain, score in domain_scores.items()
            if score >= 60  # Threshold for concerning score
        ]
        
        return {
            'baseline_risk_score': baseline_score,
            'risk_level': risk_level,
            'domain_scores': domain_scores,
            'at_risk_domains': at_risk_domains,
            'urgency': 'CRITICAL' if baseline_score >= 80 else ('HIGH' if baseline_score >= 60 else 'MEDIUM' if baseline_score >= 40 else 'LOW')
        }
EOF

cat > referral_logic.py << 'EOF'
"""
Referral Decision Support Logic - Clinical Decision Rules
Routes children to appropriate specialists based on domain deficits
"""

class ReferralLogic:
    def __init__(self):
        self.referral_rules = {
            'neurodevelopmental_specialist': {
                'triggers': ['cognitive_severe', 'language_severe', 'autism_indicators'],
                'priority': 'CRITICAL'
            },
            'speech_language_therapist': {
                'triggers': ['language_severe', 'speech_delay'],
                'priority': 'HIGH'
            },
            'physiotherapist': {
                'triggers': ['gross_motor_severe', 'fine_motor_severe'],
                'priority': 'HIGH'
            },
            'psychologist': {
                'triggers': ['socio_emotional_severe', 'behavioral_issues'],
                'priority': 'HIGH'
            },
            'nutritionist': {
                'triggers': ['severe_malnutrition', 'growth_failure'],
                'priority': 'CRITICAL'
            },
            'social_worker': {
                'triggers': ['family_crisis', 'abuse_suspected', 'economic_hardship'],
                'priority': 'CRITICAL'
            }
        }
    
    def determine_referrals(self, risk_profile, neurobehavioral_data, nutrition_data):
        """Determine which services child should be referred to"""
        referrals = []
        
        # Check domain-specific referrals
        at_risk_domains = risk_profile.get('at_risk_domains', [])
        
        if 'cognitive' in at_risk_domains:
            referrals.append({
                'service': 'neurodevelopmental_specialist',
                'reason': 'Cognitive domain delay detected',
                'priority': 'CRITICAL' if risk_profile['baseline_risk_score'] >= 80 else 'HIGH'
            })
        
        if 'language' in at_risk_domains:
            referrals.append({
                'service': 'speech_language_therapist',
                'reason': 'Language domain delay detected',
                'priority': 'HIGH'
            })
        
        if 'gross_motor' in at_risk_domains or 'fine_motor' in at_risk_domains:
            referrals.append({
                'service': 'physiotherapist',
                'reason': 'Motor domain delay detected',
                'priority': 'HIGH'
            })
        
        if 'socio_emotional' in at_risk_domains:
            referrals.append({
                'service': 'psychologist',
                'reason': 'Socio-emotional domain delay detected',
                'priority': 'MEDIUM'
            })
        
        # Check neurobehavioral indicators
        if neurobehavioral_data.get('autism_indicators', False):
            referrals.append({
                'service': 'neurodevelopmental_specialist',
                'reason': 'Autism spectrum indicators detected',
                'priority': 'CRITICAL'
            })
        
        if neurobehavioral_data.get('behavioral_issues', False):
            referrals.append({
                'service': 'psychologist',
                'reason': 'Behavioral issues reported',
                'priority': 'HIGH'
            })
        
        # Check nutrition indicators
        if nutrition_data.get('malnutrition_status', '') == 'SEVERE':
            referrals.append({
                'service': 'nutritionist',
                'reason': 'Severe malnutrition detected',
                'priority': 'CRITICAL'
            })
        
        return referrals
    
    def calculate_referral_urgency(self, baseline_score):
        """Determine urgency level for referral completion"""
        if baseline_score >= 80:
            return {'urgency': 'CRITICAL', 'target_days': 3}
        elif baseline_score >= 60:
            return {'urgency': 'HIGH', 'target_days': 7}
        elif baseline_score >= 40:
            return {'urgency': 'MEDIUM', 'target_days': 14}
        else:
            return {'urgency': 'LOW', 'target_days': 30}
EOF

cat > kpi_calculator.py << 'EOF'
"""
KPI Calculator - Mandatory Dashboard Metrics
"""

from datetime import datetime
from collections import defaultdict

class KPICalculator:
    def __init__(self, data):
        self.data = data
        self.calculation_date = datetime.now().strftime("%Y-%m-%d")
    
    def total_children_screened(self):
        return len(self.data.get('children', []))
    
    def assessments_per_awc(self):
        awc_assessments = defaultdict(int)
        for child in self.data.get('children', []):
            awc = child.get('awc_id', 'Unknown')
            awc_assessments[awc] += 1
        return dict(awc_assessments)
    
    def get_age_band_distribution(self):
        age_bands = {'0-1': 0, '1-2': 0, '2-3': 0, '3-4': 0, '4-5': 0, '5-6': 0}
        for child in self.data.get('children', []):
            age = child.get('age_in_months', 0) / 12
            if age < 1:
                age_bands['0-1'] += 1
            elif age < 2:
                age_bands['1-2'] += 1
            elif age < 3:
                age_bands['2-3'] += 1
            elif age < 4:
                age_bands['3-4'] += 1
            elif age < 5:
                age_bands['4-5'] += 1
            else:
                age_bands['5-6'] += 1
        return age_bands
    
    def children_by_risk_level(self):
        risk_counts = {'critical': 0, 'high': 0, 'medium': 0, 'low': 0}
        for child in self.data.get('children', []):
            risk_level = child.get('risk_level', 'unknown').lower()
            if risk_level in risk_counts:
                risk_counts[risk_level] += 1
        return risk_counts
    
    def get_all_kpis(self):
        return {
            'calculation_date': self.calculation_date,
            'screening_and_coverage': {
                'total_children_screened': self.total_children_screened(),
                'assessments_per_awc': self.assessments_per_awc(),
                'age_band_distribution': self.get_age_band_distribution()
            },
            'risk_stratification': {
                'children_by_risk_level': self.children_by_risk_level()
            }
        }
EOF

cat > app.py << 'EOF'
"""
Flask Application - ICDS Dashboard Backend
"""

from flask import Flask, jsonify, render_template
from baseline_risk_logic import BaselineRiskLogic
from referral_logic import ReferralLogic
from kpi_calculator import KPICalculator

app = Flask(__name__)

baseline_logic = BaselineRiskLogic()
referral_logic = ReferralLogic()

sample_data = {
    'children': [
        {
            'child_id': 'C001',
            'name': 'Aditya Kumar',
            'age_in_months': 36,
            'awc_id': 'AWC_001',
            'risk_score': 92,
            'risk_level': 'critical',
            'at_risk_domains': ['cognitive', 'language'],
            'referral_needed': True,
            'referral_completed': False
        }
    ],
    'training': []
}

@app.route('/')
def dashboard():
    return render_template('dashboard.html')

@app.route('/api/kpis', methods=['GET'])
def get_kpis():
    kpi_calc = KPICalculator(sample_data)
    kpis = kpi_calc.get_all_kpis()
    return jsonify(kpis)

if __name__ == '__main__':
    app.run(debug=True, port=5000)
EOF

3.7 - .gitignore
cat > .gitignore << 'EOF'
__pycache__/
*.py[cod]
.vscode/
.idea/
.env
*.db
*.log
EOF

cat > contact.txt << 'EOF'
ICDS Child Development Intelligence Dashboard
Project Lead: THARUN KUMAR MANOHAR
Email: tharunkumarmanohar@gmail.com
GitHub: https://github.com/tharunkumarmanohar/skandaxnexsus
EOF

git add .
git commit -m "Initial commit: Complete ICDS Dashboard with Risk Stratification, Referral Logic, and KPI Calculator"
git push origin main
