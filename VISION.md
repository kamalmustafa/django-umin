# django-umin Vision

## Overview

**django-umin** is a Django framework for rapidly building business applications by defining business model definitions in code. It handles wiring components together, adding workflow capabilities, and providing advanced data management—all through code-only configuration, never through web-based interfaces.

## Core Philosophy

- **Code-First**: All configuration through Python code. No web-based form builders or drag-drop UI designers.
- **Business Logic Ready**: Built-in support for workflows, validations, and state machines.
- **Django Native**: Leverages Django's ORM, auth, and admin patterns while providing a more modern, HTMX-powered UI.
- **Minimal Dependencies**: No unnecessary packages. Pure Django + HTMX + Tailwind.

## Comparison with Frappe

| Feature | Frappe Framework | django-umin |
|---------|-----------------|-------------|
| Configuration | Web-based + Code | Code-only |
| UI Pattern | Server-side rendered | HTMX + Tailwind |
| DocTypes | Database-driven | Model-driven |
| Workflows | Built-in | Built-in |
| Web Development | Full-stack (bench) | Django-native |

## Target Applications

- Point-of-Sale (PoS) Systems
- Clinic/Hospital Management
- School Management Systems
- ERP Systems
- CRM Applications
- Inventory Management
- HR Management Systems

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                  django-umin                         │
├─────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │
│  │   Models    │  │   Views     │  │  Workflows  │  │
│  │  (CRUDView) │  │  (HTMX UI)  │  │  (State)    │  │
│  └─────────────┘  └─────────────┘  └─────────────┘  │
├─────────────────────────────────────────────────────┤
│                  Django ORM                          │
└─────────────────────────────────────────────────────┘
```

## Core Components

### 1. DocType System

Define business models with rich metadata:

```python
class SalesInvoice(DocType):
    name = CharField(max_length=50)
    customer = LinkField("Customer")
    posting_date = DateField()
    items = ChildTable(SalesInvoiceItem)
    status = SelectField(choices=["Draft", "Submitted", "Paid", "Cancelled"])
    
    workflow = "SalesInvoiceWorkflow"
    
    class Meta:
        app_label = "sales"
        default_actions = ["submit", "cancel", "print"]
```

### 2. Workflow Engine

Define state transitions and actions:

```python
class SalesInvoiceWorkflow(Workflow):
    states = [
        ("Draft", ["submit"]),
        ("Submitted", ["pay", "cancel"]),
        ("Paid", ["return"]),
        ("Cancelled", []),
    ]
    
    on_transition = "validate_invoice"
```

### 3. Form Builder (Code-Based)

Rich form configuration without UI builders:

```python
class SalesInvoiceForm(DocTypeForm):
    items = InlineForm(SalesInvoiceItem, fields=["item", "qty", "rate", "amount"])
    
    def validate(self):
        self.validate_amounts()
        self.calculate_totals()
