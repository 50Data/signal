# Technical Architecture - 50Data EU Compliance Deadline Service

*Blinktank GmbH | Non-Technical Founder + Claude Code Implementation*

## 🎯 Implementation Reality

**Founder Role (Andreas Dahrendorf):**
- Business strategy and market validation
- Content direction and compliance expertise
- Claude Code handles: MD/YAML/CSV generation + Python script creation
- Customer feedback and growth decisions
- No Python coding - executes scripts Claude Code provides

**Claude Code Role:**
- 100% of technical development and implementation
- Server setup, database design, API integrations
- Code writing, testing, deployment, and maintenance
- Technical troubleshooting and system scaling
- All DevOps and infrastructure management

**User Interface:** Claude Code handles all technical implementation - user provides business direction only

## 🏗️ Core System Overview

### Core Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                   50DATA EU DEADLINE SERVICE                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────┐    ┌─────────────────────┐    ┌─────────────────┐   │
│  │DATA SOURCES │───▶│  DEADLINE EXTRACTION│───▶│  ICS CALENDAR   │   │
│  │             │    │                     │    │                 │   │
│  │ • EUR-Lex   │    │ • Date Parsing      │    │ • ICS Generation│   │
│  │ • German    │    │ • AI Validation     │    │ • Password Protect│  │
│  │   Sources   │    │ • Quality Control   │    │ • Email Alerts │   │
│  └─────────────┘    └─────────────────────┘    └─────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

**Core Function**: Deadline extraction, ICS calendar generation with password protection, email notifications
**Goal**: Pure deadline data service - collect, list, alert only

### Technology Stack

**Backend API & Data Processing**
- **Python Flask**: API backend for deadline processing
- **PostgreSQL**: Database for deadline storage
- **Requests**: HTTP client for EUR-Lex API calls
- **lxml**: XML processing for German deadline extraction
- **Automated extraction**: EUR-Lex JSON and German XML parsing

**Database**
- **PostgreSQL**: Database for deadline storage
- **Basic queries**: Deadline lookups and calendar generation
- **Automated backups**: Backup strategy

**Processing**
- **Date extraction**: Regex patterns for date extraction
- **AI validation**: Automated validation
- **Scheduled updates**: Data collection

**Email Integration**
- **Email Service**: Email list management and notifications
- **Email templates**: Pure deadline notifications (collect, list, alert only)

**Infrastructure**
- **Hetzner**: EU server hosting
- **Hosting**: Flask API + PostgreSQL
- **Domain**: 50data.eu with SSL
- **Deployment**: Git-based updates

## 🔌 API Design

### REST API Endpoints

**Core API Structure:**
```python
# Deadline Management API
GET  /api/deadlines              # List deadlines with filtering
GET  /api/deadlines/{id}         # Get specific deadline

# ICS Calendar API
GET  /ics                        # Password-protected ICS calendar
GET  /ics/{password}             # ICS calendar with password

# Export API
GET  /api/export/csv             # Export CSV data
GET  /api/export/json            # Export JSON data

# Source Status API
GET  /api/sources/status         # Data source health
GET  /api/sources/stats          # Collection statistics
```

**Response Format:**
```python
# Standard deadline object
{
  "id": "uuid",
  "date": "2025-01-01",
  "title": "eRechnung B2G mandatory",
  "description": "Electronic invoicing mandatory for B2G",
  "country": "DE",
  "source": "German Federal Ministry",
  "deadline_type": "implementation",
  "ai_validated": true,
  "confidence_score": 0.95,
  "created_at": "2024-12-20T10:00:00Z",
  "updated_at": "2024-12-20T10:00:00Z"
}
```

## 📁 ICS Password Protection System

### Password-Protected Calendar Access

**Password-Protected Access:**
- ICS calendar access with simple password
- Essential EU deadlines
- /ics/{password} endpoint

**Password Distribution:**
- Passwords shared via email
- Simple token-based system

**Implementation:**
```python
class ICSProtection:
    """Password protection for ICS calendar downloads"""

    def __init__(self):
        self.valid_passwords = ["deadline2024", "compliance2024"]

    def validate_access(self, password):
        """Validate password for ICS calendar access"""
        return password in self.valid_passwords

    def generate_ics_calendar(self, password):
        """Generate ICS calendar if password is valid"""
        if self.validate_access(password):
            return self.create_calendar()
        return None
```

## 📁 Claude Code Automated Workflow (MD/YAML/CSV/Python)

