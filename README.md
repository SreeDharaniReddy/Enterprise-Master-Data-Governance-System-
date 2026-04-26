# Enterprise Master Data Governance System

## Overview
A centralized master data governance platform that consolidates customer, supplier, material, and financial master data from multiple disparate source systems into a single governed repository. Built using SQL Server and Python with automated data quality validation, deduplication, and role-based approval workflows.

## Key Features

### 🎯 Data Consolidation
- Integrated 5+ disparate source systems into single governed repository
- Reduced data duplication by 78%
- Centralized governance for customer, supplier, material, and financial master data

### ✅ Data Quality Framework
- 25+ automated business rules for completeness, accuracy, and consistency checks
- Flagged 12,000+ quality issues before distribution to downstream systems
- Real-time validation at data entry and batch processing stages

### 🔄 Approval Workflows
- Role-based approval workflows for create/change/extend requests
- Material master: 8 approval steps
- Vendor master: 6 approval steps
- Customer master: 5 approval steps
- Reduced average approval cycle time by 42%

### 🔍 Deduplication & Matching
- Fuzzy logic and phonetic matching algorithms (Soundex, Levenshtein distance)
- Identified and merged 3,500+ duplicate records
- Machine learning-based similarity scoring for potential matches

### 📊 Audit & Compliance
- Comprehensive audit trail for all CRUD operations
- User, timestamp, and change delta tracking
- Full regulatory compliance for FDA and EMA audits
- Power BI dashboards for data stewardship KPIs

## Technology Stack

- **Database**: SQL Server 2019
- **Backend**: Python 3.9+
- **Libraries**: 
  - `pandas` - Data manipulation
  - `sqlalchemy` - Database connectivity
  - `fuzzywuzzy` - Fuzzy string matching
  - `jellyfish` - Phonetic matching (Soundex)
  - `python-Levenshtein` - Edit distance calculations
- **Visualization**: Power BI
- **Version Control**: Git

## Project Structure

```
enterprise-mdg-system/
├── src/
│   ├── data_quality/
│   │   ├── validation_rules.py
│   │   ├── completeness_checks.py
│   │   ├── accuracy_checks.py
│   │   └── consistency_checks.py
│   ├── deduplication/
│   │   ├── matching_algorithms.py
│   │   ├── fuzzy_matcher.py
│   │   ├── phonetic_matcher.py
│   │   └── merge_engine.py
│   ├── workflows/
│   │   ├── approval_engine.py
│   │   ├── material_workflow.py
│   │   ├── vendor_workflow.py
│   │   └── customer_workflow.py
│   ├── etl/
│   │   ├── source_extractors.py
│   │   ├── transformations.py
│   │   └── load_master.py
│   └── audit/
│       ├── change_tracker.py
│       └── audit_logger.py
├── sql/
│   ├── schema/
│   │   ├── master_tables.sql
│   │   ├── staging_tables.sql
│   │   └── audit_tables.sql
│   ├── stored_procedures/
│   │   ├── sp_validate_material.sql
│   │   ├── sp_validate_vendor.sql
│   │   └── sp_merge_duplicates.sql
│   └── views/
│       ├── vw_data_quality_dashboard.sql
│       └── vw_pending_approvals.sql
├── powerbi/
│   ├── MDG_Dashboard.pbix
│   └── Data_Quality_Metrics.pbix
├── tests/
│   ├── test_validation_rules.py
│   ├── test_deduplication.py
│   └── test_workflows.py
├── docs/
│   ├── architecture_diagram.png
│   ├── workflow_design.md
│   └── user_guide.pdf
├── config/
│   ├── database_config.yaml
│   └── quality_rules_config.json
├── requirements.txt
└── README.md
```

## Installation

### Prerequisites
- Python 3.9+
- SQL Server 2019 or higher
- Power BI Desktop

### Setup

1. Clone the repository:
```bash
git clone https://github.com/SreeDharaniReddy/Enterprise-MDG-System.git
cd Enterprise-MDG-System
```

2. Install Python dependencies:
```bash
pip install -r requirements.txt
```

3. Configure database connection:
```bash
cp config/database_config.yaml.example config/database_config.yaml
# Edit database_config.yaml with your SQL Server credentials
```

4. Initialize database schema:
```bash
python scripts/init_database.py
```

5. Load initial validation rules:
```bash
python scripts/load_quality_rules.py
```

## Usage

### Running Data Quality Checks
```python
from src.data_quality.validation_rules import DataQualityEngine

# Initialize quality engine
dq_engine = DataQualityEngine()

# Run completeness checks
results = dq_engine.run_completeness_checks(domain='material_master')

# Run all validation rules
all_results = dq_engine.run_all_validations()
```

### Deduplication and Matching
```python
from src.deduplication.matching_algorithms import DuplicateMatcher

# Initialize matcher
matcher = DuplicateMatcher(threshold=0.85)

# Find potential duplicates in vendor master
duplicates = matcher.find_duplicates(
    domain='vendor_master',
    fields=['vendor_name', 'address', 'tax_id']
)

# Merge confirmed duplicates
matcher.merge_records(master_id=12345, duplicate_ids=[12346, 12347])
```

