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
