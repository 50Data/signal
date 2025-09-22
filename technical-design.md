# Technical Architecture - 50Data EU Compliance Deadline Service

*Blinktank GmbH | Simple MVP Architecture*
*Three-Phase Evolution: Free MVP → Paid Tiers → API Platform*

## 🏗️ MVP System Overview

### Simple MVP Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                   50DATA EU DEADLINE SERVICE MVP                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────┐    ┌─────────────────────┐    ┌─────────────────┐   │
│  │DATA SOURCES │───▶│  DEADLINE EXTRACTION│───▶│  CALENDAR & EMAIL│   │
│  │             │    │                     │    │                 │   │
│  │ • EUR-Lex   │    │ • Date Parsing      │    │ • ICS Generation│   │
│  │ • German    │    │ • Legal Text NLP    │    │ • Kit/ConvertKit│   │
│  │   Sources   │    │ • Manual Validation │    │   Integration   │   │
│  │ • RSS Feeds │    │ • Quality Control   │    │ • Paddle Billing│   │
│  └─────────────┘    └─────────────────────┘    └─────────────────┘   │
│          │                      │                        │           │
│          ▼                      ▼                        ▼           │
│  ┌─────────────┐    ┌─────────────────────┐    ┌─────────────────┐   │
│  │API ACCESS   │    │   DEADLINE ENGINE   │    │  NOTIFICATION   │   │
│  │LAYER        │    │                     │    │    SYSTEM       │   │
│  │             │    │ • Date Validation   │    │ • Email Alerts  │   │
│  │• EUR-Lex API│    │ • Change Detection  │    │ • Calendar Sync │   │
│  │• Manual     │    │ • Deadline Tracking │    │ • Pure Data     │   │
│  │  Research   │    │ • No Commentary     │    │ • No Editorial  │   │
│  │• RSS Parsing│    │ • Pure Dates Only   │    │ • Kit/ConvertKit│   │
│  └─────────────┘    └─────────────────────┘    └─────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

**MVP Scope**: Deadline extraction, calendar generation, email notifications, simple billing
**Goal**: Simple deadline service with Kit/ConvertKit + Paddle integration

### MVP Technology Stack (Simple)

**Backend (Enhanced & Reliable)**
- **Python Flask**: Lightweight web framework for API and website
- **PostgreSQL**: Robust database for deadline storage and large datasets
- **BeautifulSoup**: HTML parsing for legal documents
- **Requests**: HTTP client for EUR-Lex API calls
- **lxml**: Efficient XML processing for 15GB German legal data

**Database (Enhanced)**
- **PostgreSQL**: Production database for deadlines, documents, and user info
- **Full-text search**: Built-in search capabilities for legal document content
- **Proper indexing**: Optimized queries for deadline lookups and filtering
- **Automated backups**: PostgreSQL dump + Hetzner volume backup strategy

**Processing (Basic)**
- **Python NLP**: Simple date extraction with regex patterns
- **Manual validation**: Quality control for extracted deadlines
- **Cron jobs**: Scheduled updates from legal sources
- **Basic logging**: Simple error tracking and monitoring

**Email & Billing Integration**
- **Kit/ConvertKit API**: Email list management and notifications
- **Paddle API**: EU-compliant billing and subscription management
- **Webhook handling**: Basic webhook receivers for Paddle events
- **Email templates**: Pure deadline notifications (no commentary)

**Infrastructure (EU-Compliant & Enhanced)**
- **Hetzner CX31**: €25/month EU server (2 vCPU, 8GB RAM, 80GB SSD)
- **Enhanced hosting**: Flask API + PostgreSQL + document storage
- **Domain**: 50data.eu with Let's Encrypt SSL
- **Storage capacity**: 80GB for German XML (15GB) + EUR-Lex cache + backups
- **Deployment**: Git-based with database migrations
- **Monitoring**: PostgreSQL metrics + application logs + uptime monitoring

