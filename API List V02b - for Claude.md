# 🚨 ARCHIVED - Legal Collection System Implementation Briefing

**⚠️ THIS DOCUMENT IS OUTDATED AND OUT OF SCOPE ⚠️**

**Current Project Scope**: Simple EU compliance deadline service (see README.md, TODO.md, technical-design.md)
**This Document**: Archived - represents old, abandoned comprehensive legal collection approach
**Decision**: Project was deliberately scaled down per git history (commit 6e2e40d: "Simplify MVP to EU Brussels + Germany only")

---

_ARCHIVED: For Claude Code - Building EU Legal Text Collection Infrastructure_

## System Requirements

Build an automated collection system for legal texts (laws, directives, regulations) from all 27 EU member states plus EU-level legislation.

## Collection Architecture

```
┌──────────────────────────────────────┐
│         COLLECTION ORCHESTRATOR       │
├──────────────────────────────────────┤
│ Priority 1: API Collectors (6)       │
│ Priority 2: EUR-Lex Collector        │
│ Priority 3: XML/RSS Parsers (3)      │
│ Priority 4: Scraper Modules (18)     │
└──────────────────────────────────────┘
```

## PRIORITY 1: Direct API Collectors (Implement First)

### 1. France - Légifrance Collector

```python
class LegifranCollector:
    """Most comprehensive - 500k+ documents"""
    
    BASE_URL = "https://api.piste.gouv.fr/dila/legifrance/v1"
    SANDBOX = "https://sandbox-api.piste.gouv.fr/dila/legifrance/v1"
    
    AUTH_REQUIRED = True  # OAuth 2.0
    AUTH_ENDPOINT = "https://oauth.piste.gouv.fr/api/oauth/token"
    
    ENDPOINTS = {
        "lois": "/lois/",                    # Laws
        "codes": "/codes/",                   # Legal codes (41 codes)
        "decrets": "/decrets/",              # Decrees
        "arretes": "/arretes/",              # Ministerial orders
        "circulaires": "/circulaires/",      # Circulars
        "conventions": "/conventions/",      # Collective agreements
        "jurisprudence": "/jurisprudence/"  # Case law
    }
    
    def collect_all_laws(self):
        """
        Collection strategy:
        1. Authenticate via OAuth
        2. Get list of all laws via /lois/search
        3. Iterate with pagination (limit=100)
        4. Store full text + metadata
        
        Rate limit: 100 requests/minute
        Update frequency: Daily
        Format: JSON or XML
        """
        
    SEARCH_PARAMS = {
        "pageSize": 100,
        "pageNumber": 1,
        "sort": "DATE_PUBLI",
        "facettes": ["typeDocument", "datePubli"],
        "champRecherche": "ALL"
    }
    
    DOCUMENT_STRUCTURE = {
        "id": "JORFTEXT000000000000",
        "titre": str,
        "datePublication": "YYYY-MM-DD",
        "texteConsolide": str,  # Full consolidated text
        "versionEnVigueur": str,
        "liens": []  # Related documents
    }
```

### 2. Poland - ISAP/Sejm Collector

```python
class PolandLegalCollector:
    """Complete Polish legislation with ELI support"""
    
    BASE_URL = "https://api.sejm.gov.pl"
    ELI_ENDPOINT = f"{BASE_URL}/eli"
    ISAP_URL = "http://isap.sejm.gov.pl/api"
    
    AUTH_REQUIRED = False
    
    DOCUMENT_TYPES = {
        "ustawa": "/acts",           # Laws
        "rozporzadzenie": "/regulations",  # Regulations
        "obwieszczenie": "/announcements", # Announcements
        "umowa": "/agreements"        # International agreements
    }
    
    def collect_by_year(self, year: int, doc_type: str):
        """
        Collection strategy:
        1. Query by year and type
        2. Get ELI identifiers
        3. Fetch full text via /eli/{identifier}
        4. Parse JSON response
        
        Rate limit: None specified
        Format: JSON (default), XML available
        """
        
    ELI_PATTERN = "/eli/acts/{year}/{position}"
    # Example: /eli/acts/2024/1234
    
    RESPONSE_STRUCTURE = {
        "eli": str,
        "title": str,
        "promulgationDate": str,
        "entryIntoForce": str,
        "text": {
            "html": str,
            "pdf": str,
            "xml": str
        },
        "consolidatedText": str
    }
```

### 3. Austria - RIS Collector

