# Invoice Generator MicroSaaS - Complete Build & Deploy Guide

A comprehensive step-by-step guide to build a full-stack Invoice Generator application **from scratch** using **Claude Code** and the **AI_FullStack_Development_Kit** pattern, then deploy it to production.

## 🚀 Live Demo

- **Frontend:** https://makeinvoice.sbs
- **API Docs:** https://api.makeinvoice.sbs/docs
- **Demo Credentials:**
  - Email: `demo@makeinvoice.sbs`
  - Password: `demo123456`

---

## 📋 Table of Contents

1. [Tech Stack](#-tech-stack)
2. [Part 0: Claude Code & PRP Setup](#-part-0-claude-code--prp-setup)
3. [Part 1: Project Setup](#-part-1-project-setup)
4. [Part 2: Backend Development](#-part-2-backend-development)
5. [Part 3: Frontend Development](#-part-3-frontend-development)
6. [Part 4: Docker Configuration](#-part-4-docker-configuration)
7. [Part 5: Local Testing](#-part-5-local-testing)
8. [Part 6: Production Deployment](#-part-6-production-deployment)
9. [Part 7: Domain & SSL Setup](#-part-7-domain--ssl-setup)
10. [API Reference](#-api-reference)
11. [Troubleshooting](#-troubleshooting)

---

## 🛠 Tech Stack

| Layer | Technology |
|-------|------------|
| **AI Development** | Claude Code CLI, PRP (Product Requirement Plan) |
| **Backend** | FastAPI, SQLModel, PostgreSQL, JWT Auth, WeasyPrint |
| **Frontend** | React 18, Vite, TypeScript, TailwindCSS, React Query |
| **Infrastructure** | Docker, Nginx, Let's Encrypt, Ubuntu VPS |

---

## 🤖 Part 0: Claude Code & PRP Setup

This section covers setting up Claude Code and the AI_FullStack_Development_Kit pattern for AI-assisted development.

### Step 0.1: Install Claude Code CLI

Claude Code is Anthropic's official CLI tool for AI-assisted development.

```bash
# Install Claude Code globally using npm
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version

# Login to Claude (requires Anthropic API key or Claude Pro subscription)
claude login
```

### Step 0.2: Create Project Root Directory

```bash
mkdir invoice-generator && cd invoice-generator
```

### Step 0.3: Create Claude Code Configuration

Create the `.claude` directory for Claude Code settings:

```bash
mkdir -p .claude/commands
```

**Create `.claude/settings.local.json`:**

```bash
cat > .claude/settings.local.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(npm:*)",
      "Bash(npx:*)",
      "Bash(git:*)",
      "Bash(docker:*)",
      "Bash(docker-compose:*)",
      "Bash(python:*)",
      "Bash(pip:*)",
      "Bash(alembic:*)",
      "Bash(pytest:*)",
      "Read",
      "Write",
      "Edit"
    ],
    "deny": []
  }
}
EOF
```

### Step 0.4: Create Custom Slash Commands

**Create `/generate-prp` command - `.claude/commands/generate-prp.md`:**

```bash
cat > .claude/commands/generate-prp.md << 'EOF'
# Generate Product Requirement Plan (PRP)

You are an expert software architect. Generate a comprehensive PRP for the requested feature.

## Instructions

1. Ask clarifying questions about the feature if needed
2. Research the codebase to understand existing patterns
3. Generate a PRP following this structure:

```markdown
name: "[Feature Name]"
description: |

## Purpose
[Why we're building this]

## Goal
[What users can do with this feature]

## Success Criteria
- [ ] Criteria 1
- [ ] Criteria 2

## Implementation Blueprint

### Data Models
[Database schema and relationships]

### API Endpoints
[REST endpoints with request/response schemas]

### Frontend Components
[React components and pages]

### Tasks
1. Task 1
2. Task 2

## Validation Loop
[How to test the implementation]

## Anti-Patterns to Avoid
- Don't do X
- Don't do Y
```

4. Save the PRP to `PRPs/[feature-name].md`
EOF
```

**Create `/execute-prp` command - `.claude/commands/execute-prp.md`:**

```bash
cat > .claude/commands/execute-prp.md << 'EOF'
# Execute Product Requirement Plan

You are an expert full-stack developer. Execute the specified PRP step by step.

## Instructions

1. Read the specified PRP from `PRPs/` directory
2. Review current codebase state
3. Execute each task sequentially:
   - Create/modify files as specified
   - Run validation loops after each major step
   - Mark tasks complete in TASK.md
4. Report progress and any blockers

## Execution Rules

- Follow existing code patterns and style
- Run linters and tests after changes
- Commit after each major milestone
- Update documentation as needed
EOF
```

### Step 0.5: Create PRPs Directory Structure

```bash
mkdir -p PRPs/templates
```

**Create PRP base template - `PRPs/templates/prp_base.md`:**

```bash
cat > PRPs/templates/prp_base.md << 'EOF'
# PRP Template

name: "[Feature Name]"
description: |

## Purpose
[Business value and why we're building this]

## Goal
[What users/developers can accomplish]

## Why
- **Business value**: [Value proposition]
- **Integration**: [How it fits with existing system]
- **Problems solved**: [Pain points addressed]

## What
[Detailed feature description]

### Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## All Needed Context

### Documentation & References
```yaml
- url: [Documentation URL]
  why: [Reason for reference]
```

### Known Gotchas
```python
# CRITICAL: [Gotcha description]
# Example: [How to handle it]
```

## Implementation Blueprint

### Data Models
```python
# Model definitions
```

### API Endpoints
```python
# Endpoint definitions
```

### Frontend Components
```typescript
// Component definitions
```

### Tasks
1. Task 1: [Description]
2. Task 2: [Description]

## Validation Loop

### Level 1: Syntax & Style
```bash
# Linting commands
```

### Level 2: Unit Tests
```bash
# Test commands
```

### Level 3: Integration Tests
```bash
# Integration test commands
```

## Anti-Patterns to Avoid
- ❌ Don't [anti-pattern 1]
- ❌ Don't [anti-pattern 2]

## Confidence Score: X/10
EOF
```

### Step 0.6: Create Agents Directory

```bash
mkdir -p agents
```

**Create Backend Agent - `agents/backend_agent.md`:**

```bash
cat > agents/backend_agent.md << 'EOF'
# Backend Development Agent

You are an expert Python/FastAPI backend developer.

## Expertise
- FastAPI REST API development
- SQLModel/SQLAlchemy ORM
- PostgreSQL database design
- JWT authentication
- Async Python programming
- WeasyPrint PDF generation

## Guidelines

### Code Style
- Follow PEP8
- Use type hints everywhere
- Format with black
- Use pydantic for validation

### Architecture
- Thin route handlers in `api/`
- Business logic in `services/`
- Database models in `models/`
- Validation schemas in `schemas/`
- Security/config in `core/`

### Best Practices
- Never expose raw database errors
- Always validate input with Pydantic
- Use dependency injection for database sessions
- Handle all exceptions explicitly
- Write docstrings for all functions

### File Size Limit
- Maximum 500 lines per file
- Split large modules into submodules
EOF
```

**Create Frontend Agent - `agents/frontend_agent.md`:**

```bash
cat > agents/frontend_agent.md << 'EOF'
# Frontend Development Agent

You are an expert React/TypeScript frontend developer.

## Expertise
- React 18 with hooks
- TypeScript strict mode
- TailwindCSS styling
- React Query for server state
- React Router for navigation
- Vite build tooling

## Guidelines

### Code Style
- Use functional components only
- Prefer named exports
- Use TypeScript interfaces for props
- Keep components under 200 lines

### Architecture
- UI components in `components/ui/`
- Feature components in `components/features/`
- Page components in `pages/`
- API calls in `api/`
- Custom hooks in `hooks/`

### Best Practices
- Extract reusable logic into hooks
- Use React Query for all API calls
- Handle loading and error states
- Use proper TypeScript types (avoid `any`)
- Keep state close to where it's used
EOF
```

### Step 0.7: Create Skills Directory

```bash
mkdir -p skills/{backend,frontend}
```

**Create Backend Skill - `skills/backend/SKILL.md`:**

```bash
cat > skills/backend/SKILL.md << 'EOF'
# Backend Development Skill

## FastAPI Patterns

### Router Setup
```python
from fastapi import APIRouter, Depends, HTTPException, status
from app.api.deps import DbSession, CurrentUser

router = APIRouter()

@router.get("/items")
def list_items(db: DbSession, current_user: CurrentUser):
    """List all items for current user."""
    return db.query(Item).filter(Item.user_id == current_user.id).all()
```

### Service Layer
```python
# services/item.py
from sqlmodel import Session
from app.models import Item

def create_item(db: Session, data: ItemCreate, user_id: UUID) -> Item:
    """Create a new item."""
    item = Item(**data.dict(), user_id=user_id)
    db.add(item)
    db.commit()
    db.refresh(item)
    return item
```

### Error Handling
```python
from fastapi import HTTPException, status

def get_item_or_404(db: Session, item_id: UUID, user_id: UUID) -> Item:
    item = db.get(Item, item_id)
    if not item or item.user_id != user_id:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Item not found"
        )
    return item
```

## SQLModel Patterns

### Relationships
```python
class Parent(SQLModel, table=True):
    id: UUID = Field(default_factory=uuid4, primary_key=True)
    children: list["Child"] = Relationship(back_populates="parent")

class Child(SQLModel, table=True):
    id: UUID = Field(default_factory=uuid4, primary_key=True)
    parent_id: UUID = Field(foreign_key="parent.id")
    parent: "Parent" = Relationship(back_populates="children")
```

## WeasyPrint PDF Generation
```python
from weasyprint import HTML
from jinja2 import Environment, FileSystemLoader

def generate_pdf(template_name: str, context: dict) -> bytes:
    env = Environment(loader=FileSystemLoader("templates/pdf"))
    template = env.get_template(f"{template_name}.html")
    html = template.render(**context)
    return HTML(string=html).write_pdf()
```
EOF
```

**Create Frontend Skill - `skills/frontend/SKILL.md`:**

```bash
cat > skills/frontend/SKILL.md << 'EOF'
# Frontend Development Skill

## React Query Patterns

### Custom Hook for API
```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'
import apiClient from '../api/client'

export function useItems() {
  return useQuery({
    queryKey: ['items'],
    queryFn: async () => {
      const response = await apiClient.get('/items')
      return response.data
    }
  })
}

export function useCreateItem() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: async (data: ItemCreate) => {
      const response = await apiClient.post('/items', data)
      return response.data
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['items'] })
    }
  })
}
```

## Form Patterns

### Controlled Form
```typescript
import { useState } from 'react'

interface FormData {
  name: string
  email: string
}

export function MyForm({ onSubmit }: { onSubmit: (data: FormData) => void }) {
  const [formData, setFormData] = useState<FormData>({ name: '', email: '' })

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setFormData(prev => ({ ...prev, [e.target.name]: e.target.value }))
  }

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    onSubmit(formData)
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" value={formData.name} onChange={handleChange} />
      <input name="email" value={formData.email} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  )
}
```

## Auth Context Pattern
```typescript
import { createContext, useContext, useState, ReactNode } from 'react'

interface AuthContextType {
  user: User | null
  login: (token: string) => Promise<void>
  logout: () => void
}

const AuthContext = createContext<AuthContextType | undefined>(undefined)

export function useAuth() {
  const context = useContext(AuthContext)
  if (!context) throw new Error('useAuth must be used within AuthProvider')
  return context
}
```
EOF
```

### Step 0.8: Create Sample Invoice Examples

```bash
mkdir -p examples/sample_invoices
```

**Create sample invoice - `examples/sample_invoices/invoice_001.json`:**

```bash
cat > examples/sample_invoices/invoice_001.json << 'EOF'
{
  "invoice_number": "INV-2025-001",
  "issue_date": "2025-01-15",
  "due_date": "2025-02-15",
  "status": "sent",
  "currency": "USD",
  "user": {
    "full_name": "John Doe",
    "company_name": "Acme Corp",
    "company_address": "123 Main St, San Francisco, CA 94102",
    "email": "john@acme.com",
    "tax_id": "12-3456789"
  },
  "client": {
    "name": "Jane Smith",
    "company_name": "Tech Solutions Inc",
    "email": "jane@techsolutions.com",
    "address": "456 Oak Ave",
    "city": "Los Angeles",
    "state": "CA",
    "postal_code": "90001",
    "country": "USA"
  },
  "items": [
    {
      "description": "Website Development",
      "quantity": 40,
      "unit": "hours",
      "unit_price": 150.00,
      "amount": 6000.00
    },
    {
      "description": "Logo Design",
      "quantity": 1,
      "unit": "project",
      "unit_price": 500.00,
      "amount": 500.00
    }
  ],
  "subtotal": 6500.00,
  "tax_rate": 8.5,
  "tax_amount": 552.50,
  "discount_rate": 0,
  "discount_amount": 0,
  "total": 7052.50,
  "notes": "Thank you for your business!",
  "terms": "Payment due within 30 days"
}
EOF
```

### Step 0.9: Create Project Documentation Files

**Create CLAUDE.md - AI Instructions:**

```bash
cat > CLAUDE.md << 'EOF'
### Project Awareness & Context
- **Always read `PLANNING.md`** at the start of a new conversation to understand the project's architecture, goals, style, and constraints.
- **Check `TASK.md`** before starting a new task. If the task isn't listed, add it with a brief description and today's date.
- **Use consistent naming conventions, file structure, and architecture patterns** as described in `PLANNING.md`.

### Code Structure & Modularity
- **Never create a file longer than 500 lines of code.** If a file approaches this limit, refactor by splitting it into modules or helper files.
- **Organize code into clearly separated modules**, grouped by feature or responsibility.
  For backend this looks like:
    - `api/` - Route handlers (thin)
    - `services/` - Business logic (fat)
    - `models/` - Database models
    - `schemas/` - Pydantic validation
    - `core/` - Security, config, exceptions
  For frontend this looks like:
    - `components/ui/` - Reusable UI components
    - `components/forms/` - Form components
    - `components/layout/` - Layout components
    - `components/features/` - Feature-specific components
    - `pages/` - Page components
    - `hooks/` - Custom React hooks
    - `context/` - React context providers
    - `api/` - API client and service functions
    - `types/` - TypeScript interfaces
    - `utils/` - Helper functions

### Testing & Reliability
- **Always create Pytest unit tests for new features** (functions, classes, routes, etc).
- **Tests should live in a `/tests` folder** mirroring the main app structure.

### Style & Conventions
- **Backend**: Python, PEP8, type hints, format with `black`, use `pydantic` for validation
- **Frontend**: TypeScript, TailwindCSS, React Query, React Router

### Project Specific
- **WeasyPrint**: Requires system dependencies (libpango, libcairo) - handle in Dockerfile
- **JWT Auth**: Use python-jose for tokens, passlib for password hashing
- **CORS**: Configure for frontend-backend communication
- **PDF Templates**: Use Jinja2 with WeasyPrint, CSS @page rules for print layout
EOF
```

**Create INITIAL.md - Feature Context Template:**

```bash
cat > INITIAL.md << 'EOF'
# FEATURE

Invoice Generator MicroSaaS - A full-stack application for creating and managing professional invoices with PDF export.

# EXAMPLES

See `examples/sample_invoices/` for JSON invoice structures.

# DOCUMENTATION

- FastAPI: https://fastapi.tiangolo.com/
- SQLModel: https://sqlmodel.tiangolo.com/
- WeasyPrint: https://doc.courtbouillon.org/weasyprint/stable/
- React Query: https://tanstack.com/query/latest
- TailwindCSS: https://tailwindcss.com/docs

# OTHER CONSIDERATIONS

## Gotchas

1. **WeasyPrint Dependencies**: Requires system packages (libpango, libcairo, libgdk-pixbuf). Install in Dockerfile.

2. **pydyf Compatibility**: Pin pydyf==0.10.0 for WeasyPrint 60.2 compatibility.

3. **CORS Configuration**: Must include all frontend origins in CORS_ORIGINS.

4. **Vite Environment Variables**: Must prefix with VITE_ and are baked in at build time.

5. **SQLModel Relationships**: Use back_populates on both sides of relationships.
EOF
```

**Create PLANNING.md - Architecture Document:**

```bash
cat > PLANNING.md << 'EOF'
# Invoice Generator - Architecture & Planning

## Overview

Full-stack Invoice Generator MicroSaaS with:
- FastAPI backend with PostgreSQL
- React + TypeScript frontend
- JWT authentication
- PDF generation with WeasyPrint
- Docker deployment

## Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│    Frontend     │────▶│     Backend     │────▶│    Database     │
│  React + Vite   │     │    FastAPI      │     │   PostgreSQL    │
│  Port: 3000     │     │  Port: 8000     │     │   Port: 5432    │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

## Database Schema

```
users
├── id (UUID, PK)
├── email (unique)
├── hashed_password
├── full_name
├── company_name
├── company_address
├── phone
├── tax_id
├── is_active
└── created_at

clients
├── id (UUID, PK)
├── user_id (FK → users)
├── name
├── email
├── company_name
├── address, city, state, postal_code, country
├── phone
└── is_active

invoices
├── id (UUID, PK)
├── user_id (FK → users)
├── client_id (FK → clients)
├── invoice_number
├── issue_date, due_date
├── status (draft, sent, paid, overdue, cancelled)
├── subtotal, tax_rate, tax_amount
├── discount_rate, discount_amount, total
├── currency, notes, terms
└── template_name

invoice_items
├── id (UUID, PK)
├── invoice_id (FK → invoices)
├── description
├── quantity, unit_price, unit
├── amount
└── sort_order
```

## API Structure

```
/api/v1/
├── auth/
│   ├── POST /register
│   ├── POST /login
│   └── GET /me
├── users/
│   ├── GET /me
│   └── PUT /me
├── clients/
│   ├── GET /
│   ├── POST /
│   ├── GET /{id}
│   ├── PUT /{id}
│   └── DELETE /{id}
├── invoices/
│   ├── GET /
│   ├── POST /
│   ├── GET /{id}
│   ├── PUT /{id}
│   ├── DELETE /{id}
│   └── PUT /{id}/status
└── pdf/
    ├── GET /{id}/preview
    └── GET /{id}/download
```

## Frontend Routes

```
/login          - Login page
/register       - Registration page
/               - Dashboard (protected)
/clients        - Client list
/clients/:id    - Client detail
/invoices       - Invoice list
/invoices/new   - Create invoice
/invoices/:id   - Invoice detail
/settings       - User settings
```

## Deployment

- Docker Compose for container orchestration
- Nginx as reverse proxy
- Let's Encrypt for SSL
- PostgreSQL for database
EOF
```

**Create TASK.md - Task Tracking:**

```bash
cat > TASK.md << 'EOF'
# Task Tracking

## Completed Tasks

- [x] Project scaffolding and Claude Code setup
- [x] Backend foundation (FastAPI, config, database)
- [x] Database models (User, Client, Invoice, InvoiceItem)
- [x] Authentication system (JWT, password hashing)
- [x] API endpoints (auth, users, clients, invoices, pdf)
- [x] PDF generation with WeasyPrint
- [x] Frontend setup (React, Vite, TailwindCSS)
- [x] Auth context and protected routes
- [x] Client management UI
- [x] Invoice management UI
- [x] Dashboard with stats
- [x] Docker configuration
- [x] Production deployment
- [x] Domain and SSL setup

## Current Tasks

None - Project complete!

## Discovered During Work

- pydyf version incompatibility with WeasyPrint 60.2 - fixed by pinning pydyf==0.10.0
- libgdk-pixbuf package name difference in Alpine - use libgdk-pixbuf-2.0-0
- Vite environment variables must be set at build time, not runtime
EOF
```

### Step 0.10: Create the Feature PRP

Now create the actual PRP for the Invoice Generator:

```bash
cat > PRPs/invoice-generator.md << 'EOF'
name: "Invoice Generator MicroSaaS"
description: |

## Purpose
Build a production-ready Invoice Generator microSaaS with React + Vite frontend, FastAPI backend, PostgreSQL database, JWT authentication, and WeasyPrint PDF generation.

## Goal
Users can:
- Register and authenticate with JWT
- Manage clients (CRUD)
- Create invoices with line items and automatic calculations
- Export invoices as professional PDF documents
- Deploy using Docker Compose

## Success Criteria
- [ ] User can register, login, and logout
- [ ] User can CRUD clients
- [ ] User can create invoices with dynamic line items
- [ ] Invoice totals auto-calculate (subtotal, tax, discount, total)
- [ ] PDF generation works
- [ ] Docker Compose starts all services
- [ ] All tests pass

## Implementation Blueprint

### Data Models
- User: email, password, company info
- Client: contact details, belongs to user
- Invoice: header, status, calculations, belongs to user and client
- InvoiceItem: line items, belongs to invoice

### API Endpoints
- Auth: /register, /login, /me
- Clients: CRUD at /clients
- Invoices: CRUD at /invoices
- PDF: /pdf/{id}/preview, /pdf/{id}/download

### Tasks
1. Project scaffolding
2. Docker infrastructure
3. Backend foundation
4. Database models + migrations
5. Security module
6. Pydantic schemas
7. Service layer
8. API endpoints
9. PDF generation
10. Frontend setup
11. Auth UI
12. Client UI
13. Invoice UI
14. Dashboard
15. Testing
16. Deployment

## Validation Loop
```bash
# Backend
cd backend && ruff check app/ && mypy app/ && pytest

# Frontend
cd frontend && npm run lint && npm run type-check && npm test

# Integration
docker-compose up -d && curl http://localhost:8000/health
```

## Anti-Patterns to Avoid
- Don't hardcode secrets
- Don't skip password hashing
- Don't forget CORS
- Don't skip input validation
- Don't create files > 500 lines
EOF
```

### Step 0.11: Start Claude Code Session

Now you're ready to start developing with Claude Code:

```bash
# Navigate to project directory
cd invoice-generator

# Start Claude Code
claude

# In Claude Code, you can now use:
# - /generate-prp to create new feature PRPs
# - /execute-prp to execute a PRP
# - Or just describe what you want to build
```

### Step 0.12: Example Claude Code Session

```bash
# Start Claude Code
claude

# Example prompts to give Claude:

# 1. Execute the existing PRP:
> Execute the PRP in PRPs/invoice-generator.md. Start with task 1.

# 2. Or describe what you want:
> Build an invoice generator with FastAPI backend, React frontend,
> PostgreSQL database, and PDF generation. Users should be able to
> register, manage clients, create invoices, and download PDFs.

# 3. For specific tasks:
> Create the database models for users, clients, invoices, and invoice items

# 4. For debugging:
> The PDF download is giving CORS errors. Help me fix it.

# 5. For deployment:
> Help me deploy this to a VPS with Docker and set up SSL
```

### Step 0.13: Directory Structure After Setup

After completing Part 0, your directory should look like:

```
invoice-generator/
├── .claude/
│   ├── commands/
│   │   ├── generate-prp.md      # /generate-prp slash command
│   │   └── execute-prp.md       # /execute-prp slash command
│   └── settings.local.json      # Claude Code permissions
├── PRPs/
│   ├── templates/
│   │   └── prp_base.md          # PRP template
│   └── invoice-generator.md     # Feature PRP
├── agents/
│   ├── backend_agent.md         # Backend development guidelines
│   └── frontend_agent.md        # Frontend development guidelines
├── skills/
│   ├── backend/
│   │   └── SKILL.md             # Backend code patterns
│   └── frontend/
│       └── SKILL.md             # Frontend code patterns
├── examples/
│   └── sample_invoices/
│       └── invoice_001.json     # Sample invoice data
├── CLAUDE.md                    # AI instructions
├── INITIAL.md                   # Feature context
├── PLANNING.md                  # Architecture document
└── TASK.md                      # Task tracking
```

---

## 📁 Part 1: Project Setup

### Step 1.1: Create Project Structure

```bash
mkdir invoice-generator && cd invoice-generator

# Create directory structure
mkdir -p backend/app/{api/v1,core,models,schemas,services/pdf,templates/pdf/styles}
mkdir -p backend/alembic/versions
mkdir -p frontend/src/{api,components/{ui,layout,forms,features/{clients,invoices}},pages/{auth,dashboard,clients,invoices,settings},hooks,context,types,utils,styles}
mkdir -p nginx
mkdir -p tests/{backend,frontend}
```

### Step 1.2: Initialize Git

```bash
git init
```

### Step 1.3: Create .gitignore

```bash
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
ENV/
.venv/

# Node
node_modules/
npm-debug.log*

# Environment
.env
.env.local
!.env.example

# IDE
.idea/
.vscode/
*.swp

# Build
dist/
build/

# Database
*.db
*.sqlite3

# OS
.DS_Store
Thumbs.db

# Docker
docker-compose.override.yml

# Testing
.coverage
.pytest_cache/
htmlcov/

# Logs
*.log
logs/
EOF
```

### Step 1.4: Create Environment Example

```bash
cat > .env.example << 'EOF'
# Database
DATABASE_URL=postgresql://invoice_user:invoice_pass@localhost:5432/invoice_db

# JWT Authentication
SECRET_KEY=your-secret-key-change-in-production
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# Application
DEBUG=true
ENVIRONMENT=development

# CORS
CORS_ORIGINS=http://localhost:3000,http://localhost:5173

# Frontend
VITE_API_URL=http://localhost:8000
EOF
```

---

## 🐍 Part 2: Backend Development

### Step 2.1: Create Requirements Files

**backend/requirements.txt:**
```bash
cat > backend/requirements.txt << 'EOF'
# FastAPI and server
fastapi==0.109.0
uvicorn[standard]==0.27.0
python-multipart==0.0.6

# Database
sqlmodel==0.0.14
psycopg2-binary==2.9.9
alembic==1.13.1

# Authentication
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
bcrypt==4.0.1

# Settings
pydantic-settings==2.1.0
python-dotenv==1.0.0

# PDF Generation
weasyprint==60.2
pydyf==0.10.0
jinja2==3.1.3

# Utilities
email-validator==2.1.0
EOF
```

**backend/requirements-dev.txt:**
```bash
cat > backend/requirements-dev.txt << 'EOF'
-r requirements.txt

# Testing
pytest==7.4.4
pytest-asyncio==0.23.3
httpx==0.26.0

# Code quality
ruff==0.1.14
mypy==1.8.0
black==24.1.1

# Type stubs
types-passlib==1.7.7.20240106
EOF
```

### Step 2.2: Create Configuration

**backend/app/config.py:**
```python
cat > backend/app/config.py << 'EOF'
"""Application configuration using Pydantic Settings."""

from functools import lru_cache
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    """Application settings loaded from environment variables."""

    # Database
    DATABASE_URL: str = "postgresql://invoice_user:invoice_pass@localhost:5432/invoice_db"

    # JWT Authentication
    SECRET_KEY: str = "change-this-in-production"
    ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 30

    # Application
    DEBUG: bool = False
    ENVIRONMENT: str = "development"
    APP_NAME: str = "Invoice Generator API"
    APP_VERSION: str = "1.0.0"

    # CORS
    CORS_ORIGINS: str = "http://localhost:3000,http://localhost:5173"

    @property
    def cors_origins_list(self) -> list[str]:
        """Parse CORS origins string into a list."""
        return [origin.strip() for origin in self.CORS_ORIGINS.split(",")]

    class Config:
        env_file = ".env"
        env_file_encoding = "utf-8"
        case_sensitive = True


@lru_cache
def get_settings() -> Settings:
    """Get cached settings instance."""
    return Settings()


settings = get_settings()
EOF
```

### Step 2.3: Create Database Connection

**backend/app/database.py:**
```python
cat > backend/app/database.py << 'EOF'
"""Database connection and session management."""

from sqlmodel import SQLModel, Session, create_engine
from app.config import settings

# Create engine
engine = create_engine(
    settings.DATABASE_URL,
    echo=settings.DEBUG,
)


def create_db_and_tables():
    """Create all database tables."""
    SQLModel.metadata.create_all(engine)


def get_session():
    """Get database session."""
    with Session(engine) as session:
        yield session
EOF
```

### Step 2.4: Create Database Models

**backend/app/models/user.py:**
```python
cat > backend/app/models/user.py << 'EOF'
"""User database model."""

import uuid
from datetime import datetime
from typing import TYPE_CHECKING, Optional
from sqlmodel import Field, Relationship, SQLModel

if TYPE_CHECKING:
    from app.models.client import Client
    from app.models.invoice import Invoice


class User(SQLModel, table=True):
    """User model for authentication and ownership."""

    __tablename__ = "users"

    id: uuid.UUID = Field(default_factory=uuid.uuid4, primary_key=True)
    email: str = Field(unique=True, index=True, max_length=255)
    hashed_password: str = Field(max_length=255)
    full_name: str = Field(max_length=255)
    company_name: Optional[str] = Field(default=None, max_length=255)
    company_address: Optional[str] = Field(default=None)
    phone: Optional[str] = Field(default=None, max_length=50)
    tax_id: Optional[str] = Field(default=None, max_length=100)
    is_active: bool = Field(default=True)
    created_at: datetime = Field(default_factory=datetime.utcnow)

    # Relationships
    clients: list["Client"] = Relationship(back_populates="user")
    invoices: list["Invoice"] = Relationship(back_populates="user")
EOF
```

**backend/app/models/client.py:**
```python
cat > backend/app/models/client.py << 'EOF'
"""Client database model."""

import uuid
from typing import TYPE_CHECKING, Optional
from sqlmodel import Field, Relationship, SQLModel

if TYPE_CHECKING:
    from app.models.user import User
    from app.models.invoice import Invoice


class Client(SQLModel, table=True):
    """Client model for invoice recipients."""

    __tablename__ = "clients"

    id: uuid.UUID = Field(default_factory=uuid.uuid4, primary_key=True)
    user_id: uuid.UUID = Field(foreign_key="users.id", index=True)
    name: str = Field(max_length=255)
    email: str = Field(max_length=255)
    company_name: Optional[str] = Field(default=None, max_length=255)
    address: Optional[str] = Field(default=None)
    city: Optional[str] = Field(default=None, max_length=100)
    state: Optional[str] = Field(default=None, max_length=100)
    postal_code: Optional[str] = Field(default=None, max_length=20)
    country: Optional[str] = Field(default=None, max_length=100)
    phone: Optional[str] = Field(default=None, max_length=50)
    is_active: bool = Field(default=True)

    # Relationships
    user: "User" = Relationship(back_populates="clients")
    invoices: list["Invoice"] = Relationship(back_populates="client")
EOF
```

**backend/app/models/invoice.py:**
```python
cat > backend/app/models/invoice.py << 'EOF'
"""Invoice database model."""

import uuid
from datetime import date
from enum import Enum
from typing import TYPE_CHECKING, Optional
from sqlmodel import Field, Relationship, SQLModel

if TYPE_CHECKING:
    from app.models.user import User
    from app.models.client import Client
    from app.models.invoice_item import InvoiceItem


class InvoiceStatus(str, Enum):
    """Invoice status options."""
    DRAFT = "draft"
    SENT = "sent"
    PAID = "paid"
    OVERDUE = "overdue"
    CANCELLED = "cancelled"


class Invoice(SQLModel, table=True):
    """Invoice model."""

    __tablename__ = "invoices"

    id: uuid.UUID = Field(default_factory=uuid.uuid4, primary_key=True)
    user_id: uuid.UUID = Field(foreign_key="users.id", index=True)
    client_id: uuid.UUID = Field(foreign_key="clients.id", index=True)
    invoice_number: str = Field(max_length=50, index=True)
    issue_date: date = Field(default_factory=date.today)
    due_date: date
    status: InvoiceStatus = Field(default=InvoiceStatus.DRAFT)
    subtotal: float = Field(default=0.0)
    tax_rate: float = Field(default=0.0)
    tax_amount: float = Field(default=0.0)
    discount_rate: float = Field(default=0.0)
    discount_amount: float = Field(default=0.0)
    total: float = Field(default=0.0)
    currency: str = Field(default="USD", max_length=3)
    notes: Optional[str] = Field(default=None)
    terms: Optional[str] = Field(default=None)
    template_name: str = Field(default="default", max_length=50)

    # Relationships
    user: "User" = Relationship(back_populates="invoices")
    client: "Client" = Relationship(back_populates="invoices")
    items: list["InvoiceItem"] = Relationship(
        back_populates="invoice",
        sa_relationship_kwargs={"cascade": "all, delete-orphan"}
    )
EOF
```

**backend/app/models/invoice_item.py:**
```python
cat > backend/app/models/invoice_item.py << 'EOF'
"""Invoice item database model."""

import uuid
from typing import TYPE_CHECKING, Optional
from sqlmodel import Field, Relationship, SQLModel

if TYPE_CHECKING:
    from app.models.invoice import Invoice


class InvoiceItem(SQLModel, table=True):
    """Invoice line item model."""

    __tablename__ = "invoice_items"

    id: uuid.UUID = Field(default_factory=uuid.uuid4, primary_key=True)
    invoice_id: uuid.UUID = Field(foreign_key="invoices.id", index=True)
    description: str = Field(max_length=500)
    quantity: float = Field(default=1.0)
    unit_price: float = Field(default=0.0)
    unit: Optional[str] = Field(default=None, max_length=50)
    amount: float = Field(default=0.0)
    sort_order: int = Field(default=0)

    # Relationships
    invoice: "Invoice" = Relationship(back_populates="items")
EOF
```

**backend/app/models/__init__.py:**
```python
cat > backend/app/models/__init__.py << 'EOF'
"""Database models package."""

from app.models.user import User
from app.models.client import Client
from app.models.invoice import Invoice, InvoiceStatus
from app.models.invoice_item import InvoiceItem

__all__ = ["User", "Client", "Invoice", "InvoiceStatus", "InvoiceItem"]
EOF
```

### Step 2.5: Create Security Module

**backend/app/core/security.py:**
```python
cat > backend/app/core/security.py << 'EOF'
"""Security utilities for authentication."""

from datetime import datetime, timedelta
from typing import Optional
import uuid

from jose import JWTError, jwt
from passlib.context import CryptContext

from app.config import settings

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")


def verify_password(plain_password: str, hashed_password: str) -> bool:
    """Verify a password against a hash."""
    return pwd_context.verify(plain_password, hashed_password)


def get_password_hash(password: str) -> str:
    """Hash a password."""
    return pwd_context.hash(password)


def create_access_token(subject: uuid.UUID, expires_delta: Optional[timedelta] = None) -> str:
    """Create a JWT access token."""
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=settings.ACCESS_TOKEN_EXPIRE_MINUTES)

    to_encode = {"sub": str(subject), "exp": expire}
    encoded_jwt = jwt.encode(to_encode, settings.SECRET_KEY, algorithm=settings.ALGORITHM)
    return encoded_jwt


def verify_token(token: str) -> Optional[uuid.UUID]:
    """Verify a JWT token and return the user ID."""
    try:
        payload = jwt.decode(token, settings.SECRET_KEY, algorithms=[settings.ALGORITHM])
        user_id: str = payload.get("sub")
        if user_id is None:
            return None
        return uuid.UUID(user_id)
    except JWTError:
        return None
EOF
```

**backend/app/core/__init__.py:**
```python
cat > backend/app/core/__init__.py << 'EOF'
"""Core package."""
EOF
```

### Step 2.6: Create API Dependencies

**backend/app/api/deps.py:**
```python
cat > backend/app/api/deps.py << 'EOF'
"""API dependencies."""

from typing import Annotated

from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from sqlmodel import Session

from app.database import get_session
from app.core.security import verify_token
from app.models.user import User

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/v1/auth/login")

DbSession = Annotated[Session, Depends(get_session)]


def get_current_user(
    db: DbSession,
    token: Annotated[str, Depends(oauth2_scheme)],
) -> User:
    """Get current authenticated user."""
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )

    user_id = verify_token(token)
    if user_id is None:
        raise credentials_exception

    user = db.get(User, user_id)
    if user is None:
        raise credentials_exception

    if not user.is_active:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Inactive user",
        )

    return user


CurrentUser = Annotated[User, Depends(get_current_user)]
EOF
```

**backend/app/api/__init__.py:**
```python
cat > backend/app/api/__init__.py << 'EOF'
"""API package."""
EOF
```

### Step 2.7: Create Auth Endpoints

**backend/app/api/v1/auth.py:**
```python
cat > backend/app/api/v1/auth.py << 'EOF'
"""Authentication API endpoints."""

from datetime import timedelta

from fastapi import APIRouter, HTTPException, status
from fastapi.security import OAuth2PasswordRequestForm
from typing import Annotated

from app.api.deps import DbSession, CurrentUser
from app.config import settings
from app.core.security import create_access_token, get_password_hash, verify_password
from app.models.user import User
from app.schemas.auth import Token
from app.schemas.user import UserCreate, UserRead

router = APIRouter()


@router.post("/register", response_model=Token)
def register(user_data: UserCreate, db: DbSession) -> Token:
    """Register a new user."""
    # Check if email already exists
    existing_user = db.query(User).filter(User.email == user_data.email).first()
    if existing_user:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Email already registered",
        )

    # Create user
    user = User(
        email=user_data.email,
        hashed_password=get_password_hash(user_data.password),
        full_name=user_data.full_name,
        company_name=user_data.company_name,
    )
    db.add(user)
    db.commit()
    db.refresh(user)

    # Create access token
    access_token = create_access_token(
        subject=user.id,
        expires_delta=timedelta(minutes=settings.ACCESS_TOKEN_EXPIRE_MINUTES),
    )

    return Token(access_token=access_token, token_type="bearer")


@router.post("/login", response_model=Token)
def login(
    db: DbSession,
    form_data: Annotated[OAuth2PasswordRequestForm, Depends()],
) -> Token:
    """Login and get access token."""
    user = db.query(User).filter(User.email == form_data.username).first()

    if not user or not verify_password(form_data.password, user.hashed_password):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect email or password",
            headers={"WWW-Authenticate": "Bearer"},
        )

    if not user.is_active:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Inactive user",
        )

    access_token = create_access_token(
        subject=user.id,
        expires_delta=timedelta(minutes=settings.ACCESS_TOKEN_EXPIRE_MINUTES),
    )

    return Token(access_token=access_token, token_type="bearer")


@router.get("/me", response_model=UserRead)
def get_current_user_info(current_user: CurrentUser) -> UserRead:
    """Get current user information."""
    return current_user


from fastapi import Depends
EOF
```

### Step 2.8: Create Schemas

**backend/app/schemas/auth.py:**
```python
cat > backend/app/schemas/auth.py << 'EOF'
"""Authentication schemas."""

from pydantic import BaseModel


class Token(BaseModel):
    """JWT token response."""
    access_token: str
    token_type: str = "bearer"
EOF
```

**backend/app/schemas/user.py:**
```python
cat > backend/app/schemas/user.py << 'EOF'
"""User schemas."""

import uuid
from datetime import datetime
from typing import Optional
from pydantic import BaseModel, EmailStr


class UserCreate(BaseModel):
    """Schema for creating a user."""
    email: EmailStr
    password: str
    full_name: str
    company_name: Optional[str] = None


class UserRead(BaseModel):
    """Schema for reading a user."""
    id: uuid.UUID
    email: str
    full_name: str
    company_name: Optional[str] = None
    company_address: Optional[str] = None
    phone: Optional[str] = None
    tax_id: Optional[str] = None
    is_active: bool
    created_at: datetime

    class Config:
        from_attributes = True


class UserUpdate(BaseModel):
    """Schema for updating a user."""
    full_name: Optional[str] = None
    company_name: Optional[str] = None
    company_address: Optional[str] = None
    phone: Optional[str] = None
    tax_id: Optional[str] = None
EOF
```

**backend/app/schemas/__init__.py:**
```python
cat > backend/app/schemas/__init__.py << 'EOF'
"""Schemas package."""
EOF
```

### Step 2.9: Create Main Application

**backend/app/main.py:**
```python
cat > backend/app/main.py << 'EOF'
"""FastAPI application entry point."""

from contextlib import asynccontextmanager
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

from app.config import settings
from app.database import create_db_and_tables
from app.api.router import api_router


@asynccontextmanager
async def lifespan(app: FastAPI):
    """Application lifespan handler."""
    create_db_and_tables()
    yield


app = FastAPI(
    title=settings.APP_NAME,
    version=settings.APP_VERSION,
    description="A full-stack invoice generator application",
    lifespan=lifespan,
)

# Configure CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.cors_origins_list,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Include API router
app.include_router(api_router, prefix="/api/v1")


@app.get("/health")
def health_check():
    """Health check endpoint."""
    return {"status": "healthy", "version": settings.APP_VERSION}
EOF
```

**backend/app/__init__.py:**
```python
cat > backend/app/__init__.py << 'EOF'
"""Application package."""
EOF
```

### Step 2.10: Create Backend Dockerfile

**backend/Dockerfile:**
```dockerfile
cat > backend/Dockerfile << 'EOF'
# Multi-stage Dockerfile for Backend

# Development stage
FROM python:3.12-slim AS development

WORKDIR /app

# Install system dependencies for WeasyPrint
RUN apt-get update && apt-get install -y --no-install-recommends \
    libpango-1.0-0 \
    libpangocairo-1.0-0 \
    libcairo2 \
    libgdk-pixbuf-2.0-0 \
    libffi-dev \
    shared-mime-info \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt requirements-dev.txt ./
RUN pip install --no-cache-dir -r requirements-dev.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]


# Production stage
FROM python:3.12-slim AS production

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    libpango-1.0-0 \
    libpangocairo-1.0-0 \
    libcairo2 \
    libgdk-pixbuf-2.0-0 \
    libffi-dev \
    shared-mime-info \
    && rm -rf /var/lib/apt/lists/*

RUN adduser --disabled-password --gecos '' appuser

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN chown -R appuser:appuser /app

USER appuser

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
EOF
```

> **Note:** The complete backend includes more files (clients, invoices, PDF endpoints). See the full repository for all files.

---

## ⚛️ Part 3: Frontend Development

### Step 3.1: Initialize React Project

```bash
cd frontend
npm create vite@latest . -- --template react-ts
npm install
npm install axios react-router-dom @tanstack/react-query
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Step 3.2: Configure Tailwind

**frontend/tailwind.config.js:**
```javascript
cat > frontend/tailwind.config.js << 'EOF'
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        },
      },
    },
  },
  plugins: [],
}
EOF
```

### Step 3.3: Create API Client

**frontend/src/api/client.ts:**
```typescript
cat > frontend/src/api/client.ts << 'EOF'
import axios from 'axios'

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:8000'

