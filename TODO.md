# MVP TODO List - 50Data Free EU Compliance Calendar

*4-Week MVP Development | Non-Technical Founder + Claude Code Implementation*

## 🎯 Role Clarification

**Your Role (Non-Technical Founder):**
- Business strategy and market validation
- Content direction and compliance expertise
- File-based management: Edit MD/YAML/CSV + execute Python scripts
- Customer feedback and growth decisions

**Claude Code Role:**
- 100% of Python script development and technical implementation
- Build file management system (YAML configs, CSV data processing)
- All server setup, deployment, and maintenance

## 🚀 WEEK 1: Business Setup & Direction

### Business Tasks (Your Responsibility)

**Priority 1: Business Setup**
- [ ] Register 50data.eu domain (you can do this via Namecheap/Cloudflare)
- [ ] Create Hetzner Cloud account (Claude Code will configure server)
- [ ] Set up Kit/ConvertKit account for email marketing
- [ ] Register for Paddle account for future billing

**Priority 2: Content & Research (Your Expertise) - EU + Germany Only**
- [ ] Identify target German laws for XML processing (eRechnung, compliance laws)
- [ ] Identify 20-30 key EU directive deadlines from EUR-Lex (Brussels legislation)
- [ ] Focus: EU directives apply to ALL 27 countries through Brussels
- [ ] Define target audience and messaging for EU+German coverage
- [ ] Create initial content strategy for automated deadline extraction

**Priority 3: API Registrations (You Handle, Claude Code Implements)**
- [ ] **URGENT**: Register for EUR-Lex API access (free tier)
- [ ] Get Kit/ConvertKit API credentials
- [ ] Get Paddle API credentials (when ready for billing)
- [ ] Provide all credentials to Claude Code for implementation

---

## 📋 WEEK 1 DETAILED BUSINESS TASKS

### Day 1: Business Foundation
**Your Tasks (Non-Technical):**
- [ ] Register 50data.eu domain via Namecheap or Cloudflare
- [ ] Create Hetzner Cloud account (€25/month CX31 plan)
- [ ] Set up Kit/ConvertKit account for email marketing
- [ ] Define initial target audience (EU compliance professionals)

**Claude Code Tasks (Technical):**
- Set up Hetzner CX31 server with PostgreSQL and Flask API
- Create backend project structure and version control
- Configure development environment for API deployment
- Build foundational API architecture for deadline data management

### Day 2: API Registrations & Research Strategy
**Your Tasks (Business):**
- [ ] Register for EUR-Lex API at https://eur-lex.europa.eu/content/tools/webservices.html
- [ ] Research German eRechnung compliance requirements
- [ ] Identify high-priority EU directives (AI Act, GDPR updates, etc.)
- [ ] Create initial messaging strategy for "pure deadline data"

**Claude Code Tasks (Technical):**
- Integrate EUR-Lex API when credentials provided
- Build German XML processing pipeline
- Create deadline extraction algorithms
- Set up PostgreSQL database schema with full-text search
- Begin REST API endpoint development for deadline access

### Day 3-7: Content Strategy & Market Research
**Your Tasks (Business Expertise):**
- [ ] Research German eRechnung B2G deadline (Jan 1, 2025)
- [ ] Research German eRechnung B2B deadlines (2026-2028)
- [ ] Research AI Act implementation phases and key dates
- [ ] Collect 30+ EU compliance deadlines from official sources
- [ ] Validate all dates with government websites
- [ ] Create initial messaging for "pure deadline data" approach
- [ ] Define competitive positioning vs commentary-heavy publishers

**Claude Code Tasks (Technical Implementation):**
- Build database schema for deadline storage with PostgreSQL
- Create data validation systems and deadline extraction algorithms
- Build REST API endpoints for deadline data access and management
- Implement deadline filtering, searching, and pagination in API
- Create ICS calendar generation and export functionality
- Build Python scripts for deadline management and file-based workflow
- Create YAML configuration system and CSV data processing
- Set up automated backups and monitoring
- Implement API authentication and rate limiting

