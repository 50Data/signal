# Data Sources & API Specifications - 50Data EU Platform

*Blinktank GmbH | EU-focused compliance data ingestion*

## 🎯 Priority 1: EUR-Lex (EU-Level Legislation)

### Overview
**EUR-Lex** is the official EU legal database containing all EU legislation, case law, and legislative proposals. Critical for EU-wide compliance deadlines.

### API Access Details

**Base URL**: `https://eur-lex.europa.eu/api`
**Alternative**: SPARQL endpoint at `https://publications.europa.eu/webapi/rdf/sparql`

**Authentication**:
- Free registration required at https://eur-lex.europa.eu/content/tools/webservices.html
- API key provided after approval (24-48 hours)
- Include in header: `Authorization: Bearer {api_key}`

**Rate Limits**:
- 10 requests per second maximum
- 1000 requests per hour recommended
- Implement exponential backoff for 429 responses

### Key Endpoints

#### Document Search
```http
GET /search
Parameters:
- qid: Query ID for specific search types
- type: Document type (directive, regulation, decision)
- DD-DT: Date range (YYYY-MM-DD TO YYYY-MM-DD)
- DN: CELEX number pattern
- DI: Director-general responsible
- form: Response format (json, xml)
```

#### Document Content
```http
GET /content
Parameters:
- uri: Document URI from search results
- language: Language code (EN, FR, DE, etc.)
- format: Content format (html, pdf, xml)
```

### CELEX Number Patterns

**Format**: `3{year}{type}{sequential_number}`

**Document Types**:
- **L**: Directive (e.g., 32019L0944 = Directive 2019/944)
- **R**: Regulation (e.g., 32024R1689 = Regulation 2024/1689)
- **D**: Decision
- **H**: Recommendation
- **A**: Opinion

### Sample SPARQL Queries

#### eRechnung Related Documents
```sparql
PREFIX cdm: <http://publications.europa.eu/ontology/cdm#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>

SELECT ?work ?title ?date ?celex WHERE {
  ?work cdm:work_has_subject-matter ?subject .
  ?subject skos:prefLabel ?title .
  ?work cdm:work_date_document ?date .
  ?work cdm:resource_legal_id_celex ?celex .

  FILTER(
    CONTAINS(LCASE(?title), "electronic invoicing") ||
    CONTAINS(LCASE(?title), "e-invoicing") ||
    CONTAINS(LCASE(?title), "digital invoicing")
  )
  FILTER(?date >= "2020-01-01"^^xsd:date)
}
ORDER BY DESC(?date)
LIMIT 100
```

#### AI Act Documents
```sparql
SELECT ?work ?title ?date ?celex WHERE {
  ?work cdm:work_has_subject-matter ?subject .
  ?subject skos:prefLabel ?title .
  ?work cdm:work_date_document ?date .
  ?work cdm:resource_legal_id_celex ?celex .

  FILTER(
    CONTAINS(LCASE(?title), "artificial intelligence") ||
    CONTAINS(LCASE(?title), "ai act") ||
    ?celex = "32024R1689"
  )
  FILTER(?date >= "2020-01-01"^^xsd:date)
}
ORDER BY DESC(?date)
```

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
        """Retrieve full document content by CELEX number"""

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
                <text>Legal text content</text>
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
        "manual_review": True,
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
        "measurement": "manual_validation_sample",
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
- Implement GDPR-compliant data retention (max 7 years for legal documents)
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

---

**Company**: Blinktank GmbH, Berlin | **Product**: 50Data EU Compliance Platform
**Domain**: 50data.eu | **Focus**: EU-27 data sovereignty
**Status**: Complete data source specifications for 50Data MVP implementation
**Next**: Begin EUR-Lex API registration and basic integration testing for 50Data