const apiClient = axios.create({
  baseURL: `${API_URL}/api/v1`,
  headers: {
    'Content-Type': 'application/json',
  },
})

// Add auth token to requests
apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem('token')
  if (token) {
    config.headers.Authorization = `Bearer ${token}`
  }
  return config
})

// Handle auth errors
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token')
      window.location.href = '/login'
    }
    return Promise.reject(error)
  }
)

export default apiClient
EOF
```

### Step 3.4: Create Auth Context

**frontend/src/context/AuthContext.tsx:**
```typescript
cat > frontend/src/context/AuthContext.tsx << 'EOF'
import { createContext, useState, useEffect, ReactNode } from 'react'
import apiClient from '../api/client'
import { User } from '../types/user'

interface AuthContextType {
  user: User | null
  isAuthenticated: boolean
  isLoading: boolean
  login: (token: string) => Promise<void>
  logout: () => void
}

export const AuthContext = createContext<AuthContextType | undefined>(undefined)

export function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<User | null>(null)
  const [isLoading, setIsLoading] = useState(true)

  useEffect(() => {
    const token = localStorage.getItem('token')
    if (token) {
      fetchUser()
    } else {
      setIsLoading(false)
    }
  }, [])

  const fetchUser = async () => {
    try {
      const response = await apiClient.get('/auth/me')
      setUser(response.data)
    } catch {
      localStorage.removeItem('token')
    } finally {
      setIsLoading(false)
    }
  }

  const login = async (token: string) => {
    localStorage.setItem('token', token)
    await fetchUser()
  }

  const logout = () => {
    localStorage.removeItem('token')
    setUser(null)
  }

  return (
    <AuthContext.Provider value={{ user, isAuthenticated: !!user, isLoading, login, logout }}>
      {children}
    </AuthContext.Provider>
  )
}
EOF
```

### Step 3.5: Create Frontend Dockerfile

**frontend/Dockerfile:**
```dockerfile
cat > frontend/Dockerfile << 'EOF'
# Development stage
FROM node:20-alpine AS development

