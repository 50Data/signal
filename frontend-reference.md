# Frontend Reference - 50Data Admin Dashboard

*Reference Only - Frontend Implementation is Separate Project*

## ⚠️ SCOPE CLARIFICATION

**This file is REFERENCE ONLY** for understanding what APIs and data structures the backend needs to provide.

**Backend Project (This Repo)**: EU legal data collection, processing, and API provision
**Frontend Project (Separate)**: Admin dashboard consuming the backend APIs

---

## 🎨 Frontend Requirements (API Perspective)

### API Endpoints Backend Must Provide

```typescript
// Deadline Management API
GET /api/deadlines              // List all deadlines with filtering
GET /api/deadlines/:id          // Get specific deadline details
POST /api/deadlines             // Create new deadline (admin)
PUT /api/deadlines/:id          // Update deadline (admin)
DELETE /api/deadlines/:id       // Delete deadline (admin)

// Export API
GET /api/export/ics             // Generate ICS calendar file
GET /api/export/csv             // Export deadlines as CSV
GET /api/export/json            // Export deadlines as JSON

// Source Status API
GET /api/sources/status         // Data source health and last update
GET /api/sources/stats          // Collection statistics

// User Management API (Future)
POST /api/auth/login            // User authentication
GET /api/user/preferences       // User preferences for filtering
PUT /api/user/preferences       // Update user preferences
```

### Data Structures Backend Must Return

```typescript
// Deadline Object Structure
interface Deadline {
  id: string
  date: string                  // ISO date format
  title: string
  description: string
  country: string               // ISO country code
  source: string               // Source name
  source_url?: string          // Original source URL
  deadline_type: string        // implementation|compliance|reporting
  manually_validated: boolean
  confidence_score: number     // 0.0 to 1.0
  created_at: string
  updated_at: string
}

// Source Status Structure
interface SourceStatus {
  name: string                 // "EUR-Lex API", "German Sources"
  status: "connected" | "error" | "stale"
  last_update: string         // ISO timestamp
  document_count: number
  error_message?: string
}

// Export Configuration
interface ExportConfig {
  countries?: string[]        // Filter by countries
  date_range?: {
    start: string
    end: string
  }
  deadline_types?: string[]   // Filter by types
  format: "ics" | "csv" | "json"
}
```

---

## 🎨 Dashboard Concept (shadcn/ui)

*Frontend team will implement this consuming the backend APIs*

### Core Views
1. **Deadlines Table** - Advanced data table with filtering
2. **Calendar View** - Visual deadline calendar
3. **Export Center** - ICS/CSV download interface
4. **Sources Status** - Data collection monitoring

### Technology Stack (Frontend Team)
- Next.js 14 with App Router
- shadcn/ui components for professional styling
- Tailwind CSS for responsive design
- Tanstack Table for advanced data tables
- Recharts for analytics visualization

---

## 🔌 Backend Integration Points

### What Backend Provides
- REST APIs with JSON responses
- ICS calendar file generation
- CSV export functionality
- Real-time source status monitoring
- Data validation and confidence scoring

### What Frontend Handles
- User interface and experience
- Authentication and session management
- Dashboard layout and navigation
- Data visualization and charts
- Responsive design and mobile support

---

## 📊 Success Metrics (API Usage)

### Backend API Metrics
- API response times < 200ms
- 99.9% API uptime
- Data freshness < 24 hours
- Export generation < 10 seconds

### Frontend Usage Metrics (Separate Tracking)
- Dashboard page views
- Calendar downloads
- User session duration
- Feature usage analytics

---

**Note**: This reference file helps backend development understand the API requirements. All actual frontend implementation is handled separately by the frontend team.