### Workflow Management
```python
from src.workflows.approval_engine import WorkflowEngine

# Submit material creation request
workflow = WorkflowEngine()
request_id = workflow.submit_request(
    request_type='CREATE',
    domain='material_master',
    data={'material_number': 'MAT001', 'description': 'Raw Material A'},
    requester_id='user123'
)

# Approve request
workflow.approve_step(request_id=request_id, approver_id='manager456')
```

## Data Quality Rules

### Completeness Rules
- Required fields validation (material number, description, UOM, etc.)
- Mandatory field coverage > 95%

### Accuracy Rules
- Valid value ranges (e.g., unit price > 0)
- Reference data validation (e.g., valid currency codes)
- Format validation (e.g., tax ID format)

### Consistency Rules
- Cross-field validation (e.g., ship-to address matches bill-to country)
- Temporal consistency (e.g., end date > start date)
- Cross-system consistency checks

## Key Metrics & Results

| Metric | Before MDG | After MDG | Improvement |
|--------|-----------|-----------|-------------|
| Data Duplication Rate | 23% | 5% | **78% reduction** |
| Data Quality Score | 67% | 94% | **+27 points** |
| Approval Cycle Time | 8.2 days | 4.8 days | **42% faster** |
| Manual Data Entry Errors | 340/month | 45/month | **87% reduction** |
| Audit Preparation Time | 40 hours | 6 hours | **85% reduction** |

## Deduplication Algorithm

The system uses a multi-stage matching approach:

1. **Exact Match**: Direct comparison on key fields
2. **Fuzzy Match**: Levenshtein distance with 85% threshold
3. **Phonetic Match**: Soundex algorithm for name variations
4. **Machine Learning Scoring**: Weighted scoring across multiple attributes

```python
def calculate_match_score(record1, record2):
    """
    Calculate similarity score between two records
    Returns score between 0 and 1
    """
    weights = {
        'name': 0.4,
        'address': 0.3,
        'tax_id': 0.2,
        'contact': 0.1
    }
    
    scores = {}
    scores['name'] = fuzz.ratio(record1['name'], record2['name']) / 100
    scores['address'] = fuzz.token_sort_ratio(record1['address'], record2['address']) / 100
    scores['tax_id'] = 1.0 if record1['tax_id'] == record2['tax_id'] else 0.0
    scores['contact'] = fuzz.ratio(record1['contact'], record2['contact']) / 100
    
    final_score = sum(scores[k] * weights[k] for k in weights)
    return final_score
```

## Approval Workflow Design

### Material Master Workflow (8 Steps)
1. Requestor submits creation request
2. Material Planner reviews technical specifications
3. Procurement reviews sourcing information
4. Quality Management reviews quality parameters
5. Finance reviews costing data
6. Warehouse reviews storage parameters
7. Data Steward validates completeness
8. Master Data Manager final approval

### Workflow State Machine
```
DRAFT → SUBMITTED → IN_REVIEW → APPROVED → ACTIVATED
                        ↓
                   REJECTED → DRAFT
```

## Dashboard & Reporting

### Data Stewardship KPIs
- Request volume by domain and type
- Average approval cycle time
- Data quality score trends
- Duplicate resolution rate
- Top data quality issues by category

### Power BI Dashboards
1. **Executive Dashboard**: High-level KPIs and trends
2. **Data Quality Dashboard**: Detailed quality metrics by domain
3. **Workflow Dashboard**: Request status, bottlenecks, and SLA tracking
4. **Audit Dashboard**: Change history and compliance metrics

## Database Schema

### Core Tables

**master_data.material_master**
- material_id (PK)
- material_number (Unique)
- description
- base_uom
- material_type
- industry_sector
- created_by, created_date
- modified_by, modified_date
- data_quality_score

**master_data.vendor_master**
- vendor_id (PK)
- vendor_number (Unique)
- vendor_name
- address, city, country
- tax_id
- payment_terms
- created_by, created_date

**governance.change_requests**
- request_id (PK)
- request_type (CREATE/CHANGE/EXTEND)
- domain (material/vendor/customer)
- request_data (JSON)
- workflow_status
- current_approver
- created_date

**audit.change_log**
- log_id (PK)
- table_name
- record_id
- operation (INSERT/UPDATE/DELETE)
- old_values (JSON)
- new_values (JSON)
- changed_by
- changed_date

## Testing

Run unit tests:
```bash
pytest tests/ -v
```

Run specific test suite:
```bash
pytest tests/test_validation_rules.py -v
```

Generate coverage report:
```bash
pytest --cov=src tests/
```

## Performance Optimization

- Indexed all foreign keys and frequently queried fields
- Partitioned audit tables by month
- Implemented caching for validation rule lookups
- Batch processing for large-volume deduplication jobs
- Query optimization with execution plan analysis

## Future Enhancements

- [ ] Integration with SAP MDG for enterprise-wide governance
- [ ] Machine learning-based anomaly detection
- [ ] Real-time data quality monitoring with alerts
- [ ] Mobile app for approval workflow management
- [ ] Advanced analytics with predictive data quality scoring
- [ ] API gateway for third-party system integration
