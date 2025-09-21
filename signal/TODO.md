# MVP TODO List - 50Data Free EU Compliance Calendar

*4-Week MVP Development | Free Version Only*

## 🚀 WEEK 1: Setup & Research

### Critical MVP Tasks - Start Today

**Priority 1: Basic Setup**
- [ ] Register 50data.eu domain
- [ ] Set up basic Hetzner VPS (€5/month)
- [ ] Create simple Python project for MVP
- [ ] Set up basic development environment

**Priority 2: API Access & Research**
- [ ] **URGENT**: Register for EUR-Lex API access (free tier)
- [ ] Research German eRechnung deadlines manually
- [ ] Identify 20-30 key EU compliance deadlines
- [ ] Test EUR-Lex API for specific documents

**Priority 3: Data Collection**
- [ ] Create simple deadline database (JSON/SQLite)
- [ ] Manual research of compliance dates
- [ ] Basic validation of deadline accuracy
- [ ] Document sources for each deadline

---

## 📋 WEEK 1 DETAILED TASKS

### Day 1: Basic Setup
```bash
# Simple MVP commands:
mkdir 50data-mvp && cd 50data-mvp
git init
touch requirements.txt main.py
mkdir data templates static
echo "# 50Data MVP - Free EU Compliance Calendar" > README.md
```

**Simple MVP Tasks:**
- [ ] Register 50data.eu domain (Namecheap/Cloudflare)
- [ ] Set up basic Hetzner Cloud VPS
- [ ] Create simple Python project structure
- [ ] Basic Git repository setup

**Minimal Dependencies:**
```python
# requirements.txt for MVP
flask
requests
icalendar
```

### Day 2: EUR-Lex Registration & Testing
**MVP Tasks:**
- [ ] Register for EUR-Lex API at https://eur-lex.europa.eu/content/tools/webservices.html
- [ ] Test basic API connectivity
- [ ] Research specific document IDs (AI Act: 32024R1689)
- [ ] Create simple API test script

**Simple file structure:**
```
50data-mvp/
├── main.py          # Flask app
├── data.py          # Data collection
├── calendar_gen.py  # ICS generation
├── data/            # JSON deadline storage
├── static/          # Website files
└── templates/       # HTML templates
```

### Day 3-7: Manual Research & Data Collection
**MVP Tasks:**
- [ ] Research German eRechnung B2G deadline (Jan 1, 2025)
- [ ] Research German eRechnung B2B deadlines (2026-2028)
- [ ] Research AI Act implementation phases
- [ ] Collect 30+ EU compliance deadlines manually
- [ ] Create simple JSON database structure
- [ ] Validate all dates with official sources

**JSON Structure Example:**
```json
{
  "deadlines": [
    {
      "date": "2025-01-01",
      "title": "eRechnung B2G mandatory (Germany)",
      "description": "Electronic invoicing mandatory for B2G transactions",
      "source": "German government regulation",
      "countries": ["DE"],
      "type": "implementation"
    }
  ]
}
```

### Day 4: Database Implementation
**Tasks:**
- [ ] Create SQLAlchemy models for source documents and deadlines
- [ ] Implement database connection and session management
- [ ] Add Alembic for database migrations
- [ ] Create initial migration for base schema
- [ ] Test database operations with sample data

**Critical database models:**
```python
# app/models/documents.py
class SourceDocument(Base):
    __tablename__ = "source_documents"

    id = Column(UUID, primary_key=True, default=uuid4)
    source_type = Column(String(50), nullable=False)  # "eur_lex"
    document_identifier = Column(String(200))  # CELEX number
    title = Column(String(1000))
    publication_date = Column(Date)
    raw_content = Column(Text)
    created_at = Column(DateTime, default=datetime.utcnow)

class ComplianceDeadline(Base):
    __tablename__ = "compliance_deadlines"

    id = Column(UUID, primary_key=True, default=uuid4)
    source_document_id = Column(UUID, ForeignKey("source_documents.id"))
    extracted_date = Column(Date, nullable=False)
    deadline_type = Column(String(50))  # "implementation", "compliance"
    title = Column(String(500))
    confidence_score = Column(Float)
```

### Day 5: Basic Document Ingestion
**Tasks:**
- [ ] Create document ingestion service
- [ ] Implement EUR-Lex document retrieval and storage
- [ ] Add error handling for API failures and rate limits
- [ ] Create Celery task for background document processing
- [ ] Test ingestion with 10-20 sample documents

### Day 6-7: Testing & Validation
**Tasks:**
- [ ] Write unit tests for EUR-Lex client
- [ ] Add integration tests for database operations
- [ ] Create test fixtures with sample legal documents
- [ ] Implement logging and monitoring for ingestion pipeline
- [ ] Document API endpoints and setup procedures

---

## 📅 WEEK 2 PRIORITIES

### Date Extraction Engine
- [ ] Install spaCy language models (en_core_web_lg, fr_core_news_lg, de_core_news_lg)
- [ ] Implement regex-based date extraction patterns
- [ ] Create NLP pipeline for contextual date extraction
- [ ] Build rule-based deadline type classifier
- [ ] Create manual validation interface for accuracy testing

