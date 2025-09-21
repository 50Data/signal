# Technical Architecture - 50Data EU Compliance Platform

*Blinktank GmbH | Hetzner Cloud EU Infrastructure*

## 🏗️ System Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    COMPLIANCE CALENDAR SYSTEM               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐    ┌─────────────────┐    ┌─────────────┐ │
│  │   INGESTION   │───▶│    PROCESSING    │───▶│   DELIVERY  │ │
│  │              │    │                 │    │             │ │
│  │ • EUR-Lex    │    │ • Date Extract  │    │ • ICS Feeds │ │
│  │ • National   │    │ • NLP Analysis  │    │ • REST API  │ │
│  │ • RSS Feeds  │    │ • Classification│    │ • Webhooks  │ │
│  │ • Scheduled  │    │ • Validation    │    │ • Dashboard │ │
│  └──────────────┘    └─────────────────┘    └─────────────┘ │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                    DATA LAYER                           │ │
│  │ [Raw Documents] [Processed Data] [User Configs] [Feeds] │ │
│  └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### Technology Stack (EU-Focused)

**Backend Framework**
- **FastAPI**: High-performance async Python framework
- **Pydantic**: Data validation and serialization (GDPR-compliant)
- **SQLAlchemy**: ORM with async support
- **Alembic**: Database migrations

**Database & Storage (EU Data Centers)**
- **PostgreSQL**: Primary database (compliance data, users) - Hetzner managed
- **Redis**: Caching and background job queue - Hetzner
- **Hetzner Object Storage**: Raw document storage (EU data residency)
- **TimescaleDB**: Time-series data for analytics

**Processing & NLP (Multi-language EU)**
- **spaCy**: Multi-language NLP (DE, FR, EN, ES, IT, PL support)
- **Transformers**: Fine-tuned BERT for EU compliance classification
- **Celery**: Asynchronous task processing
- **scikit-learn**: Machine learning pipelines

**Infrastructure & Deployment (Hetzner Cloud)**
- **Docker**: Containerization
- **Hetzner Cloud**: Primary hosting (EU data sovereignty)
- **Hetzner Load Balancer**: High availability
- **GitHub Actions**: CI/CD pipeline
- **Sentry**: Error monitoring (EU instance)

## 📥 Data Ingestion Layer

### Priority 1: EUR-Lex API Collector

```python
class EURLexCollector:
    """Primary source for EU-level legislation"""

    BASE_URL = "https://eur-lex.europa.eu/api"
    RATE_LIMIT = "10 requests/second"

    async def collect_compliance_documents(self, since_date: str):
        """
        Collection strategy:
        1. Search by document type and date
        2. Filter for compliance-relevant documents
        3. Extract full text and metadata
        4. Queue for processing
        """

    FOCUS_DOCUMENT_TYPES = {
        "directives": "32{year}L{number}",  # EU Directives
        "regulations": "32{year}R{number}", # EU Regulations
        "decisions": "32{year}D{number}",   # EU Decisions
        "recommendations": "32{year}H{number}"
    }

    COMPLIANCE_KEYWORDS = [
        "implementation date", "application date", "compliance",
        "deadline", "transposition", "effective date", "reporting"
    ]
```

### Priority 2: National API Collectors

```python
class NationalCollectorManager:
    """Manages country-specific legal data sources"""

    def __init__(self):
        self.collectors = {
            "france": LegifranCollector(),    # OAuth 2.0 API
            "poland": PolandCollector(),      # Open API
            "austria": AustriaRISCollector(), # Open API
            "netherlands": NetherlandsCollector(),
            "czech": CzechCollector(),        # SPARQL endpoint
            "luxembourg": LuxembourgCollector()
        }

    async def collect_all_updates(self):
        """Run all collectors in parallel with rate limiting"""
        tasks = []
        for country, collector in self.collectors.items():
            task = asyncio.create_task(
                collector.collect_with_backoff()
            )
            tasks.append(task)

        results = await asyncio.gather(*tasks, return_exceptions=True)
        return self.process_collection_results(results)
```

### Priority 3: RSS/XML Monitoring