## 📥 EU Legal Data Sources (Deadline-Focused)

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
        MVP approach for EUR-Lex:
        1. Fetch specific high-value documents
        2. Simple regex date extraction
        3. Manual validation of results
        4. Pure deadline data only - no analysis
        """

    TARGET_DOCUMENTS = [
        "32024R1689",  # AI Act
        "32014L0055",  # eRechnung directive
        "32016R0679",  # GDPR
        # Add more manually as needed
    ]

    def simple_date_extraction(self, text: str):
        """Basic regex patterns for MVP"""
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

### Source 2: German Legal Sources (eRechnung Focus)

```python
class GermanDeadlineCollector:
    """
    Basic German compliance deadline collection
    """

    def collect_erechnung_deadlines(self):
        """
        MVP approach for German deadlines:
        1. Manual research of key dates
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
        # Add more manually researched deadlines
    ]

    def validate_deadline_accuracy(self, deadline: dict):
        """Manual validation workflow for quality control"""
        # Always manual verification for MVP
        # No automated acceptance of deadline data
        pass

## 🔄 Simple Processing Pipeline (Deadline-Focused)

### Deadline Data Processing

```python
class DeadlineProcessingPipeline:
    """
    Simple deadline processing for compliance calendar
    """

    def __init__(self):
        self.kit_client = KitConvertKitClient()
        self.paddle_client = PaddleClient()

    def process_legal_sources(self):
        """
        Simple MVP approach:
        1. Fetch data from EUR-Lex and German sources
        2. Extract dates using regex patterns
        3. Manual validation for accuracy
        4. Generate calendar and email notifications
        """

    def extract_and_validate_deadlines(self, source_data: str):
        # Step 1: Simple date extraction
        raw_deadlines = self.extract_dates(source_data)

        # Step 2: Manual validation (always required for MVP)
        validated_deadlines = self.manual_validation(raw_deadlines)

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
        - Kit/ConvertKit email notifications
        - Pure deadline data only - no commentary
        """

        # Generate ICS calendar
        ics_content = self.generate_ics_calendar(deadlines)

        # Send Kit/ConvertKit notifications (pure data only)
        for deadline in deadlines:
            self.kit_client.send_deadline_notification(
                deadline_date=deadline['date'],
                title=deadline['title'],
                # NO commentary or analysis ever sent
                pure_data_only=True
            )

    def manual_validation(self, deadlines: List[dict]):
        """Always require manual validation for deadline accuracy"""
        # MVP: All deadlines must be manually verified
        # No automated acceptance of extracted dates
        return []  # Return only manually verified deadlines

## 📅 Calendar & Email System (Simple)

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
    Simple MVP approach:
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

### Kit/ConvertKit Email Integration

```python
class KitConvertKitClient:
  """
  Simple Kit/ConvertKit integration for deadline notifications
  """

  def __init__(self, api_key: str):
    self.api_key = api_key
    self.base_url = "https://api.convertkit.com/v3"

  def send_deadline_notification(self, deadline_date: str, title: str):
    """
    Send pure deadline notification via Kit/ConvertKit
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

    # Kit/ConvertKit API call to send email
    # Pure data delivery - no editorial content

  def add_subscriber(self, email: str):
    """Add subscriber to deadline notification list"""
    # Simple Kit/ConvertKit subscription

  def segment_by_country(self, subscriber_email: str, countries: List[str]):
    """Tag subscribers by country interest for targeted notifications"""
    # Kit/ConvertKit tagging for segmentation

## 🚀 Simple Deployment (EU Compliant)

### Basic Infrastructure Setup

```bash
# Simple Hetzner VPS setup for MVP
# €10/month CX11 instance (EU region)

# Enhanced server setup for CX31
apt update && apt upgrade -y
apt install python3 python3-pip nginx postgresql postgresql-contrib -y

# Install Python dependencies
pip3 install flask requests beautifulsoup4 icalendar pytz psycopg2-binary lxml

# Enhanced Flask application structure
/var/www/50data/
├── app.py              # Main Flask application
├── deadline_extractor.py # EUR-Lex integration
├── german_xml_processor.py # German legal XML processing
├── calendar_generator.py # ICS generation
├── kit_client.py       # Kit/ConvertKit integration
├── paddle_client.py    # Paddle billing integration
├── data/
│   ├── german_xml/     # 15GB German legal XML storage
│   ├── eur_lex_cache/  # EUR-Lex document cache
│   └── backups/        # Database and file backups
├── migrations/         # Database schema migrations
└── static/            # Website assets
```

### Paddle Billing Integration

```python
class PaddleBillingClient:
  """
  Simple Paddle integration for EU-compliant billing
  """

  def __init__(self, vendor_id: str, auth_code: str):
    self.vendor_id = vendor_id
    self.auth_code = auth_code
    self.base_url = "https://vendors.paddle.com/api/2.0"

  def create_subscription_checkout(self, plan_id: str, customer_email: str):
    """
    Create Paddle checkout for EU subscription
    - Automatic VAT calculation
    - EU payment methods
    - GDPR compliant billing
    """

    checkout_data = {
      "vendor_id": self.vendor_id,
      "product_id": plan_id,
      "customer_email": customer_email,
      "marketing_consent": False,  # No marketing by default
      "return_url": "https://50data.eu/success",
      "webhook_url": "https://50data.eu/paddle-webhook"
    }

    # Paddle handles all EU compliance automatically

  def handle_webhook(self, webhook_data: dict):
    """
    Handle Paddle subscription webhooks
    - Subscription created/updated/cancelled
    - Update Kit/ConvertKit subscription status
    - Grant/revoke access to premium countries
    """

    if webhook_data['alert_name'] == 'subscription_created':
      # Add to premium Kit/ConvertKit segment
      # Enable multi-country calendar access
      pass

    elif webhook_data['alert_name'] == 'subscription_cancelled':
      # Remove from premium segment
      # Revert to basic calendar access
      pass

### Production Configuration (Enhanced & EU-Compliant)

production_setup = {
  "hosting": "Hetzner Cloud CX31 (Nuremberg, Germany - EU)",
  "specs": "2 vCPU, 8GB RAM, 80GB SSD - €25/month",
  "database": "PostgreSQL with full-text search and proper indexing",
  "storage": "80GB SSD for 15GB German XML + EUR-Lex cache + backups",
  "domain": "50data.eu with Let's Encrypt SSL",
  "email": "Kit/ConvertKit (GDPR compliant email service)",
  "billing": "Paddle (automatic EU VAT compliance)",
  "monitoring": "PostgreSQL metrics + application logs + uptime monitoring",
  "backup": "Automated PostgreSQL dumps + file system backups",
  "deployment": "Git-based with database migrations and zero-downtime updates"
}
```

## 🛣️ Evolution Roadmap

### Mid-state Architecture (Months 6-12)
- **Multi-Country APIs**: Poland, Austria, Netherlands legal sources
- **Paddle Tiers**: Subscription billing for premium country coverage
- **Advanced Kit/ConvertKit**: Segmented notifications by country/topic
- **API Access**: Basic API for developers and legal tech companies
- **Webhook System**: Real-time deadline change notifications

### End-state Architecture (Year 2+)
- **Complete EU Coverage**: All EU-27 countries with deadline tracking
- **Real-time Updates**: Live monitoring of legal source changes
- **White-label Platform**: Calendar embedding for legal software
- **Enterprise API**: High-volume access for legal tech ecosystem
- **Advanced Filtering**: Granular deadline categorization and search
- **Mobile Optimization**: Responsive design for mobile deadline alerts

## 🔧 MVP Development Requirements

### Simple MVP Tasks (4 weeks)
```python
mvp_implementation = {
  "week1": {
    "backend": "Flask application + PostgreSQL database setup",
    "sources": "EUR-Lex API integration + German XML processing",
    "infrastructure": "Hetzner CX31 deployment with 80GB storage"
  },
  "week2": {
    "processing": "Simple deadline extraction + manual validation",
    "calendar": "ICS calendar generation + download system",
    "email": "Kit/ConvertKit integration for notifications"
  },
  "week3": {
    "billing": "Paddle integration for future subscriptions",
    "website": "Simple website with download functionality",
    "validation": "Manual quality control of all deadlines"
  },
  "week4": {
    "testing": "Calendar compatibility testing across platforms",
    "content": "Pure deadline data validation (no commentary)",
    "launch": "Production deployment + initial user acquisition"
  }
}
```

### Database Schema for Enhanced Deadline Service

```sql
-- PostgreSQL database for EU compliance deadlines with enhanced storage
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
    country VARCHAR(5), -- DE, EU, FR, etc.
    document_id VARCHAR(100), -- CELEX number or German doc ID

    -- Processing metadata
    extraction_method VARCHAR(50), -- manual, api, regex, xml_parser
    manually_validated BOOLEAN DEFAULT FALSE,
    confidence_score DECIMAL(3,2), -- 0.00 to 1.00

    -- No commentary fields - pure data only
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Source documents table for storing large XML content
CREATE TABLE source_documents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    document_type VARCHAR(50), -- eur_lex, german_xml, rss
    document_identifier VARCHAR(200), -- CELEX, German law ID
    title VARCHAR(1000),
    publication_date DATE,
    content_hash VARCHAR(64), -- SHA256 for change detection
    raw_content TEXT, -- Full document content
    processing_status VARCHAR(20) DEFAULT 'pending', -- pending, processed, failed
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Kit/ConvertKit subscribers (minimal data)
CREATE TABLE subscribers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(200) NOT NULL UNIQUE,

    -- Subscription preferences
    countries JSONB, -- JSON array of country interests
    kit_subscriber_id VARCHAR(50), -- Kit/ConvertKit ID

    -- Paddle billing (when applicable)
    paddle_subscription_id VARCHAR(50),
    subscription_status VARCHAR(20) DEFAULT 'free', -- free, basic, premium

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Performance indexes for enhanced queries
CREATE INDEX idx_deadlines_date ON deadlines(deadline_date);
CREATE INDEX idx_deadlines_country ON deadlines(country);
CREATE INDEX idx_deadlines_type ON deadlines(deadline_type);
CREATE INDEX idx_deadlines_validated ON deadlines(manually_validated);
CREATE INDEX idx_subscribers_email ON subscribers(email);
CREATE INDEX idx_source_docs_type ON source_documents(document_type);
CREATE INDEX idx_source_docs_identifier ON source_documents(document_identifier);
CREATE INDEX idx_source_docs_hash ON source_documents(content_hash);

-- Full-text search index for deadline content
CREATE INDEX idx_deadlines_fts ON deadlines USING gin(to_tsvector('english', title || ' ' || description));
```

## 📊 MVP Success Metrics

### Technical Performance Metrics
```python
technical_metrics = {
  "deadline_processing": {
    "extraction_accuracy": "90%+ manual validation success",
    "calendar_generation": "Sub-10 second ICS file creation",
    "email_delivery": "99%+ Kit/ConvertKit delivery rate",
    "source_coverage": "EUR-Lex + German sources"
  },

  "website": {
    "load_time": "Sub-3 second page loading",
    "calendar_download": "One-click ICS download",
    "mobile_responsive": "Works on all devices",
    "uptime": "99%+ Hetzner hosting reliability"
  },

  "integrations": {
    "kit_convertkit": "Real-time email notifications",
    "paddle_billing": "EU-compliant subscription handling",
    "calendar_compatibility": "Works with Outlook, Google, Apple",
    "gdpr_compliance": "EU data residency + privacy"
  }
}
```

### Business Success Metrics
```python
business_metrics = {
  "mvp_phase": {
    "calendar_downloads": "100 downloads in Month 3",
    "email_subscribers": "50 Kit/ConvertKit subscribers",
    "user_feedback": "Collect validation for monetization",
    "deadline_accuracy": "90%+ user-reported accuracy"
  },

  "growth": {
    "seo_ranking": "Top 10 for 'EU compliance deadlines'",
    "email_growth": "20% monthly Kit/ConvertKit growth",
    "word_of_mouth": "Organic referrals from compliance teams",
    "paddle_conversion": "10-20% free to paid conversion"
  },

  "content_policy": {
    "pure_data_only": "Never publish commentary or analysis",
    "deadline_changes": "Only dates/deadlines/changes notifications",
    "no_editorial": "No blog posts, opinions, or market analysis",
    "simple_messaging": "Basic deadline notifications via Kit/ConvertKit"
  }
}
```

---

**Company**: Blinktank GmbH, Berlin | **Founder**: Andreas Dahrendorf
**Product**: 50Data EU Compliance Deadline Service | **Domain**: 50data.eu
**Strategy**: Three-phase evolution (Free MVP → Paid Tiers → API Platform)
**MVP**: Simple deadline service with Kit/ConvertKit + Paddle integration
**Investment**: €500/month basic hosting + APIs | **Risk**: Low risk, proven approach
**Next Steps**: Build simple deadline service in 4 weeks, target 100 downloads Month 3
**Content Policy**: Pure deadline data only - never editorial content or commentary