**Claude Code Will Build:** Python scripts + automated data management system

### Project File Structure
```
50data/
├── config/
│   ├── settings.yml              # Main configuration
│   ├── sources.yml               # Data source settings
│   └── email_templates.yml       # Email configuration
├── data/
│   ├── deadlines.csv             # All compliance deadlines
│   ├── pending_review.csv        # Extracted deadlines awaiting approval
│   └── approved_deadlines.csv    # Validated deadlines
├── scripts/                      # Python scripts (Claude Code writes)
│   ├── extract_deadlines.py      # Extract from EUR-Lex/German sources
│   ├── approve_deadlines.py      # Approve pending deadlines
│   ├── generate_calendar.py      # Create ICS calendar file
│   ├── send_notifications.py     # Email updates
│   └── analytics.py              # Generate reports
├── docs/
│   ├── README.md                 # Project documentation
│   ├── deadline_sources.md       # Source documentation
│   └── daily_notes.md            # Your daily notes/observations
└── output/
    ├── calendar.ics              # Generated calendar file
    └── reports/                  # Analytics and reports
```

### Claude Code Automated Workflow
```bash
# 1. Extract new deadlines automatically
python scripts/extract_deadlines.py    # Claude Code script extracts new deadlines
# Creates: data/pending_review.csv

# 2. AI validation and approval (automated)
python scripts/approve_deadlines.py   # AI validates and moves approved items
# Updates: data/deadlines.csv

# 3. Generate calendar and notify users (automated)
python scripts/generate_calendar.py   # Creates calendar.ics
python scripts/send_notifications.py  # Sends email updates

# 4. Generate analytics (automated)
python scripts/analytics.py           # Generates reports in Markdown
```

### File-Based Management Components

**YAML Configuration (Claude Code Manages):**
```yaml
# config/settings.yml
api_keys:
  eur_lex: "your-api-key"
  email_api: "your-api-key"

extraction_settings:
  auto_approve_confidence: 0.85
  countries: ["DE", "EU"]
  deadline_types: ["implementation", "compliance", "reporting"]

email_settings:
  send_frequency: "weekly"
  template: "pure_deadline_update"
```

**CSV Data Management (Claude Code Manages):**
```csv
# data/pending_review.csv
date,title,description,source,country,confidence,status
2025-01-01,eRechnung B2G mandatory,Electronic invoicing mandatory for B2G,German Ministry,DE,0.95,pending
2025-06-01,AI Act compliance deadline,AI systems must comply with new regulations,EUR-Lex,EU,0.87,pending
```

**User Role:** Business direction + Claude Code handles all technical implementation

## 📥 EU Legal Data Sources (Claude Code Implementation)

### Source 1: EUR-Lex API (EU-wide)

```python
class EURLexDeadlineExtractor:
    """
    Simple EUR-Lex integration for deadline extraction
    """

    BASE_URL = "https://eur-lex.europa.eu/api"

    def __init__(self):
        self.session = requests.Session()

    def extract_deadlines_from_document(self, celex_number: str):
        """
        Approach for EUR-Lex:
        1. Fetch specific high-value documents
        2. Simple regex date extraction
        3. AI validation of results
        4. Pure deadline data only - no analysis
        """

    TARGET_DOCUMENTS = [
        "32024R1689",  # AI Act
        "32014L0055",  # eRechnung directive
        "32016R0679",  # GDPR
        # Add more automatically as needed
    ]

    def simple_date_extraction(self, text: str):
        """Basic regex patterns"""
        patterns = [
            r"shall apply from (\d{1,2} \w+ \d{4})",
            r"(\d{1,2} \w+ \d{4})",  # General date pattern
            r"(\d{4}-\d{2}-\d{2})"   # ISO format
        ]

        # Extract dates without commentary
        deadlines = []
        for pattern in patterns:
            matches = re.findall(pattern, text)
            deadlines.extend(matches)

        return deadlines
```

### Source 2: German Legal Sources