```python
class RSSMonitor:
    """Monitor RSS feeds for compliance updates"""

    FEEDS = {
        "erechnung": "https://www.inoreader.com/stream/user/1005663366/tag/eRechnung",
        "vida": "https://www.inoreader.com/stream/user/1005663366/tag/ViDA"
    }

    async def monitor_feeds(self):
        """
        Monitoring strategy:
        1. Check feeds every hour
        2. Parse new entries
        3. Extract links to legal documents
        4. Queue for full document retrieval
        """

    def extract_compliance_signals(self, feed_entry):
        """
        Signal detection:
        - Keywords: deadline, implementation, compliance
        - Date patterns in titles/descriptions
        - Links to official legal sources
        """
```

## 🧠 NLP Processing Pipeline

### Stage 1: Document Preprocessing

```python
class DocumentPreprocessor:
    """Clean and prepare legal documents for analysis"""

    def __init__(self):
        self.language_detector = langdetect.detect
        self.cleaners = {
            "html": BeautifulSoup,
            "pdf": PyPDF2,
            "xml": lxml
        }

    async def preprocess_document(self, raw_document):
        """
        Preprocessing steps:
        1. Detect language and document format
        2. Extract clean text from HTML/PDF/XML
        3. Segment into logical sections
        4. Identify compliance-relevant sections
        """

    def segment_document(self, text: str):
        """
        Segmentation strategy:
        - Article/section boundaries
        - Implementation date sections
        - Compliance requirement sections
        - Deadline and timeline sections
        """
```

### Stage 2: Date Extraction Engine

```python
class ComplianceDateExtractor:
    """Extract and classify compliance dates from legal text"""

    def __init__(self):
        self.nlp_models = {
            "en": spacy.load("en_core_web_lg"),
            "fr": spacy.load("fr_core_news_lg"),
            "de": spacy.load("de_core_news_lg")
        }

    async def extract_dates(self, text: str, language: str):
        """
        Multi-stage extraction:
        1. Regex pattern matching
        2. Named entity recognition
        3. Dependency parsing for context
        4. Relative date resolution
        """

    DATE_PATTERNS = {
        "absolute": [
            r"(\d{1,2}\s+\w+\s+\d{4})",      # "15 January 2025"
            r"(\d{4}-\d{2}-\d{2})",          # "2025-01-15"
            r"(\d{1,2}/\d{1,2}/\d{4})"       # "15/01/2025"
        ],
        "relative": [
            r"(\d+)\s+months?\s+after\s+(.+)",
            r"not\s+later\s+than\s+(\d+\s+\w+\s+\d{4})",
            r"(\d+)\s+days?\s+from\s+(.+)"
        ],
        "contextual": [
            r"shall\s+apply\s+from\s+(\d+\s+\w+\s+\d{4})",
            r"comes?\s+into\s+force\s+on\s+(\d+\s+\w+\s+\d{4})",
            r"transpose\s+.*\s+by\s+(\d+\s+\w+\s+\d{4})"
        ]
    }
```

### Stage 3: Compliance Classification

```python
class ComplianceClassifier:
    """Classify extracted dates by compliance type and urgency"""

    def __init__(self):
        # Fine-tuned BERT model for compliance classification
        self.classifier = AutoModelForSequenceClassification.from_pretrained(
            "compliance-bert-classifier"
        )

    async def classify_deadline(self, text_chunk: str, extracted_date: str):
        """
        Classification categories:
        - implementation_deadline
        - compliance_deadline
        - reporting_deadline
        - transposition_deadline
        - phase_in_date
        - exemption_expiry
        """

    CONTEXT_PATTERNS = {
        "implementation": {
            "keywords": ["shall apply", "comes into force", "takes effect"],
            "scope": "legal_framework",
            "urgency": "high"
        },
        "compliance": {
            "keywords": ["must comply", "shall ensure", "compliance with"],
            "scope": "business_obligation",
            "urgency": "high"
        },
        "reporting": {
            "keywords": ["report by", "submit by", "notify"],
            "scope": "reporting_obligation",
            "urgency": "medium"
        }
    }
```

### Stage 4: Scope & Entity Recognition

