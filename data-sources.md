# Data Sources - 50Data MVP (EU + Germany Only)

*Ultra-simplified MVP: EU Brussels + Germany = Maximum Impact*

## 🎯 MVP Data Strategy - SIMPLIFIED

**Smart MVP Focus**: EU Brussels + Germany Only
**Why This Works**: EU directives provide framework for national implementations
**Sources**: EUR-Lex API (EU) + German XML processing (gesetze-im-internet.de)
**Timeline**: 4 weeks to 50+ EU+German deadlines
**Cost**: <€50/month for EUR-Lex API + server for XML processing

**Maximum Impact Strategy:**
- EUR-Lex = EU directive deadlines (framework for implementations)
- Germany = Largest EU economy implementation example
- Proof of concept before expanding to other member states

## 📋 MVP Source 1: EUR-Lex (EU-Level)

### MVP Approach

**Strategy**: Target specific high-value documents manually
**Documents**: AI Act, eRechnung directives, key EU regulations
**Method**: Direct document access via CELEX numbers
**Scope**: 20-30 key EU deadlines for MVP

### Simple API Access

**Registration**: https://eur-lex.europa.eu/content/tools/webservices.html
**Cost**: Free tier (sufficient for MVP)
**Timeline**: 24-48 hours approval
**Rate Limit**: 10 requests/second (more than enough for MVP)

### MVP Target Documents

**Key CELEX Numbers for MVP:**
- **32024R1689**: AI Act (main regulation)
- **32014L0055**: eRechnung directive
- **32019L0944**: Clean Energy directive
- Additional directives as research identifies

### Simple Implementation

```python
# MVP EUR-Lex integration
import requests

def get_document_text(celex_number):
    """Simple document retrieval for MVP"""
    url = f"https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:{celex_number}"
    response = requests.get(url)
    return response.text

# Target documents for MVP
MVP_DOCUMENTS = [
    "32024R1689",  # AI Act
    "32014L0055",  # eRechnung
    # Add more as needed
]
```

## 📋 MVP Source 2: German XML Processing (Automated)

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

## 🎯 Priority 2: France - Légifrance API

### Overview
**Légifrance** is France's official legal information service with comprehensive API access to French legislation, including EU directive transpositions.

### API Access Details

**Base URL**: `https://api.piste.gouv.fr/dila/legifrance/v1`
**Sandbox**: `https://sandbox-api.piste.gouv.fr/dila/legifrance/v1`

**Authentication**: OAuth 2.0
- Register at https://piste.gouv.fr
- Client credentials flow
- Token endpoint: `https://oauth.piste.gouv.fr/api/oauth/token`

**Rate Limits**: 100 requests per minute

### Key Endpoints

#### Authentication
```http
POST /oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id={client_id}
&client_secret={client_secret}
&scope=openid
```

#### Search Laws
```http
POST /search
Content-Type: application/json
Authorization: Bearer {access_token}

{
  "recherche": {
    "champRecherche": "ALL",
    "pageSize": 100,
    "pageNumber": 1,
    "sort": "DATE_PUBLI",
    "facettes": ["typeDocument", "datePubli"]
  }
}
```

#### Get Document
```http
GET /consult/jorf
Parameters:
- textId: Document identifier
- format: json or xml
```

### Document Structure

```json
{
  "id": "JORFTEXT000000000000",
  "titre": "Document title",
  "datePublication": "2024-01-15",
  "texteConsolide": "Full consolidated text",
  "versionEnVigueur": "Current version",
  "liens": [
    {
      "type": "transposition",
      "directive": "32019L0944"
    }
  ]
}
```

### Implementation Example

```python
class LegifranCollector:
    def __init__(self, client_id: str, client_secret: str):
        self.auth_url = "https://oauth.piste.gouv.fr/api/oauth/token"
        self.base_url = "https://api.piste.gouv.fr/dila/legifrance/v1"
        self.client_id = client_id
        self.client_secret = client_secret
        self.access_token = None

    async def authenticate(self):
        """Get OAuth 2.0 access token"""
        data = {
            "grant_type": "client_credentials",
            "client_id": self.client_id,
            "client_secret": self.client_secret,
            "scope": "openid"
        }

        async with httpx.AsyncClient() as client:
            response = await client.post(
                self.auth_url,
                data=data,
                headers={"Content-Type": "application/x-www-form-urlencoded"}
            )

            token_data = response.json()
            self.access_token = token_data["access_token"]

    async def search_transpositions(self, directive_number: str):
        """Search for national transpositions of EU directives"""

        search_payload = {
            "recherche": {
                "champRecherche": "ALL",
                "operateur": "ET",
                "pageSize": 50,
                "sort": "DATE_PUBLI",
                "typePagination": "DEFAUT",
                "filters": [
                    {
                        "facette": "ALL",
                        "sousOperateur": "ET",
                        "valeurs": [f"transposition {directive_number}"]
                    }
                ]
            }
        }

        headers = {
            "Authorization": f"Bearer {self.access_token}",
            "Content-Type": "application/json"
        }

        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.base_url}/search",
                json=search_payload,
                headers=headers
            )

            return response.json()
```