```python
class GermanDeadlineCollector:
    """
    Basic German compliance deadline collection
    """

    def collect_erechnung_deadlines(self):
        """
        Approach for German deadlines:
        1. Automated extraction of key dates
        2. Official government sources
        3. Simple deadline list - no commentary
        4. Focus on eRechnung B2G/B2B deadlines
        """

    KNOWN_GERMAN_DEADLINES = [
        {
            "date": "2025-01-01",
            "title": "eRechnung B2G mandatory",
            "description": "Electronic invoicing mandatory for B2G transactions",
            "source": "German Federal Ministry of Finance",
            "type": "implementation_deadline"
        },
        {
            "date": "2028-01-01",
            "title": "eRechnung B2B mandatory",
            "description": "Electronic invoicing mandatory for B2B transactions",
            "source": "German Federal Ministry of Finance",
            "type": "implementation_deadline"
        }
        # Add more automatically extracted deadlines
    ]

    def validate_deadline_accuracy(self, deadline: dict):
        """AI validation workflow for quality control"""
        # Always AI verification
        # No automated acceptance of deadline data
        pass
```

## 🔄 Processing Pipeline (Deadline-Focused)

### Deadline Data Processing

```python
class DeadlineProcessingPipeline:
    """
    Simple deadline processing for compliance calendar
    """

    def __init__(self):
        self.email_service = EmailService()

    def process_legal_sources(self):
        """
        Simple approach:
        1. Fetch data from EUR-Lex and German sources
        2. Extract dates using regex patterns
        3. AI validation for accuracy
        4. Generate calendar and email notifications
        """

    def extract_and_validate_deadlines(self, source_data: str):
        # Step 1: Date extraction
        raw_deadlines = self.extract_dates(source_data)

        # Step 2: AI validation (always required)
        validated_deadlines = self.ai_validation(raw_deadlines)

        # Step 3: Store validated deadlines
        self.store_deadlines(validated_deadlines)

        # Step 4: Generate notifications
        self.generate_notifications(validated_deadlines)

        return {
            "success": True,
            "deadlines_processed": len(validated_deadlines),
            "validation_required": len(raw_deadlines),
            "notifications_sent": self.get_notification_count()
        }

    def generate_notifications(self, deadlines: List[dict]):
        """
        Generate calendar and email notifications
        - ICS calendar file generation
        - Email notifications
        - Pure deadline data only - collect, list, alert only
        """

        # Generate ICS calendar
        ics_content = self.generate_ics_calendar(deadlines)

        # Send email notifications (pure data only)
        for deadline in deadlines:
            self.email_service.send_deadline_notification(
                deadline_date=deadline['date'],
                title=deadline['title'],
                # NO commentary or analysis ever sent
                pure_data_only=True
            )

    def ai_validation(self, deadlines: List[dict]):
        """Always require AI validation for deadline accuracy"""
        # All deadlines must be AI verified
        # Claude Code automated validation of extracted dates
        return []  # Return only AI verified deadlines
```

## 📅 Calendar & Email System

### ICS Calendar Generation

```python
class SimpleCalendarGenerator:
  """
  Basic ICS calendar generation for compliance deadlines
  """

  def __init__(self):
    self.timezone = pytz.timezone('Europe/Berlin')

  def generate_compliance_calendar(self, deadlines: List[dict]):
    """
    Simple approach:
    1. Generate standard ICS calendar format
    2. Include all validated deadlines
    3. Add basic reminders (7 days, 1 day)
    4. No commentary - pure deadline data only
    """

    ics_content = [
      "BEGIN:VCALENDAR",
      "VERSION:2.0",
      "PRODID:-//50Data//EU Compliance Deadlines//EN",
      "METHOD:PUBLISH"
    ]

    for deadline in deadlines:
      event = self.create_deadline_event(deadline)
      ics_content.append(event)

    ics_content.append("END:VCALENDAR")
    return "\n".join(ics_content)

  def create_deadline_event(self, deadline: dict):
    """
    Basic ICS event for compliance deadline
    - Pure deadline data only
    - No analysis or commentary
    - Standard reminders
    """

    return f"""BEGIN:VEVENT
UID:{deadline['id']}@50data.eu
DTSTART;VALUE=DATE:{deadline['date']}
SUMMARY:{deadline['title']}
DESCRIPTION:{deadline['description']}\\n\\nSource: {deadline['source']}\\n\\nPowered by 50Data
CATEGORIES:EU_COMPLIANCE
URL:{deadline.get('source_url', '')}

BEGIN:VALARM
TRIGGER:-P7D
ACTION:DISPLAY
DESCRIPTION:EU Compliance deadline in 7 days: {deadline['title']}
END:VALARM

BEGIN:VALARM
TRIGGER:-P1D
ACTION:DISPLAY
DESCRIPTION:EU Compliance deadline tomorrow: {deadline['title']}
END:VALARM

END:VEVENT"""
```