```python
class ScopeRecognizer:
    """Identify geographic and business scope of compliance deadlines"""

    def extract_compliance_scope(self, text: str, date: str):
        """
        Scope extraction:
        - Geographic: EU-wide, specific countries, regions
        - Sector: Financial, healthcare, manufacturing, etc.
        - Size: SME thresholds, employee counts, revenue limits
        - Entity type: Private companies, public sector, etc.
        """

    SCOPE_PATTERNS = {
        "geographic": [
            r"in\s+(?:all|each)\s+Member\s+State[s]?",
            r"throughout\s+the\s+(?:European\s+)?Union",
            r"in\s+([A-Z][a-z]+(?:,\s*[A-Z][a-z]+)*)"
        ],
        "sector": [
            r"financial\s+institution[s]?",
            r"credit\s+institution[s]?",
            r"payment\s+service\s+provider[s]?",
            r"AI\s+system\s+provider[s]?"
        ],
        "size": [
            r"SMEs?|small\s+and\s+medium\s+enterprises?",
            r"undertakings\s+with\s+(?:more\s+than|over)\s+(\d+)\s+employees",
            r"turnover\s+exceeding\s+EUR\s+(\d+(?:,\d+)*)\s+(?:million|billion)"
        ]
    }
```

## 💾 Data Layer Architecture

### Database Schema

```sql
-- Core compliance deadlines table
CREATE TABLE compliance_deadlines (
    id UUID PRIMARY KEY,
    source_document_id UUID NOT NULL,
    extracted_date DATE NOT NULL,
    deadline_type VARCHAR(50) NOT NULL,
    title VARCHAR(500) NOT NULL,
    description TEXT,

    -- Scope information
    geographic_scope JSONB,  -- Countries, regions
    sector_scope JSONB,      -- Industries affected
    size_scope JSONB,        -- Company size criteria

    -- Metadata
    confidence_score FLOAT,
    extraction_method VARCHAR(50),
    context_snippet TEXT,

    -- Tracking
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    is_active BOOLEAN DEFAULT TRUE,

    CONSTRAINT valid_confidence CHECK (confidence_score BETWEEN 0 AND 1)
);

-- Source documents table
CREATE TABLE source_documents (
    id UUID PRIMARY KEY,
    source_type VARCHAR(50) NOT NULL,  -- eur_lex, france_api, rss
    document_identifier VARCHAR(200),   -- CELEX number, etc.
    title VARCHAR(1000),
    publication_date DATE,
    language VARCHAR(5),
    raw_content TEXT,
    processed_content TEXT,
    metadata JSONB,

    created_at TIMESTAMPTZ DEFAULT NOW(),
    last_processed_at TIMESTAMPTZ
);

-- User subscriptions
CREATE TABLE subscriptions (
    id UUID PRIMARY KEY,
    user_id UUID NOT NULL,
    plan_type VARCHAR(50) NOT NULL,

    -- Filter preferences
    countries TEXT[],
    sectors TEXT[],
    deadline_types TEXT[],
    company_size VARCHAR(20),
    lead_time_days INTEGER DEFAULT 30,

    -- Feed configuration
    feed_url VARCHAR(500) UNIQUE,
    api_key VARCHAR(100) UNIQUE,
    webhook_url VARCHAR(500),

    -- Billing
    stripe_subscription_id VARCHAR(100),
    status VARCHAR(20) DEFAULT 'active',

    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Performance indexes
CREATE INDEX idx_deadlines_date ON compliance_deadlines(extracted_date);
CREATE INDEX idx_deadlines_type ON compliance_deadlines(deadline_type);
CREATE INDEX idx_deadlines_scope ON compliance_deadlines USING GIN(geographic_scope);
CREATE INDEX idx_documents_source ON source_documents(source_type, document_identifier);
```

### Caching Strategy