```python
class AustriaRISCollector:
    """Federal and state law collector"""
    
    BASE_URL = "https://data.bka.gv.at/ris/api/v2.6"
    
    AUTH_REQUIRED = False
    
    ENDPOINTS = {
        "bundesrecht": "/Bundesrecht",    # Federal law
        "landesrecht": "/Landesrecht",    # State law (9 states)
        "gemeinderecht": "/Gemeinderecht", # Municipal law
        "judikatur": "/Judikatur"         # Court decisions
    }
    
    def collect_federal_laws(self, since_date: str):
        """
        Collection strategy:
        1. Query /Bundesrecht with date filter
        2. Process paginated results (pageSize=100)
        3. Extract consolidated versions
        
        Rate limit: 60 requests/minute
        Format: JSON or XML
        """
        
    QUERY_PARAMS = {
        "Applikation": "BrKons",  # Consolidated federal law
        "Fassung.FassungVom": "YYYY-MM-DD",
        "Seitennummer": 1,
        "Seitengroesse": 100
    }
    
    DOCUMENT_FIELDS = {
        "Titel": str,
        "Kurzinformation": str,
        "Gesetzesnummer": str,  # BGBl number
        "Dokumentnummer": str,
        "ArtikelParagraphAnlage": str,
        "Inkrafttretedatum": str,
        "Text": str  # Full text
    }
```

### 4. Netherlands - Overheid.nl Collector

```python
class NetherlandsCollector:
    """Official publications collector"""
    
    BASE_URL = "https://open.overheid.nl/opendata"
    BWB_API = "https://wetten.overheid.nl/api"  # Laws database
    
    AUTH_REQUIRED = False
    
    PUBLICATION_TYPES = {
        "staatsblad": "/staatsblad",      # Laws and decrees
        "staatscourant": "/staatscourant", # Government gazette
        "tractatenblad": "/tractatenblad", # Treaties
        "gemeenteblad": "/gemeenteblad",  # Municipal
        "provinciaalblad": "/provinciaalblad" # Provincial
    }
    
    def collect_staatsblad(self, year: int):
        """
        Collection strategy:
        1. Query by publication and year
        2. Parse XML metadata
        3. Download full text (BWB format)
        
        Format: XML (BWB - Basiswettenbestand)
        Update: Daily at 06:00 CET
        """
        
    BWB_STRUCTURE = """
    <wetgeving>
        <meta-data>
            <identificatie>BWB-ID</identificatie>
            <titel>Title</titel>
            <citeertitel>Short title</citeertitel>
        </meta-data>
        <wet-besluit>
            <artikel>
                <lid>
                    <tekst>Legal text</tekst>
                </lid>
            </artikel>
        </wet-besluit>
    </wetgeving>
    """
```

### 5. Czech Republic - Zakonyprolidi Collector

```python
class CzechCollector:
    """Legal portal with SPARQL support"""
    
    BASE_URL = "https://www.zakonyprolidi.cz"
    API_URL = f"{BASE_URL}/api"
    SPARQL_ENDPOINT = f"{BASE_URL}/sparql"
    
    AUTH_REQUIRED = True  # API key needed
    
    def collect_via_sparql(self):
        """
        Collection strategy:
        1. SPARQL query for document list
        2. Fetch individual documents
        3. Parse XML/RDF
        
        Format: XML, RDF, JSON-LD
        """
        
    SPARQL_QUERY = """
    SELECT ?uri ?title ?number ?year
    WHERE {
        ?uri a leg:Act ;
             dcterms:title ?title ;
             leg:number ?number ;
             leg:year ?year .
        FILTER(?year >= 2020)
    }
    ORDER BY DESC(?year)
    """
```

### 6. Luxembourg - Legilux Collector

```python
class LuxembourgCollector:
    """Recent legislation only"""
    
    BASE_URL = "https://legilux.public.lu"
    API_URL = f"{BASE_URL}/api"
    
    AUTH_REQUIRED = False
    
    def collect_journal_officiel(self, date: str):
        """
        Collection strategy:
        1. Query by publication date
        2. Get memorial references
        3. Download PDFs and metadata
        
        Limitation: Only recent years digitized
        Format: JSON metadata + PDF
        """
        
    ENDPOINTS = {
        "search": "/api/search",
        "document": "/api/document/{id}",
        "pdf": "/api/pdf/{memorial_ref}"
    }
```

## PRIORITY 2: EUR-Lex Collector (EU-Level)

