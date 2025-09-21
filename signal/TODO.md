# Development TODO List - 50Data EU Compliance Platform

*Blinktank GmbH, Berlin | Hetzner Cloud EU Deployment*

## 🚀 IMMEDIATE ACTIONS (Week 1)

### Critical Path - Start Today

**Priority 1: 50Data Project Foundation**
- [ ] Create new Git repository for 50data-compliance project
- [ ] Set up Python project structure with pyproject.toml for 50Data
- [ ] Initialize FastAPI application with basic health check endpoint
- [ ] Configure Docker development environment (app, postgres, redis)
- [ ] Set up pytest testing framework and pre-commit hooks
- [ ] Register 50data.eu domain and configure DNS
- [ ] Set up Hetzner Cloud account and basic project

**Priority 2: EUR-Lex API Access**
- [ ] **URGENT**: Register for EUR-Lex API access at https://eur-lex.europa.eu/content/tools/webservices.html
- [ ] Test EUR-Lex SPARQL endpoint with sample queries
- [ ] Create EUR-Lex client class with authentication and rate limiting
- [ ] Test document retrieval for eRechnung and AI Act directives

**Priority 3: Database Schema**
- [ ] Create PostgreSQL database schema for source documents
- [ ] Add compliance deadlines table with proper indexing
- [ ] Implement basic CRUD operations for documents
- [ ] Add database migrations with Alembic

---

## 📋 WEEK 1 DETAILED TASKS

### Day 1: Environment Setup
```bash
# Commands to run today for 50Data:
mkdir 50data-compliance && cd 50data-compliance
git init
touch pyproject.toml requirements.txt Dockerfile docker-compose.yml
mkdir app tests docs
echo "# 50Data EU Compliance Platform" > README.md
```

**Tasks:**
- [ ] Initialize project repository with proper .gitignore
- [ ] Create pyproject.toml with dependencies (fastapi, sqlalchemy, celery, spacy)
- [ ] Write Dockerfile for development environment
- [ ] Create docker-compose.yml with postgres and redis services
- [ ] Set up GitHub repository and initial commit

**Dependencies to add:**
```toml
[tool.poetry.dependencies]
python = "^3.11"
fastapi = "^0.104.0"
uvicorn = "^0.24.0"
sqlalchemy = "^2.0.0"
asyncpg = "^0.29.0"
redis = "^5.0.0"
celery = "^5.3.0"
spacy = "^3.7.0"
pydantic = "^2.5.0"
httpx = "^0.25.0"
python-multipart = "^0.0.6"
```

### Day 2: Basic FastAPI Application
**Tasks:**
- [ ] Create `app/main.py` with FastAPI application
- [ ] Add health check endpoint (`/health`)
- [ ] Implement basic error handling and logging
- [ ] Add CORS middleware for development
- [ ] Create basic project structure (models, routers, services)

**File structure to create:**
```
app/
├── __init__.py
├── main.py
├── models/
│   ├── __init__.py
│   └── documents.py
├── routers/
│   ├── __init__.py
│   ├── health.py
│   └── api_v1.py
├── services/
│   ├── __init__.py
│   └── collectors/
└── core/
    ├── __init__.py
    ├── config.py
    └── database.py
```

### Day 3: EUR-Lex API Integration
**Tasks:**
- [ ] **CRITICAL**: Complete EUR-Lex API registration (allow 24-48 hours for approval)
- [ ] Create EUR-Lex client in `app/services/collectors/eurlex.py`
- [ ] Implement SPARQL query builder for compliance documents
- [ ] Add rate limiting (10 requests/second max)
- [ ] Test with sample CELEX numbers for AI Act and eRechnung

**Sample SPARQL query to implement:**
```sparql
SELECT ?work ?title ?date WHERE {
  ?work cdm:work_has_subject-matter ?subject .
  ?subject skos:prefLabel ?title .
  ?work cdm:work_date_document ?date .
  FILTER(CONTAINS(LCASE(?title), "artificial intelligence") ||
         CONTAINS(LCASE(?title), "electronic invoicing"))
  FILTER(?date >= "2020-01-01"^^xsd:date)
}
ORDER BY DESC(?date)
LIMIT 100
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

**Company**: Blinktank GmbH, Berlin | **Founder**: Andreas Dahrendorf
**Product**: 50Data EU Compliance Platform | **Domain**: 50data.eu
**Hosting**: Hetzner Cloud (EU data sovereignty)
**Status**: Ready to begin 50Data implementation
**Next Action**: Register for EUR-Lex API access and setup Hetzner Cloud immediately
**Target**: Working 50Data document ingestion by end of Week 1