WORKDIR /app

COPY package*.json ./
RUN npm install
COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]


# Build stage
FROM node:20-alpine AS build

WORKDIR /app

ARG VITE_API_URL=http://localhost:8000
ENV VITE_API_URL=$VITE_API_URL

COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build


# Production stage
FROM nginx:alpine AS production

COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
EOF
```

**frontend/nginx.conf:**
```nginx
cat > frontend/nginx.conf << 'EOF'
server {
    listen 80;
    server_name localhost;
    root /usr/share/nginx/html;
    index index.html;

    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
EOF
```

> **Note:** The complete frontend includes many more components. See the full repository for all files.

---

## 🐳 Part 4: Docker Configuration

### Step 4.1: Create docker-compose.yml (Production)

```yaml
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  db:
    image: postgres:16-alpine
    container_name: invoice-db
    environment:
      POSTGRES_USER: invoice_user
      POSTGRES_PASSWORD: invoice_pass
      POSTGRES_DB: invoice_db
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U invoice_user -d invoice_db"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - invoice-network

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: invoice-backend
    environment:
      DATABASE_URL: postgresql://invoice_user:invoice_pass@db:5432/invoice_db
      SECRET_KEY: ${SECRET_KEY:-change-this-in-production}
      ALGORITHM: HS256
      ACCESS_TOKEN_EXPIRE_MINUTES: 30
      CORS_ORIGINS: ${CORS_ORIGINS:-http://localhost:3000,https://yourdomain.com}
    ports:
      - "8000:8000"
    depends_on:
      db:
        condition: service_healthy
    networks:
      - invoice-network

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      args:
        VITE_API_URL: ${VITE_API_URL:-https://api.yourdomain.com}
    container_name: invoice-frontend
    ports:
      - "3000:80"
    depends_on:
      - backend
    networks:
      - invoice-network

volumes:
  postgres_data:

networks:
  invoice-network:
    driver: bridge
EOF
```

### Step 4.2: Create docker-compose.dev.yml (Development)

```yaml
cat > docker-compose.dev.yml << 'EOF'
version: '3.8'

services:
  db:
    image: postgres:16-alpine
    container_name: invoice-db-dev
    environment:
      POSTGRES_USER: invoice_user
      POSTGRES_PASSWORD: invoice_pass
      POSTGRES_DB: invoice_db
    volumes:
      - postgres_data_dev:/var/lib/postgresql/data
    ports:
      - "5433:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U invoice_user -d invoice_db"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - invoice-network-dev

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
      target: development
    container_name: invoice-backend-dev
    environment:
      DATABASE_URL: postgresql://invoice_user:invoice_pass@db:5432/invoice_db
      SECRET_KEY: dev-secret-key-not-for-production
      ALGORITHM: HS256
      ACCESS_TOKEN_EXPIRE_MINUTES: 30
      DEBUG: "true"
      CORS_ORIGINS: http://localhost:3000,http://localhost:5173
    volumes:
      - ./backend:/app
    ports:
      - "8000:8000"
    depends_on:
      db:
        condition: service_healthy
    command: uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
    networks:
      - invoice-network-dev

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      target: development
    container_name: invoice-frontend-dev
    environment:
      VITE_API_URL: http://localhost:8000
    volumes:
      - ./frontend:/app
      - /app/node_modules
    ports:
      - "3000:5173"
    depends_on:
      - backend
    command: npm run dev -- --host 0.0.0.0
    networks:
      - invoice-network-dev

volumes:
  postgres_data_dev:

networks:
  invoice-network-dev:
    driver: bridge
EOF
```

---

## 🧪 Part 5: Local Testing

### Step 5.1: Start Development Environment

```bash
docker-compose -f docker-compose.dev.yml up -d --build
```

### Step 5.2: Verify Services

```bash
docker ps
# Should show 3 containers: db, backend, frontend

curl http://localhost:8000/health
# Expected: {"status":"healthy","version":"1.0.0"}
```

### Step 5.3: Create Test User

```bash
curl -X POST http://localhost:8000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email": "demo@invoice.com", "password": "Demo@123", "full_name": "Demo User"}'
```

### Step 5.4: Access Application

| Service | URL |
|---------|-----|
| Frontend | http://localhost:3000 |
| Backend API | http://localhost:8000 |
| API Docs | http://localhost:8000/docs |

---

## 🚀 Part 6: Production Deployment

### Step 6.1: Push to GitHub

```bash
git add -A
git commit -m "Initial commit: Invoice Generator MicroSaaS"
git remote add origin https://github.com/yourusername/makeinvoice.git
git push -u origin main
```

### Step 6.2: Connect to VPS

```bash
ssh root@YOUR_SERVER_IP
```

### Step 6.3: Install Dependencies

```bash
apt update && apt install -y docker.io docker-compose git
```

### Step 6.4: Clone Repository

```bash
cd /opt
git clone https://github.com/yourusername/makeinvoice.git
cd makeinvoice
```

### Step 6.5: Create Production Environment

```bash
cat > .env << 'EOF'
DATABASE_URL=postgresql://invoice_user:invoice_pass@db:5432/invoice_db
SECRET_KEY=your-very-secure-secret-key-here
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
DEBUG=false
ENVIRONMENT=production
CORS_ORIGINS=https://yourdomain.com,https://www.yourdomain.com,https://api.yourdomain.com
VITE_API_URL=https://api.yourdomain.com
EOF
```

### Step 6.6: Deploy

```bash
docker-compose up -d --build
```

### Step 6.7: Verify

```bash
docker ps
curl http://localhost:8000/health
```

---

## 🌐 Part 7: Domain & SSL Setup

### Step 7.1: Configure DNS

Add these A records at your domain registrar:

| Type | Name | Value |
|------|------|-------|
| A | @ | YOUR_SERVER_IP |
| A | www | YOUR_SERVER_IP |
| A | api | YOUR_SERVER_IP |

### Step 7.2: Install Nginx

```bash
apt install -y nginx
```

### Step 7.3: Configure Nginx

```bash
cat > /etc/nginx/sites-available/makeinvoice << 'EOF'
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

server {
    listen 80;
    server_name api.yourdomain.com;

    location / {
        proxy_pass http://localhost:8000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
EOF
```

### Step 7.4: Enable Site

```bash
rm -f /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/makeinvoice /etc/nginx/sites-enabled/
nginx -t && systemctl restart nginx
```

### Step 7.5: Install SSL

```bash
apt install -y certbot python3-certbot-nginx

certbot --nginx -d yourdomain.com -d www.yourdomain.com -d api.yourdomain.com \
  --non-interactive --agree-tos --email admin@yourdomain.com
```

### Step 7.6: Create Demo User

```bash
curl -X POST https://api.yourdomain.com/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email": "demo@invoice.com", "password": "Demo@123", "full_name": "Demo User", "company_name": "Demo Company"}'
```

---

## 📚 API Reference

### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/auth/register` | Register new user |
| POST | `/api/v1/auth/login` | Login (OAuth2 form) |
| GET | `/api/v1/auth/me` | Get current user |

### Clients
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/clients` | List clients |
| POST | `/api/v1/clients` | Create client |
| GET | `/api/v1/clients/{id}` | Get client |
| PUT | `/api/v1/clients/{id}` | Update client |
| DELETE | `/api/v1/clients/{id}` | Delete client |

### Invoices
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/invoices` | List invoices |
| POST | `/api/v1/invoices` | Create invoice |
| GET | `/api/v1/invoices/{id}` | Get invoice |
| PUT | `/api/v1/invoices/{id}` | Update invoice |
| DELETE | `/api/v1/invoices/{id}` | Delete invoice |
| PUT | `/api/v1/invoices/{id}/status` | Update status |

### PDF
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/pdf/{id}/preview` | Preview as HTML |
| GET | `/api/v1/pdf/{id}/download` | Download as PDF |

---

## 🔧 Troubleshooting

### Docker Compose Errors

```bash
docker-compose down
docker system prune -f
docker-compose up -d --build
```

### Frontend Calling localhost

Rebuild frontend with correct API URL:
```bash
docker-compose build --no-cache frontend
docker-compose up -d
```

### CORS Errors

Update `CORS_ORIGINS` in `.env` with all your domains.

### SSL Certificate Fails

1. Ensure DNS points to your server
2. Remove duplicate A records
3. Verify Nginx is running

### View Logs

```bash
docker logs invoice-backend
docker logs invoice-frontend
docker logs invoice-db
```

---

## 🔄 Updating Application

```bash
cd /opt/makeinvoice
git pull origin main
docker-compose down
docker-compose up -d --build
```

---

## 📝 License

MIT License

---

## 🙏 Credits

- Built with [Claude Code](https://claude.ai/claude-code)
- Hosted on [Hostinger VPS](https://hostinger.com)
- SSL by [Let's Encrypt](https://letsencrypt.org)

---

**🎉 Congratulations! Your Invoice Generator MicroSaaS is live!**
