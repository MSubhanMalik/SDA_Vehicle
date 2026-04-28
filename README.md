# Automobile Insurance Management System
A file-based, text-menu driven information system for an automobile insurance company, built in C++ for the SDA (Software Design and Architecture) course.

---

## Features
- Multi-role staff login (Manager, Salesman, Surveyor)
- Customer portal for filing and tracking claims
- Full claim lifecycle: File → Inspect → Approve/Reject → Settle
- File-based persistence (no DBMS required)
- Clean 3-layer architecture: Models → Business Services → UI

---

## Project Structure

```
insurance/
├── main.cpp                  # Entry point, system initialization
├── Makefile                  # Build script
├── data/                     # Auto-created on first run (data files)
├── include/
│   └── Utils.h               # Shared utilities (date, string split, sanitize)
└── src/
    ├── models/               # Plain data structs with serialize/deserialize
    │   ├── Customer.h
    │   ├── Vehicle.h
    │   ├── Staff.h
    │   ├── Policy.h
    │   ├── Claim.h
    │   ├── Inspection.h
    │   └── Workshop.h
    ├── repositories/         # File I/O layer
    │   ├── FileRepository.h  # Generic template repository (CRUD)
    │   └── DataStore.h       # Singleton — central access to all repositories
    ├── services/             # Business logic layer
    │   ├── CustomerService.h
    │   ├── VehicleService.h
    │   ├── PolicyService.h
    │   ├── ClaimService.h
    │   ├── InspectionService.h
    │   ├── StaffService.h
    │   ├── WorkshopService.h
    │   └── ReportService.h
    └── ui/                   # Text-based menu layer
        ├── MenuHelper.h      # Shared input/output utilities
        ├── SalesmanMenu.h
        ├── SurveyorMenu.h
        ├── ManagerMenu.h
        └── CustomerPortal.h
```

---

## How to Build

Requires GCC with C++14 support.

```bash
mkdir data
make
```

This produces the `insurance` executable in the project root.

---

## How to Run

```bash
./insurance
```

On first run, the system automatically creates a default manager account:
- **Username:** `admin`
- **Password:** `admin123`

---

## Typical Workflow

### Step 1 — Manager: Setup
1. Login as `admin` / `admin123`
2. **Add Staff** (option 10): add a Salesman and a Surveyor
3. **Register Workshop** (option 8): add at least one repair workshop
4. Logout (option 0)

### Step 2 — Salesman: Onboard a Customer
1. Login as the salesman
2. **Register New Customer** (option 1) → note the Customer ID
3. **Add Vehicle** (option 3) → enter Customer ID → note the Vehicle ID
4. **Issue Policy** (option 5) → enter Customer ID and Vehicle ID

### Step 3 — Customer: File a Claim
1. Go to **Customer Portal** from the main menu
2. Enter your Customer ID
3. **File a Claim** (option 2) → select vehicle → fill in accident details

### Step 4 — Manager: Process the Claim
1. Login as manager
2. **View Pending Claims** (option 1)
3. **Schedule Inspection** (option 2) → assign surveyor and workshop
4. Wait for surveyor to submit report, then **View Inspection Report** (option 3)
5. **Approve or Reject** the claim (options 4 / 5)
6. **Settle Claim** (option 6) once payment is made

### Step 5 — Surveyor: Submit Report
1. Login as the surveyor
2. **View My Assigned Inspections** (option 1)
3. **Submit Inspection Report** (option 2) → enter findings and repair cost

---

## User Roles

| Role | Access |
|---|---|
| **Manager** | Full access: claims, inspections, reports, staff, workshops |
| **Salesman** | Register customers, add vehicles, issue policies |
| **Surveyor** | View assigned inspections, submit reports |
| **Customer** | View policies, file claims, track claim status |

---

## Policy Types
- `Comprehensive` — covers all damage including own vehicle
- `ThirdParty` — covers damage to third parties only
- `FireAndTheft` — covers fire damage and theft

---

## Claim Lifecycle

```
PENDING → UNDER_INSPECTION → APPROVED → SETTLED
                           ↘ REJECTED
```

---

## Design Patterns Used

| Pattern | Where |
|---|---|
| **Singleton** | `DataStore` — single point of file access |
| **Generic Template** | `FileRepository<T>` — reusable CRUD for any model |
| **Composition** | `ReportService` composes multiple services; menus own their services |
| **Separation of Concerns** | UI never touches files; services never print to screen |

---

## Data Persistence

All data is stored as pipe-delimited (`|`) text files in the `data/` directory:

| File | Contents |
|---|---|
| `customers.dat` | Customer records |
| `vehicles.dat` | Vehicle records |
| `staff.dat` | Staff accounts |
| `policies.dat` | Insurance policies |
| `claims.dat` | Claim records |
| `inspections.dat` | Inspection reports |
| `workshops.dat` | Registered workshops |

---

## Debug Configuration (Cursor / VS Code)

```jsonc
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch",
            "type": "lldb",
            "request": "launch",
            "program": "${workspaceRoot}/insurance",
            "args": [],
            "cwd": "${workspaceRoot}"
        }
    ]
}
```

Make sure to run `mkdir data && make` before launching the debugger.