```python
class CacheManager:
    """Redis-based caching for performance optimization"""

    def __init__(self):
        self.redis = redis.Redis()
        self.ttl_config = {
            "feed_generation": 3600,      # 1 hour
            "api_responses": 900,         # 15 minutes
            "compliance_queries": 1800,   # 30 minutes
            "user_preferences": 86400     # 24 hours
        }

    async def cache_compliance_feed(self, subscription_id: str, ics_content: str):
        """Cache generated ICS feeds to reduce processing load"""

    async def invalidate_cache(self, pattern: str):
        """Invalidate cache when new deadlines are processed"""
```

## 📅 ICS Feed Generation

### Feed Generator Architecture

```python
class ComplianceICSGenerator:
    """Generate RFC 5545 compliant ICS calendar feeds"""

    def __init__(self):
        self.timezone_handler = pytz.timezone('Europe/Brussels')

    async def generate_feed(self, subscription_config: dict):
        """
        Feed generation pipeline:
        1. Query deadlines based on user filters
        2. Transform to ICS VEVENT format
        3. Add configurable reminders (VALARM)
        4. Set proper timezone information
        5. Generate unique feed URL
        """

    def create_compliance_event(self, deadline: ComplianceDeadline):
        """
        ICS Event structure:
        - DTSTART/DTEND: Deadline date
        - SUMMARY: Compliance requirement title
        - DESCRIPTION: Detailed requirements + source
        - CATEGORIES: Compliance type classification
        - PRIORITY: Based on urgency and scope
        - URL: Link to source document
        - VALARM: Configurable reminders
        """

    ICS_EVENT_TEMPLATE = """
BEGIN:VEVENT
UID:{unique_id}@50data.eu
DTSTART;VALUE=DATE:{deadline_date}
DTEND;VALUE=DATE:{deadline_date}
SUMMARY:{compliance_title}
DESCRIPTION:{detailed_description}\\n\\nSource: {source_url}\\n\\nScope: {scope_details}\\n\\nPowered by 50Data
CATEGORIES:{compliance_category}
PRIORITY:{urgency_level}
URL:{source_document_url}
LOCATION:{applicable_regions}

BEGIN:VALARM
TRIGGER:-P{reminder_days}D
ACTION:DISPLAY
DESCRIPTION:EU Compliance deadline in {reminder_days} days: {compliance_title}
END:VALARM

END:VEVENT
"""
```

### Real-time Update System

```python
class FeedUpdateManager:
    """Handle real-time feed updates and notifications"""

    async def detect_deadline_changes(self):
        """
        Change detection:
        1. New deadlines discovered
        2. Existing deadlines modified
        3. Deadlines cancelled or postponed
        4. Source document updates
        """

    async def propagate_updates(self, change_events: List[ChangeEvent]):
        """
        Update propagation:
        1. Regenerate affected ICS feeds
        2. Send webhook notifications
        3. Update CDN cached feeds
        4. Trigger email notifications
        """

    def calculate_affected_subscriptions(self, deadline_change: DeadlineChange):
        """Determine which user feeds need regeneration"""
```

## 🔌 API Layer

### REST API Design

```python
from fastapi import FastAPI, Depends, HTTPException
from fastapi.security import HTTPBearer

app = FastAPI(title="Compliance Calendar API", version="1.0.0")

@app.get("/api/v1/deadlines")
async def get_deadlines(
    countries: List[str] = Query(None),
    sectors: List[str] = Query(None),
    deadline_types: List[str] = Query(None),
    from_date: date = Query(None),
    to_date: date = Query(None),
    limit: int = Query(100, le=1000),
    api_key: str = Depends(verify_api_key)
):
    """
    Get compliance deadlines with filtering

    Response format:
    {
        "deadlines": [
            {
                "id": "uuid",
                "date": "2025-01-15",
                "title": "AI Act Classification Compliance",
                "type": "compliance_deadline",
                "scope": {
                    "countries": ["EU-wide"],
                    "sectors": ["AI_providers"]
                },
                "urgency": "high",
                "source": "eur-lex/32024R1689"
            }
        ],
        "total": 150,
        "page": 1
    }
    """

@app.get("/feeds/{subscription_id}/calendar.ics")
async def get_calendar_feed(subscription_id: str):
    """Generate personalized ICS calendar feed"""

@app.post("/api/v1/webhooks")
async def register_webhook(webhook_config: WebhookConfig):
    """Register webhook for real-time deadline updates"""
```

