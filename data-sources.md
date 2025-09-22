# Data Sources - 50Data EU Compliance Deadline Service

*Focus: EU Brussels + Germany Only*

## 🎯 Core Data Strategy

**Smart Focus**: EU Brussels + Germany Only
**Why This Works**: EU directives provide framework for national implementations
**Sources**: EUR-Lex API (EU) + German XML processing (gesetze-im-internet.de)
**Cost**: EUR-Lex API access + server for XML processing

**Maximum Impact Strategy:**
- EUR-Lex = EU directive deadlines (framework for implementations)
- Germany = Largest EU economy implementation example
- Proof of concept with core sources

## 📋 Source 1: EUR-Lex (EU-Level)

### API Access

**Registration**: https://eur-lex.europa.eu/content/tools/webservices.html
**Cost**: Free tier (sufficient for core service)
**Timeline**: 24-48 hours approval
**Rate Limit**: 10 requests/second (more than enough)

### Target Documents

**Key CELEX Numbers:**
- **32024R1689**: AI Act (main regulation)
- **32014L0055**: eRechnung directive
- **32019L0944**: Clean Energy directive
- Additional directives as research identifies

### Implementation

```python
# EUR-Lex integration
import requests

def get_document_text(celex_number):
    """Simple document retrieval"""
    url = f"https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:{celex_number}"
    response = requests.get(url)
    return response.text

# Target documents
TARGET_DOCUMENTS = [
    "32024R1689",  # AI Act
    "32014L0055",  # eRechnung
    # Add more as needed
]
```

## 📋 Source 2: German XML Processing (Automated)

### Gesetze im Internet XML Parser

**Automated XML Processing:**
```python
class GermanyXMLParser:
    """Automated German deadline extraction processing"""

    BASE_URL = "https://www.gesetze-im-internet.de"
    XML_EXPORT = f"{BASE_URL}/xmlexport"

    def collect_all_laws(self):
        """
        Automated German deadline extraction:
        1. Download law index XML
        2. Parse each law's XML file
        3. Extract deadline dates with regex/NLP
        4. Store structured deadline data
        """

    XML_STRUCTURE = """
    <dokument>
        <metadaten>
            <jurabk>Law abbreviation</jurabk>
            <ausfertigung-datum>Date</ausfertigung-datum>
        </metadaten>
        <textdaten>
            <norm>
                <textdaten>
                    <text>Deadline dates and requirements</text>
                </textdaten>
            </norm>
        </textdaten>
    </dokument>
    """
```

### German Data Sources
- gesetze-im-internet.de XML exports (~15GB)
- Structured XML schema for all German laws
- Weekly bulk download updates
- Automated deadline date extraction from XML structure

### Implementation Example

```python
import httpx
import asyncio
from typing import List, Dict

class EURLexCollector:
    def __init__(self, api_key: str):
        self.base_url = "https://eur-lex.europa.eu/api"
        self.sparql_url = "https://publications.europa.eu/webapi/rdf/sparql"
        self.api_key = api_key
        self.session = httpx.AsyncClient(
            headers={"Authorization": f"Bearer {api_key}"},
            timeout=30.0
        )

    async def search_documents(self, keywords: List[str], since_date: str):
        """Search for documents containing compliance keywords"""

        sparql_query = f"""
        PREFIX cdm: <http://publications.europa.eu/ontology/cdm#>
        SELECT ?work ?title ?date ?celex WHERE {{
          ?work cdm:work_has_subject-matter ?subject .
          ?subject skos:prefLabel ?title .
          ?work cdm:work_date_document ?date .
          ?work cdm:resource_legal_id_celex ?celex .

          FILTER(
            {' || '.join([f'CONTAINS(LCASE(?title), "{kw}")' for kw in keywords])}
          )
          FILTER(?date >= "{since_date}"^^xsd:date)
        }}
        ORDER BY DESC(?date)
        LIMIT 100
        """

        response = await self.session.post(
            self.sparql_url,
            data={"query": sparql_query},
            headers={"Accept": "application/json"}
        )

        return response.json()

    async def get_document_content(self, celex: str, language: str = "EN"):
        """Retrieve document content for deadline extraction by CELEX number"""

        response = await self.session.get(
            f"{self.base_url}/content",
            params={
                "uri": f"CELEX:{celex}",
                "language": language,
                "format": "html"
            }
        )

        return response.text
```

---

## 🎯 RSS Feeds (Monitoring)

### eRechnung Feed
**URL**: `https://www.inoreader.com/stream/user/1005663366/tag/eRechnung`

**Update Frequency**: Hourly monitoring recommended
**Content Type**: RSS 2.0 with article links
**Language**: Mixed (EN, DE, FR)

### ViDA Feed
**URL**: `https://www.inoreader.com/stream/user/1005663366/tag/ViDA`

**Update Frequency**: Hourly monitoring recommended
**Content Type**: RSS 2.0 with regulatory updates
**Language**: Mixed (EN, DE, FR)

### RSS Monitoring Implementation