**Deadline Research Template (Your Format):**
```
Date: YYYY-MM-DD
Title: [Clear description]
Description: [Brief explanation]
Source: [Official government source]
Countries: [Affected countries]
Type: [implementation/compliance/reporting]

---

## 📅 WEEK 2: Product Development & Testing

### Your Business Tasks (Week 2)
**Content Validation & Quality Control:**
- [ ] Review and approve auto-extracted deadlines via CSV file editing
- [ ] Test Python scripts and file-based workflow built by Claude Code
- [ ] Validate deadline accuracy against original sources
- [ ] Define quality standards for deadline descriptions
- [ ] Edit YAML email templates for Kit/ConvertKit notifications

**Market Research & Competition:**
- [ ] Research existing compliance calendar solutions
- [ ] Identify target customer segments (compliance teams, consultants)
- [ ] Test messaging with potential customers
- [ ] Define pricing strategy for future monetization

### Claude Code Tasks (Week 2)
**Technical Implementation:**
- Build deadline extraction and validation system
- Complete REST API with all deadline management endpoints
- Implement API filtering, search, and pagination functionality
- Create ICS calendar generation and CSV/JSON export endpoints
- Create Python scripts for deadline management and approval via CSV/YAML
- Integrate Kit/ConvertKit API with YAML configuration
- Set up API authentication and rate limiting
- Set up automated testing and quality control for API endpoints

---

## 📅 WEEK 3-4: Launch Preparation & Go-to-Market

### Your Business Tasks (Week 3-4)
**Launch Preparation:**
- [ ] Test complete API functionality and data workflows
- [ ] Test calendar export and email subscription flows
- [ ] Create professional launch messaging showcasing data quality and API capabilities
- [ ] Generate API documentation and integration examples for developers
- [ ] Set up LinkedIn presence for compliance professionals
- [ ] Prepare PR strategy for "pure deadline data" positioning

**Customer Development:**
- [ ] Reach out to compliance professionals for beta testing
- [ ] Collect user feedback via Kit/ConvertKit surveys
- [ ] Iterate messaging based on early user responses
- [ ] Plan growth strategy for Month 2-3

### Claude Code Tasks (Week 3-4)
**Final Implementation:**
- Complete Python scripts and file management system
- Finalize REST API with all endpoints and data flows
- Deploy production system to Hetzner (Flask API + PostgreSQL)
- Set up monitoring and backup systems
- Integrate Paddle API for future billing
- Final testing and quality assurance of API functionality
- Performance optimization for API response times and data processing

---

## 🎯 SUCCESS CRITERIA - MVP LAUNCH

### Business Success (Your Responsibility)
- [ ] 50+ manually validated EU compliance deadlines
- [ ] Working calendar download at 50data.eu
- [ ] Kit/ConvertKit email system operational
- [ ] Clear messaging for target audience
- [ ] Initial user feedback collected

### Technical Success (Claude Code Responsibility)
- [ ] Complete REST API with all deadline management endpoints functional
- [ ] API filtering, searching, and pagination working properly
- [ ] ICS calendar generation and CSV/JSON export endpoints working
- [ ] API authentication and rate limiting system working
- [ ] Python scripts and file management system working properly
- [ ] CSV/YAML workflow for deadline management functional
- [ ] Email notifications via Kit/ConvertKit with YAML config
- [ ] Hetzner hosting stable and monitored (Flask API + PostgreSQL)
- [ ] All backend systems ready for frontend integration and user growth
- [ ] Performance monitoring with API metrics and response times

### Growth Metrics (Post-Launch)
- [ ] 100 API calendar exports by Month 3
- [ ] 50 Kit/ConvertKit email subscribers by Month 3
- [ ] API usage analytics and user feedback collection
- [ ] Market validation for paid tier API features

---

## 🚨 CRITICAL DEPENDENCIES & BLOCKERS

### Your Immediate Actions Required
- **EUR-Lex API registration**: Must apply today (24-48h approval time)
- **Domain registration**: Register 50data.eu immediately
- **Account setup**: Hetzner, Kit/ConvertKit, Paddle accounts
- **Business research**: EU compliance deadline research (your expertise)

### Claude Code Dependencies
- **API credentials**: Provide EUR-Lex keys when approved
- **Business requirements**: Clear direction on deadline priorities
- **Content validation**: Review extracted deadlines for accuracy
- **User feedback**: Share user testing results for iteration

### Risk Mitigation
- **Content accuracy**: Manual validation of all deadlines required
- **Market validation**: Start with free MVP to prove demand
- **Compliance liability**: Clear disclaimers about information accuracy
- **Technical backup**: Claude Code handles all technical redundancy

---

## 📞 YOUR IMMEDIATE NEXT ACTIONS

### Today (Critical Path)
1. **Register for EUR-Lex API** (most important - has approval delay)
2. **Register 50data.eu domain** (Namecheap or Cloudflare)
3. **Set up Hetzner Cloud account** (Claude Code will configure)
4. **Set up Kit/ConvertKit account** for email marketing

### This Week (Business Foundation)
1. **Complete business setup tasks** (accounts, registrations)
2. **Begin EU compliance deadline research** (your expertise area)
3. **Define target messaging** for "pure deadline data" approach
4. **Provide business requirements** to Claude Code for implementation

### Next 4 Weeks (Business Focus)
1. **Content creation and validation** of compliance deadlines
2. **Market research and customer development**
3. **Testing API functionality** built by Claude Code
4. **Launch preparation and marketing strategy**

---

## 🎯 SUCCESS SUMMARY

**Your Role**: Business strategy, content expertise, file-based management (MD/YAML/CSV + Python scripts)
**Claude Code Role**: 100% backend API development and data processing
**Timeline**: 4 weeks to working deadline API and data collection system
**Investment**: €50/month enhanced infrastructure
**Target**: 100 API exports + 50 email subscribers by Month 3

---

**Company**: Blinktank GmbH, Berlin | **Founder**: Andreas Dahrendorf (Non-Technical)
**Product**: 50Data MVP (Free EU Compliance Calendar)
**Implementation**: Claude Code handles 100% of technical development
**Strategy**: Business expertise + AI development = Rapid MVP delivery
**Content Policy**: Pure deadline data only - never editorial content or commentary
**Timeline**: 4 weeks to launch | **Investment**: <€50/month
**Next Action**: Register 50data.eu domain and EUR-Lex API access today
**Target**: Live free calendar download by Week 4