```

### 4. Auto-Generated UI

HTMX-powered interfaces from DocType definitions:

- List views with filtering, sorting, search
- Form views with inline child tables
- Dashboard views with analytics widgets
- Print formats for invoices/reports

### 5. Data Management

- Soft delete with versioning
- Audit trail
- Data validation rules
- Calculated fields

---

# Roadmap

## Phase 1: Foundation (v0.1 - v0.5)

### v0.1 - Core CRUD Enhancement
- [ ] **Current**: Basic CRUDView with list_display, search, filters
- [ ] Add support for `LinkField` (foreign key dropdowns with search)
- [ ] Add support for `SelectField` (choice fields)
- [ ] Add support for `DateField` with date picker
- [ ] Add support for `DatetimeField` with datetime picker
- [ ] Add support for `DecimalField` with number formatting
- [ ] Add basic inline editing for list views

### v0.2 - DocType System
- [ ] Create `DocType` base class for business models
- [ ] Add `ChildTable` support for one-to-many relationships
- [ ] Add `LinkField` for model relationships
- [ ] Add `SelectField` for choices
- [ ] Add `DecimalField` with currency formatting
- [ ] Add `DateField` with calendar picker

### v0.3 - Advanced List Features
- [ ] Add column sorting (server-side)
- [ ] Add column visibility toggle
- [ ] Add list view customization (save user preferences)
- [ ] Add list view grouping/aggregation
- [ ] Add calendar view for date-based models
- [ ] Add kanban view for status-based models

### v0.4 - Rich Forms
- [ ] Add `InlineForm` for child tables
- [ ] Add `TabbedInterface` for complex forms
- [ ] Add `SectionBreak` and `ColumnBreak` for layout
- [ ] Add conditional field visibility
- [ ] Add dependent field updates (cascading selects)
- [ ] Add file/image upload handling

### v0.5 - Data Validation & Management
- [ ] Add `validate_<field_name>` methods
- [ ] Add `@validates` decorator for field validation
- [ ] Add cross-field validation
- [ ] Add unique constraints at DocType level
- [ ] Add server-side validation messages
- [ ] Add soft delete with `is_deleted` flag
- [ ] Add versioning for all models
- [ ] Add audit trail logging
- [ ] Add calculated/read-only fields
- [ ] Add data import/export (CSV, Excel)

## Phase 2: Workflow Engine (v0.6 - v0.8)

### v0.6 - Workflow Core
- [ ] Create `Workflow` class for state management
- [ ] Define states and transitions in code
- [ ] Add workflow visualization (graph generation)
- [ ] Add workflow action buttons to UI

### v0.7 - Workflow Actions
- [ ] Add `on_enter` and `on_leave` callbacks
- [ ] Add validation hooks before transitions
- [ ] Add workflow permissions (role-based)
- [ ] Add workflow history/audit trail

### v0.8 - Advanced Workflows
- [ ] Add nested workflows (sub-workflows)
- [ ] Add workflow templates (reusable patterns)
- [ ] Add workflow conditions (field-based)
- [ ] Add workflow automation (scheduled transitions)

## Phase 3: User Interface (v0.9 - v1.1)

### v0.9 - Dashboard System
- [ ] Create `Dashboard` class
- [ ] Add chart widgets (bar, line, pie, doughnut)
- [ ] Add number card widgets
- [ ] Add table widgets with sparklines
- [ ] Add dashboard customization in code

### v1.0 - Print Formats
- [ ] Create `PrintFormat` class
- [ ] Add Jinja2-based print templates
- [ ] Add print CSS styling
- [ ] Add PDF generation support
- [ ] Add multi-format support (PDF, HTML, Excel)

### v1.1 - UI Refinements
- [ ] Add keyboard shortcuts
- [ ] Add bulk actions UI
- [ ] Add advanced search (query builder)
- [ ] Add notifications/toast messages
- [ ] Add mobile-responsive improvements

## Phase 4: Enterprise Features (v1.2 - v1.4)

### v1.2 - Multi-Tenancy
- [ ] Add tenant isolation at model level
- [ ] Add tenant-aware queries
- [ ] Add tenant settings/configuration
- [ ] Add cross-tenant operations (admin only)

### v1.3 - Permissions
- [ ] Create `Permission` system
- [ ] Add role-based access control
- [ ] Add field-level permissions
- [ ] Add document-level permissions
- [ ] Add API key management

### v1.4 - Integration
- [ ] Add REST API generation
- [ ] Add WebSocket support for real-time updates
- [ ] Add webhook system
- [ ] Add background task integration (Celery)
- [ ] Add file attachment handling

## Phase 5: Developer Experience (v1.5 - v1.7)

### v1.5 - CLI Tools
- [ ] Add `manage.py umin` command group
- [ ] Add `umin generate doctype` command
- [ ] Add `umin migrate` for schema management
- [ ] Add `umin doctor` for system diagnostics

### v1.6 - Developer Utilities
- [ ] Add boilerplate generation
- [ ] Add migration helpers
- [ ] Add testing utilities
- [ ] Add debug toolbar integration

### v1.7 - Documentation & Examples
- [ ] Complete API documentation
- [ ] Add example applications:
  - Point-of-Sale (PoS)
  - Clinic Management
  - School Management
- [ ] Add video tutorials

## Future Considerations (v2.0+)

- [ ] Plugin system for extensions
- [ ] Microservices support
- [ ] GraphQL API
- [ ] AI-assisted form building (code-only)
- [ ] Low-code report builder (code-only)