---

## 🎯 Priority 3: RSS/XML Feeds

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

## 🎯 Secondary Sources (Post-MVP)

### Germany - Gesetze im Internet
**Base URL**: `https://www.gesetze-im-internet.de`
**Format**: XML exports (bulk download)
**Update**: Weekly
**Access**: Open, no authentication required

**XML Structure**:
```xml
<dokument>
    <metadaten>
        <jurabk>BGB</jurabk>
        <amtabk>BGB</amtabk>
        <ausfertigung-datum>1896-08-18</ausfertigung-datum>
    </metadaten>
    <textdaten>
        <norm>
            <metadaten/>
            <textdaten>
                <text>Deadline-related content</text>
            </textdaten>
        </norm>
    </textdaten>
</dokument>
```

### Spain - BOE (Boletín Oficial del Estado)
**Base URL**: `https://www.boe.es`
**RSS Feed**: `https://www.boe.es/rss/canal.php?c=l`
**Open Data**: `https://www.boe.es/datosabiertos`
**Format**: XML + PDF
**Update**: Daily at 00:30 CET

### Poland - ISAP/Sejm
**Base URL**: `https://api.sejm.gov.pl`
**ELI Endpoint**: `https://api.sejm.gov.pl/eli`
**Authentication**: None required
**Format**: JSON (default), XML available

**ELI Pattern**: `/eli/acts/{year}/{position}`
**Example**: `/eli/acts/2024/1234`

---

## 📊 Data Source Comparison Matrix

| Source | Authentication | Rate Limit | Format | Coverage | Reliability | Priority |
|--------|---------------|------------|---------|----------|-------------|----------|
| EUR-Lex | API Key | 10/sec | JSON/XML | EU-wide | High | 1 |
| Légifrance | OAuth 2.0 | 100/min | JSON | France | High | 2 |
| RSS Feeds | None | None | RSS | EU updates | Medium | 3 |
| Germany XML | None | None | XML | Germany | High | 4 |
| Spain BOE | None | None | XML/PDF | Spain | Medium | 5 |
| Poland ISAP | None | None | JSON | Poland | High | 6 |

---

## 🔄 Data Collection Workflow

### Daily Collection Schedule

```python
COLLECTION_SCHEDULE = {
    "06:00": {
        "sources": ["eur_lex", "legifrance"],
        "focus": "new_directives_regulations",
        "priority": "high"
    },
    "12:00": {
        "sources": ["rss_feeds"],
        "focus": "compliance_updates",
        "priority": "medium"
    },
    "18:00": {
        "sources": ["national_apis"],
        "focus": "national_implementations",
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
- Use secret management services in production (AWS Parameter Store, Azure Key Vault)
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

## 🔄 MVP Data Collection Workflow

### Week 1: Automated Data Collection
1. **German XML processing** - Automated extraction from gesetze-im-internet.de
2. **EU AI Act phases** - Extract from CELEX:32024R1689 via EUR-Lex API
3. **Key EU directives** - Automated identification via EUR-Lex API
4. **Validation** - Cross-check dates with multiple sources

### Week 2: Data Processing
1. **JSON database creation** - Simple deadline storage
2. **Data validation** - AI verification of all dates
3. **ICS generation** - Convert to calendar format
4. **Quality control** - Test calendar compatibility

### MVP Success Criteria
- **50+ deadlines** captured automatically via AI and API
- **100% accuracy** through AI validation
- **Full automation** - focus on quality through AI
- **Immediate value** - working calendar for users

## 📊 Expansion Path (Post-MVP)

### Mid-state (Months 3-8)
- Add Poland, Austria, Netherlands APIs
- Automated extraction for high-volume sources
- Basic deduplication and conflict resolution

### End-state (Year 2+)
- End-state: EU-27 coverage
- Advanced NLP processing
- Real-time update systems
- AI entry for sources without APIs

---

**Company**: Blinktank GmbH, Berlin | **Product**: 50Data MVP
**Strategy**: Automated extraction → Enhanced automation → Full platform
**Timeline**: 4 weeks to 50+ validated deadlines
**Investment**: <€50/month for MVP deadline extraction
**Next**: Begin automated German XML processing and EUR-Lex registration