### Email Integration

```python
class EmailService:
  """
  Simple Email Service integration for deadline notifications
  """

  def __init__(self, api_key: str):
    self.api_key = api_key

  def send_deadline_notification(self, deadline_date: str, title: str):
    """
    Send pure deadline notification
    - NO commentary or analysis ever
    - Pure deadline data only
    - Simple email template
    """

    email_content = f"""
EU Compliance Deadline: {title}

Date: {deadline_date}
Source: Official EU/German legal sources

---
50Data - Pure EU compliance deadline data
Unsubscribe: [link]
"""

    # Email Service API call to send email
    # Pure data delivery - collect, list, alert only

  def add_subscriber(self, email: str):
    """Add subscriber to deadline notification list"""
    # Email subscription

  def segment_by_country(self, subscriber_email: str, countries: List[str]):
    """Tag subscribers by country interest for targeted notifications"""
    # Email tagging for segmentation
```

## 🚀 Deployment (EU Compliant)

### Infrastructure Setup

```bash
# Hetzner VPS setup
# Server setup
apt update && apt upgrade -y
apt install python3 python3-pip nginx postgresql postgresql-contrib -y

# Install Python dependencies
pip3 install flask requests beautifulsoup4 icalendar pytz psycopg2-binary lxml

# Application structure
/var/www/50data/
├── app.py              # Main Flask application
├── deadline_extractor.py # EUR-Lex integration
├── german_xml_processor.py # German legal XML processing
├── calendar_generator.py # ICS generation
├── email_service.py    # Email Service integration
├── data/
│   ├── german_xml/     # German legal XML storage
│   ├── eur_lex_cache/  # EUR-Lex document cache
│   └── backups/        # Database and file backups
├── migrations/         # Database schema migrations
└── static/            # Website assets
```

### Database Schema

```sql
-- PostgreSQL database for EU compliance deadlines
CREATE TABLE deadlines (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    -- Deadline information
    deadline_date DATE NOT NULL,
    title VARCHAR(500) NOT NULL,
    description TEXT,
    deadline_type VARCHAR(50), -- implementation, compliance, reporting

    -- Source information
    source VARCHAR(200) NOT NULL, -- EUR-Lex, German Ministry, etc.
    source_url VARCHAR(500),
    country VARCHAR(5), -- DE, EU, etc.
    document_id VARCHAR(100), -- CELEX number or German doc ID

    -- Processing metadata
    extraction_method VARCHAR(50), -- ai, api, regex, xml_parser
    ai_validated BOOLEAN DEFAULT FALSE,
    confidence_score DECIMAL(3,2), -- 0.00 to 1.00

    -- Pure data only - no commentary fields
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Source documents table
CREATE TABLE source_documents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    document_type VARCHAR(50), -- eur_lex, german_xml
    document_identifier VARCHAR(200), -- CELEX, German law ID
    title VARCHAR(1000),
    publication_date DATE,
    content_hash VARCHAR(64), -- SHA256 for change detection
    raw_content TEXT, -- Full document content
    processing_status VARCHAR(20) DEFAULT 'pending', -- pending, processed, failed
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Email subscribers
CREATE TABLE subscribers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(200) NOT NULL UNIQUE,
    countries JSONB, -- JSON array of country interests
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ICS access tokens
CREATE TABLE ics_tokens (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    token VARCHAR(100) NOT NULL UNIQUE,
    access_type VARCHAR(20) DEFAULT 'standard',
    expires_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_deadlines_date ON deadlines(deadline_date);
CREATE INDEX idx_subscribers_email ON subscribers(email);
CREATE INDEX idx_ics_tokens ON ics_tokens(token);
```

## 📊 Success Metrics

### Core Success Criteria
```python
success_criteria = {
  "basic_functionality": {
    "calendar_generation": "Working password-protected ICS downloads",
    "email_notifications": "Email integration working",
    "data_extraction": "EUR-Lex + German XML processing"
  },
  "content_policy": {
    "pure_data_only": "Never publish commentary or analysis",
    "deadline_changes": "Collect, list, alert only - no interpretation"
  }
}
```

---

**Company**: Blinktank GmbH, Berlin | **Founder**: Andreas Dahrendorf (Non-Technical)
**Product**: 50Data EU Compliance Deadline Service | **Domain**: 50data.eu
**Content Policy**: Pure deadline data only - collect, list, alert - no interpretation ever