### Webhook System

```python
class WebhookManager:
    """Manage webhook notifications for real-time updates"""

    async def send_webhook(self, webhook_url: str, event: WebhookEvent):
        """
        Webhook payload:
        {
            "event_type": "deadline_added|deadline_updated|deadline_cancelled",
            "timestamp": "2024-01-15T10:30:00Z",
            "deadline": {
                "id": "uuid",
                "date": "2025-01-15",
                "title": "Compliance requirement",
                "changes": ["date_updated", "scope_expanded"]
            }
        }
        """

    async def validate_webhook_endpoint(self, url: str):
        """Validate webhook URL responds correctly"""

    async def handle_webhook_failures(self, failed_webhook: FailedWebhook):
        """Retry logic with exponential backoff"""
```

## 📊 Monitoring & Analytics

### Application Monitoring

```python
class MonitoringSystem:
    """Comprehensive system monitoring and alerting"""

    def __init__(self):
        self.sentry = sentry_sdk
        self.metrics = prometheus_client

    async def track_extraction_accuracy(self, extraction_result):
        """Monitor NLP extraction accuracy over time"""

    async def monitor_api_performance(self, endpoint: str, response_time: float):
        """Track API response times and error rates"""

    async def alert_on_source_failures(self, source: str, failure_count: int):
        """Alert when legal data sources become unavailable"""

    ALERT_THRESHOLDS = {
        "extraction_accuracy": 0.85,  # Alert if accuracy drops below 85%
        "api_response_time": 2.0,     # Alert if response time > 2 seconds
        "source_failure_rate": 0.1,   # Alert if 10% of sources fail
        "feed_generation_time": 30.0  # Alert if feed generation > 30 seconds
    }
```

### Business Analytics

```python
class AnalyticsEngine:
    """Track business metrics and user behavior"""

    async def track_compliance_coverage(self):
        """Monitor percentage of compliance deadlines captured"""

    async def analyze_user_engagement(self):
        """Track feed usage patterns and user retention"""

    async def measure_deadline_accuracy(self):
        """Validate extracted deadlines against manual verification"""

    BUSINESS_METRICS = {
        "compliance_coverage": "% of known deadlines captured",
        "extraction_accuracy": "% of dates correctly extracted",
        "user_retention": "% of users active after 30 days",
        "feed_usage": "Average feed accesses per user",
        "api_adoption": "% of enterprise users using API"
    }
```

## 🔧 Development & Deployment

### Local Development Setup

```yaml
# docker-compose.yml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/compliance
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: compliance
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine

  celery:
    build: .
    command: celery -A app.celery worker --loglevel=info
    depends_on:
      - db
      - redis

volumes:
  postgres_data:
```

### Production Deployment (Hetzner Cloud EU)

```python
# 50Data Production configuration (EU data sovereignty)
PRODUCTION_CONFIG = {
    "hosting": "Hetzner Cloud (EU data centers - Nuremberg/Falkenstein)",
    "database": "Hetzner Managed PostgreSQL with read replicas",
    "caching": "Hetzner Redis Cluster",
    "cdn": "CloudFlare for feed caching (EU edge locations)",
    "monitoring": "Sentry (EU instance) + Custom metrics",
    "ci_cd": "GitHub Actions → Hetzner deployment",
    "secrets": "Hetzner Cloud secrets management"
}

# EU-focused scaling considerations
SCALING_PLAN = {
    "horizontal": "Multiple Hetzner instances behind load balancer",
    "database": "Read replicas across EU data centers",
    "background": "Separate Celery workers on Hetzner",
    "caching": "EU CDN for frequently accessed feeds",
    "monitoring": "Auto-scaling based on EU traffic patterns",
    "compliance": "GDPR data residency guaranteed"
}
```

---

**Company**: Blinktank GmbH, Berlin | **Product**: 50Data EU Compliance Platform
**Hosting**: Hetzner Cloud (EU data sovereignty) | **Domain**: 50data.eu
**Next**: Detailed implementation roadmap with Hetzner deployment tasks.