### Key regex patterns to implement:
```python
DATE_PATTERNS = {
    "absolute": [
        r"(\d{1,2}\s+(?:January|February|March|April|May|June|July|August|September|October|November|December)\s+\d{4})",
        r"(\d{4}-\d{2}-\d{2})",
        r"(\d{1,2}/\d{1,2}/\d{4})"
    ],
    "relative": [
        r"(\d+)\s+months?\s+after\s+(.+)",
        r"not\s+later\s+than\s+(\d+\s+\w+\s+\d{4})",
        r"(\d+)\s+days?\s+from\s+(.+)"
    ],
    "contextual": [
        r"shall\s+apply\s+from\s+(\d+\s+\w+\s+\d{4})",
        r"comes?\s+into\s+force\s+on\s+(\d+\s+\w+\s+\d{4})"
    ]
}
```

---

## 🔧 TECHNICAL SETUP CHECKLIST

### Development Environment
- [ ] Python 3.11+ installed
- [ ] Docker and Docker Compose installed
- [ ] PostgreSQL client tools
- [ ] Redis client tools
- [ ] Git configured with proper credentials

### External Services Registration (50Data EU)
- [ ] **EUR-Lex API** - Register at https://eur-lex.europa.eu/content/tools/webservices.html
- [ ] **GitHub** - Create repository for version control
- [ ] **Hetzner Cloud** - Account for EU hosting (start with €5/month)
- [ ] **50data.eu Domain** - Register and configure DNS
- [ ] **Sentry EU** - Error monitoring (EU instance)

### Required API Keys (Obtain ASAP)
- [ ] EUR-Lex API key (free, 24-48 hour approval)
- [ ] Sentry DSN for error tracking
- [ ] (Later) Stripe keys for payment processing
- [ ] (Later) SendGrid/Mailgun for email notifications

### Development Tools
- [ ] IDE/Editor with Python support (VS Code, PyCharm)
- [ ] Postman/Insomnia for API testing
- [ ] DBeaver/pgAdmin for database management
- [ ] Browser extensions for API testing

---

## 🎯 SUCCESS CRITERIA - WEEK 1

### Must Have (Critical)
- [ ] EUR-Lex API access approved and working
- [ ] Basic FastAPI application running with health check
- [ ] PostgreSQL database with initial schema
- [ ] Document ingestion pipeline storing 50+ EUR-Lex documents
- [ ] Docker development environment fully operational

### Should Have (Important)
- [ ] Unit tests covering core functionality
- [ ] Error handling and logging infrastructure
- [ ] Basic CI/CD pipeline with GitHub Actions
- [ ] Documentation for setup and API endpoints
- [ ] Performance monitoring with basic metrics

### Nice to Have (Bonus)
- [ ] Basic admin interface for viewing documents
- [ ] Integration with external monitoring (Sentry)
- [ ] Performance optimization for document retrieval
- [ ] Backup and recovery procedures

---

## 🚨 BLOCKERS & DEPENDENCIES

### External Dependencies
- **EUR-Lex API approval**: Critical path dependency, apply immediately
- **Development environment**: Ensure Docker/PostgreSQL working locally
- **Domain knowledge**: Research EU compliance frameworks and deadline types

### Technical Risks
- **API rate limits**: EUR-Lex limits to 10 req/sec, plan accordingly
- **Document formats**: Legal documents may be in PDF/XML, need parsing
- **Language complexity**: Multi-language support requires careful NLP setup

### Mitigation Strategies
- **Parallel development**: Work on database and basic app while waiting for API approval
- **Fallback data sources**: Prepare RSS/scraping as backup to APIs
- **Incremental testing**: Test with small document sets before scaling

---

## 📞 NEXT ACTIONS

### Today (Immediate)
1. **Register for EUR-Lex API** (most important - has approval delay)
2. **Create GitHub repository** and initialize project structure
3. **Set up development environment** with Docker
4. **Install core dependencies** and test basic FastAPI app

### This Week
1. **Complete Week 1 tasks** as outlined in implementation roadmap
2. **Test EUR-Lex integration** once API access is approved
3. **Build basic document ingestion** pipeline
4. **Create database schema** and initial data storage

### Week 2 Preparation
1. **Download spaCy language models** (large files, download ahead)
2. **Research compliance deadline patterns** in legal documents
3. **Plan date extraction testing** with known compliance deadlines
4. **Prepare sample documents** for NLP training and validation

---

## 🗓️ WEEKS 2-4: MVP Development Schedule

### Week 2: Calendar Generation & Basic Website
- [ ] Create ICS calendar generation script
- [ ] Build simple Flask website
- [ ] Add 50+ compliance deadlines to calendar
- [ ] Test calendar compatibility (Outlook, Google, Apple)

### Week 3: Testing & Polish
- [ ] Comprehensive testing across platforms
- [ ] Website content and user instructions
- [ ] Feedback collection system
- [ ] Soft launch to test users

### Week 4: Public Launch & Marketing
- [ ] Final quality checks
- [ ] Public launch on LinkedIn and German compliance communities
- [ ] User acquisition and feedback collection
- [ ] MVP validation and next phase planning

## 📊 MVP Success Criteria

**Technical:**
- 50+ compliance deadlines captured
- Compatible with major calendar applications
- 99% website uptime

**Adoption:**
- 100 downloads by Month 3
- User feedback for roadmap
- Validate demand for paid features

**Investment:**
- <€500/month total costs
- Validate market before monetization

---

**Company**: Blinktank GmbH, Berlin | **Founder**: Andreas Dahrendorf
**Product**: 50Data MVP (Free EU Compliance Calendar)
**Strategy**: Free MVP → Basic Monetization → Full Platform
**Timeline**: 4 weeks to launch | **Investment**: <€500/month
**Next Action**: Register 50data.eu domain and EUR-Lex API access today
**Target**: Live free calendar download by Week 4