```python
import feedparser
from datetime import datetime, timedelta

class RSSMonitor:
    def __init__(self):
        self.feeds = {
            "erechnung": "https://www.inoreader.com/stream/user/1005663366/tag/eRechnung",
            "vida": "https://www.inoreader.com/stream/user/1005663366/tag/ViDA"
        }
        self.last_check = {}

    async def monitor_feeds(self):
        """Monitor all RSS feeds for new compliance-related content"""

        for feed_name, feed_url in self.feeds.items():
            try:
                feed_data = feedparser.parse(feed_url)

                # Filter new entries since last check
                last_check_time = self.last_check.get(
                    feed_name,
                    datetime.now() - timedelta(hours=24)
                )

                new_entries = [
                    entry for entry in feed_data.entries
                    if self.parse_date(entry.published) > last_check_time
                ]

                # Process new entries for compliance signals
                for entry in new_entries:
                    await self.process_feed_entry(entry, feed_name)

                self.last_check[feed_name] = datetime.now()

            except Exception as e:
                logger.error(f"Failed to process feed {feed_name}: {e}")

    def extract_compliance_signals(self, entry):
        """Extract compliance-related information from feed entries"""

        compliance_keywords = [
            "deadline", "implementation", "compliance", "directive",
            "regulation", "transposition", "effective date", "apply from"
        ]

        title_lower = entry.title.lower()
        summary_lower = getattr(entry, 'summary', '').lower()

        signals = []
        for keyword in compliance_keywords:
            if keyword in title_lower or keyword in summary_lower:
                signals.append({
                    "keyword": keyword,
                    "context": "title" if keyword in title_lower else "summary",
                    "entry_url": entry.link,
                    "published": entry.published
                })

        return signals
```

---

## 📊 Data Source Comparison Matrix

| Source | Authentication | Rate Limit | Format | Coverage | Reliability | Priority |
|--------|---------------|------------|---------|----------|-------------|----------|
| EUR-Lex | API Key | 10/sec | JSON/XML | EU-wide | High | 1 |
| RSS Feeds | None | None | RSS | EU updates | Medium | 2 |
| Germany XML | None | None | XML | Germany | High | 3 |

---

## 🔄 Data Collection Workflow

### Daily Collection Schedule

```python
COLLECTION_SCHEDULE = {
    "06:00": {
        "sources": ["eur_lex"],
        "focus": "new_directives_regulations",
        "priority": "high"
    },
    "12:00": {
        "sources": ["rss_feeds"],
        "focus": "compliance_updates",
        "priority": "medium"
    },
    "18:00": {
        "sources": ["german_xml"],
        "focus": "german_implementations",
        "priority": "medium"
    }
}
```

### Error Handling Strategy

```python
ERROR_HANDLING = {
    "auth_failure": {
        "strategy": "refresh_tokens",
        "retry_delay": "exponential_backoff",
        "max_retries": 3
    },
    "rate_limit": {
        "strategy": "queue_and_retry",
        "retry_delay": "rate_limit_reset_time",
        "backoff_multiplier": 2
    },
    "parsing_error": {
        "strategy": "store_raw_and_flag",
        "ai_review": True,
        "alert_admin": True
    },
    "network_timeout": {
        "strategy": "retry_with_longer_timeout",
        "max_retries": 5,
        "timeout_multiplier": 1.5
    }
}
```

### Data Quality Metrics

```python
QUALITY_METRICS = {
    "extraction_accuracy": {
        "target": 0.90,
        "measurement": "ai_validation_sample",
        "frequency": "weekly"
    },
    "data_freshness": {
        "target": "24_hours",
        "measurement": "time_since_publication",
        "alert_threshold": "48_hours"
    },
    "source_availability": {
        "target": 0.99,
        "measurement": "successful_requests_ratio",
        "alert_threshold": 0.95
    }
}
```

---

## 🔐 Security & Compliance

### API Key Management
- Store all credentials in environment variables
- Use secret management services in production
- Rotate API keys regularly (quarterly)
- Monitor for unauthorized usage

### Data Privacy
- Store only necessary document metadata
- Implement GDPR-compliant data retention (max 7 years for deadline data)
- Provide data export and deletion capabilities
- Log access to sensitive compliance data

### Rate Limiting Protection
```python
class RateLimiter:
    def __init__(self, requests_per_second: int):
        self.requests_per_second = requests_per_second
        self.tokens = requests_per_second
        self.last_update = time.time()

    async def acquire(self):
        """Token bucket algorithm for rate limiting"""
        now = time.time()
        time_passed = now - self.last_update
        self.tokens = min(
            self.requests_per_second,
            self.tokens + time_passed * self.requests_per_second
        )
        self.last_update = now

        if self.tokens >= 1:
            self.tokens -= 1
            return True
        else:
            # Calculate wait time
            wait_time = (1 - self.tokens) / self.requests_per_second
            await asyncio.sleep(wait_time)
            self.tokens = 0
            return True
```

## 🔄 Core Data Collection Workflow

### Data Collection Process
1. **German XML processing** - Automated extraction from gesetze-im-internet.de
2. **EU AI Act phases** - Extract from CELEX:32024R1689 via EUR-Lex API
3. **Key EU directives** - Automated identification via EUR-Lex API
4. **Validation** - Cross-check dates with multiple sources

### Data Processing
1. **Database creation** - Simple deadline storage
2. **Data validation** - AI verification of all dates
3. **ICS generation** - Convert to calendar format
4. **Quality control** - Test calendar compatibility

### Success Criteria
- **50+ deadlines** captured automatically via AI and API
- **100% accuracy** through AI validation
- **Full automation** - focus on quality through AI
- **Immediate value** - working calendar for users

---

**Company**: Blinktank GmbH, Berlin | **Product**: 50Data
**Strategy**: Automated extraction → Enhanced automation → Full platform
**Target**: Automated deadline extraction from core EU sources
**Next**: Begin automated German XML processing and EUR-Lex registration