```python
class EURLexCollector:
    """Essential for EU directives and regulations"""
    
    BASE_URL = "https://eur-lex.europa.eu"
    API_URL = f"{BASE_URL}/api"
    SPARQL = "https://publications.europa.eu/webapi/rdf/sparql"
    
    AUTH_REQUIRED = True  # API key (free registration)
    RATE_LIMIT = "10 requests/second"
    
    def collect_directives(self, year: int):
        """
        Collection strategy:
        1. Search by document type and year
        2. Get CELEX numbers
        3. Fetch full text for each
        4. Include national transpositions
        
        Coverage: All EU legislation
        Languages: All 24 EU languages
        """
        
    CELEX_PATTERN = "3{year}{type}{number}"
    # 32019L0944 = Directive 2019/944
    
    DOCUMENT_TYPES = {
        "L": "Directive",
        "R": "Regulation",
        "D": "Decision",
        "H": "Recommendation",
        "A": "Opinion"
    }
    
    API_ENDPOINTS = {
        "search": "/search",
        "content": "/content",
        "metadata": "/metadata",
        "pdf": "/pdf"
    }
    
    def get_full_text(self, celex: str):
        params = {
            "celex": celex,
            "language": "EN",
            "format": "json"
        }
        # Returns consolidated version with amendments
```

## PRIORITY 3: XML/RSS Parsers

### Germany - Gesetze im Internet Parser

```python
class GermanyXMLParser:
    """No API but structured XML exports"""
    
    BASE_URL = "https://www.gesetze-im-internet.de"
    XML_EXPORT = f"{BASE_URL}/xmlexport"
    
    def collect_all_laws(self):
        """
        Collection strategy:
        1. Download law index XML
        2. Parse each law's XML file
        3. Extract structured text
        
        Update: Weekly bulk download
        Format: Custom XML schema
        Size: ~15GB total
        """
        
    XML_STRUCTURE = """
    <dokument>
        <metadaten>
            <jurabk>Law abbreviation</jurabk>
            <amtabk>Official abbreviation</amtabk>
            <ausfertigung-datum>Date</ausfertigung-datum>
        </metadaten>
        <textdaten>
            <fussnoten/>
            <norm>
                <metadaten/>
                <textdaten>
                    <text>Legal text</text>
                </textdaten>
            </norm>
        </textdaten>
    </dokument>
    """
```

### Spain - BOE Parser

```python
class SpainBOEParser:
    """RSS feeds and XML data"""
    
    BASE_URL = "https://www.boe.es"
    RSS_FEED = f"{BASE_URL}/rss/canal.php?c=l"
    OPEN_DATA = f"{BASE_URL}/datosabiertos"
    
    def collect_daily_publications(self):
        """
        Collection strategy:
        1. Parse daily RSS feed
        2. Download XML summaries
        3. Fetch PDF full texts
        
        Update: Daily at 00:30 CET
        Format: XML + PDF
        """
```

### Italy - Normattiva Parser

```python
class ItalyNormattivaParser:
    """Akoma Ntoso XML format"""
    
    BASE_URL = "https://www.normattiva.it"
    OPEN_DATA = f"{BASE_URL}/opendata"
    
    def collect_bulk_export(self):
        """
        Collection strategy:
        1. Download bulk XML exports
        2. Parse Akoma Ntoso format
        3. Extract structured articles
        
        Update: Weekly
        Format: Akoma Ntoso XML
        """
```

## PRIORITY 4: Scraper Modules (18 Countries)

### Template Scraper for HTML-Only Countries

```python
class GenericLegalScraper:
    """For countries without APIs or structured data"""
    
    COUNTRIES_CONFIG = {
        "belgium": {
            "url": "http://www.ejustice.just.fgov.be/moniteur",
            "selector": "div.law-text",
            "pagination": "a.next-page",
            "identifier": "numac"  # Belgian law ID
        },
        "bulgaria": {
            "url": "https://dv.parliament.bg",
            "selector": "div.document-content",
            "identifier": "state-gazette-number"
        },
        "denmark": {
            "url": "https://www.retsinformation.dk",
            "selector": "div.dokumenttekst",
            "identifier": "eli-reference"
        },
        "estonia": {
            "url": "https://www.riigiteataja.ee",
            "selector": "div.act-text",
            "language": "et",
            "identifier": "rt-reference"
        },
        "finland": {
            "url": "https://www.finlex.fi",
            "selector": "div#latomakoneessa",
            "languages": ["fi", "sv"],
            "identifier": "sdk-number"
        },
        # ... continue for all 18 countries
    }
    
    def scrape_country(self, country: str, date_from: str):
        """
        Generic collection strategy:
        1. Navigate to search page
        2. Filter by date
        3. Extract document links
        4. Download and parse HTML
        5. Clean and structure text
        
        Challenges:
        - Rate limiting (add delays)
        - Dynamic JS content (use Selenium)
        - PDF-only (OCR required)
        """
```

## Collection Orchestrator

```python
class LegalCollectionOrchestrator:
    """Main system controller"""
    
    def __init__(self):
        self.collectors = {
            # Priority 1: Direct APIs
            "france": LegifranCollector(),
            "poland": PolandLegalCollector(),
            "austria": AustriaRISCollector(),
            "netherlands": NetherlandsCollector(),
            "czech": CzechCollector(),
            "luxembourg": LuxembourgCollector(),
            
            # Priority 2: EUR-Lex
            "eu": EURLexCollector(),
            
            # Priority 3: Parsers
            "germany": GermanyXMLParser(),
            "spain": SpainBOEParser(),
            "italy": ItalyNormattivaParser(),
            
            # Priority 4: Scrapers
            **{country: GenericLegalScraper(country) 
               for country in SCRAPER_COUNTRIES}
        }
        
    def daily_collection_workflow(self):
        """
        Execution order:
        1. Collect from APIs (parallel)
        2. Collect EUR-Lex updates
        3. Parse XML/RSS feeds
        4. Run scrapers (sequential with delays)
        """
        
    def handle_failures(self):
        """
        Retry strategy:
        - API failures: Exponential backoff
        - Scraper blocks: Rotate user agents
        - Rate limits: Queue and retry
        """
```

## Data Storage Schema

```python
UNIFIED_DOCUMENT_SCHEMA = {
    "document_id": str,          # Unique identifier
    "country": str,              # ISO country code
    "title": str,                # Document title
    "document_type": str,        # Law/Decree/Regulation
    "identifier": {
        "national": str,         # National ID (BGBl, NOR, etc.)
        "eli": str,             # ELI if available
        "celex": str            # CELEX for EU docs
    },
    "dates": {
        "promulgation": str,     # Date signed
        "publication": str,      # Date published
        "entry_into_force": str  # Date effective
    },
    "text": {
        "full": str,            # Complete text
        "consolidated": str,     # Current version
        "format": str           # html/xml/json
    },
    "metadata": {
        "source_url": str,
        "collection_date": str,
        "collection_method": str,  # api/parser/scraper
        "language": str,
        "file_formats": []
    },
    "amendments": [],           # Related documents
    "status": str              # in_force/repealed/amended
}
```

## Implementation Schedule

```python
IMPLEMENTATION_PHASES = {
    "Phase 1 (Week 1-2)": [
        "Implement EUR-Lex collector",
        "Implement France Légifrance collector",
        "Set up data storage"
    ],
    "Phase 2 (Week 3-4)": [
        "Add Poland, Austria, Netherlands APIs",
        "Implement Czech, Luxembourg collectors"
    ],
    "Phase 3 (Week 5-6)": [
        "Build Germany, Spain, Italy parsers",
        "Create unified document schema"
    ],
    "Phase 4 (Week 7-10)": [
        "Develop generic scraper",
        "Configure for 18 remaining countries",
        "Add error handling and monitoring"
    ]
}
```

## Critical Success Factors

1. **Start with APIs** - Most reliable, structured data
2. **EUR-Lex is essential** - Covers all EU-level legislation
3. **Handle rate limits** - Respect limits, implement backoff
4. **Store raw + processed** - Keep original format and cleaned version
5. **Monitor changes** - APIs and websites change frequently
6. **Language handling** - Many countries publish in multiple languages
7. **Update scheduling** - Different sources update at different times

## Error Handling Requirements

```python
ERROR_STRATEGIES = {
    "AUTH_FAILURE": "Refresh OAuth tokens",
    "RATE_LIMIT": "Exponential backoff",
    "PARSING_ERROR": "Store raw, flag for manual review",
    "NETWORK_TIMEOUT": "Retry with longer timeout",
    "SCRAPER_BLOCKED": "Rotate user agents, add delays",
    "SCHEMA_CHANGE": "Alert admin, fallback to raw storage"
}
```

## Monitoring Dashboard Metrics

- Documents collected per country/day
- API success/failure rates
- Scraper blocking incidents
- Data freshness (time since last update)
- Storage usage and growth rate
- Missing date ranges per country

## Version

- **Briefing Version**: 3.0
- **Purpose**: Legal text collection system implementation
- **Coverage**: 27 EU countries + EUR-Lex
- **Focus**: Practical implementation details