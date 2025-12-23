# LegalGuard - Complete Workflow Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [Architecture Overview](#architecture-overview)
3. [Frontend Workflow](#frontend-workflow)
4. [Backend Workflow](#backend-workflow)
5. [Database Workflow](#database-workflow)
6. [Data Flow Diagrams](#data-flow-diagrams)
7. [API Endpoints Reference](#api-endpoints-reference)
8. [State Management](#state-management)
9. [Authentication Flow](#authentication-flow)
10. [Emergency Consultation Flow](#emergency-consultation-flow)
11. [Payment Processing Flow](#payment-processing-flow)
12. [Session Management Flow](#session-management-flow)
13. [Integration Points](#integration-points)

---

## Project Overview

**LegalGuard** is a React Native mobile application that connects users with legal professionals during emergency situations. The application provides on-demand legal consultation services through a streamlined mobile interface.

### Technology Stack

**Frontend:**
- React Native (Expo) - Cross-platform mobile framework
- TypeScript - Type-safe development
- React Navigation - Navigation management
- Zustand - State management
- NativeWind (Tailwind CSS) - Styling
- Expo SDK - Native device features

**Backend:**
- FastAPI - Python web framework
- Supabase - Database and backend services
- Pydantic - Data validation
- Uvicorn - ASGI server

**Database:**
- PostgreSQL (via Supabase)
- Real-time capabilities
- Row-level security

---

## Architecture Overview

### System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    React Native Mobile App                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Screens    │  │  Components  │  │   Services   │     │
│  │  (UI Layer)  │  │  (Reusable)  │  │  (API Calls) │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│         │                  │                  │            │
│         └──────────────────┴──────────────────┘            │
│                            │                                │
│                    ┌───────▼────────┐                      │
│                    │  Zustand Store │                      │
│                    │  (State Mgmt)  │                      │
│                    └───────┬────────┘                      │
└────────────────────────────┼────────────────────────────────┘
                             │
                             │ HTTP/REST API
                             │
┌────────────────────────────▼────────────────────────────────┐
│                    FastAPI Backend                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Routers    │  │   Models     │  │   Database   │     │
│  │  (Endpoints) │  │  (Schemas)   │  │  (Supabase)  │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│         │                  │                  │            │
│         └──────────────────┴──────────────────┘            │
└────────────────────────────┼────────────────────────────────┘
                             │
                             │ Supabase Client
                             │
┌────────────────────────────▼────────────────────────────────┐
│                  Supabase (PostgreSQL)                        │
│  ┌──────────────┐  ┌──────────────┐                        │
│  │   Profiles   │  │   Sessions   │                        │
│  │    Table     │  │    Table     │                        │
│  └──────────────┘  └──────────────┘                        │
└─────────────────────────────────────────────────────────────┘
```

### Component Layers

1. **Presentation Layer (Frontend)**
   - React Native screens and components
   - User interface and interactions
   - Navigation and routing

2. **Business Logic Layer (Frontend)**
   - Zustand state management
   - Screen-level logic
   - Data transformation

3. **API Layer (Backend)**
   - FastAPI routers
   - Request/response handling
   - Data validation

4. **Data Layer (Database)**
   - PostgreSQL tables
   - Supabase client
   - Data persistence

---

## Frontend Workflow

### Application Initialization

```
App.tsx
  │
  ├─> GestureHandlerRootView (Gesture support)
  │
  ├─> StatusBar (System status bar)
  │
  └─> AppNavigator (Main navigation container)
```

### Navigation Structure

**Stack Navigator (Root)**
```
Splash Screen
  │
  ├─> Welcome Screen
  │     │
  │     ├─> PhoneAuth Screen
  │     │     │
  │     │     └─> Registration Screen
  │     │           │
  │     │           └─> Onboarding Screen
  │     │                 │
  │     │                 └─> Permissions Screen
  │     │                       │
  │     │                       └─> MainTabs (Bottom Tab Navigator)
  │     │
  │     └─> MainTabs (if returning user)
  │
  └─> MainTabs
        │
        ├─> Emergency Selection Tab
        │     │
        │     ├─> Emergency Detail Screen
        │     │     │
        │     │     └─> Payment Screen
        │     │           │
        │     │           └─> Lawyer Matching Screen
        │     │                 │
        │     │                 └─> Lawyer Profile Screen
        │     │                       │
        │     │                       └─> Call Screen
        │     │                             │
        │     │                             └─> Post Call Summary Screen
        │     │
        │     └─> (Back to Emergency Selection)
        │
        ├─> History Tab
        │     │
        │     └─> Post Call Summary Screen (for past cases)
        │
        └─> Profile Tab
              │
              ├─> Payment Methods Screen
              │
              └─> Billing History Screen
```

### Screen Flow Details

#### 1. Splash Screen
- **Purpose**: Initial app loading and branding
- **Duration**: 2.5 seconds (auto-advance)
- **Actions**: 
  - Display animated logo
  - Initialize app state
  - Navigate to Welcome or MainTabs based on auth state

#### 2. Welcome Screen
- **Purpose**: Authentication entry point
- **Options**: 
  - Phone authentication
  - Apple Sign-In (future)
  - Google Sign-In (future)
- **Navigation**: → PhoneAuth Screen

#### 3. PhoneAuth Screen
- **Purpose**: Phone number verification
- **Flow**:
  1. User enters phone number
  2. OTP sent (currently mocked)
  3. User enters OTP
  4. Verification (currently mocked)
- **Navigation**: → Registration Screen

#### 4. Registration Screen
- **Purpose**: Collect user profile information
- **Data Collected**:
  - Full Name
  - State
- **State Update**: Sets user object in Zustand store
- **Navigation**: → Onboarding Screen

#### 5. Onboarding Screen
- **Purpose**: Educate users about app features
- **Content**: 4 swipeable slides
- **Options**: Skip button available
- **State Update**: Sets `hasCompletedOnboarding: true`
- **Navigation**: → Permissions Screen

#### 6. Permissions Screen
- **Purpose**: Request device permissions
- **Permissions**:
  - Location (Required)
  - Microphone (Required)
  - Notifications (Optional)
- **State Update**: Sets `hasGrantedPermissions: true`
- **Navigation**: → MainTabs

#### 7. Emergency Selection Screen (Tab)
- **Purpose**: Main emergency type selection
- **Emergency Types**:
  - 🚔 Traffic Pullover
  - 🍺 DUI / Sobriety Test
  - 👮 Arrest / Detainment
  - ⚠️ Other
- **Navigation**: → Emergency Detail Screen

#### 8. Emergency Detail Screen
- **Purpose**: Show legal information and initiate consultation
- **Content**:
  - Legal tips
  - User rights information
  - Call-to-action button
- **Navigation**: → Payment Screen

#### 9. Payment Screen
- **Purpose**: Process payment for consultation
- **Payment Methods**:
  - Google Pay
  - Credit/Debit Card
- **Amount**: $49.99
- **Actions**:
  - Process payment (currently mocked)
  - Save payment card (if selected)
  - Create receipt
  - Update billing history
- **Navigation**: → Lawyer Matching Screen

#### 10. Lawyer Matching Screen
- **Purpose**: Match user with available lawyer
- **Features**:
  - Animated matching indicator
  - ETA countdown
  - Status updates
- **Duration**: ~6 seconds (simulated)
- **State Update**: Creates current case
- **Navigation**: → Lawyer Profile Screen

#### 11. Lawyer Profile Screen
- **Purpose**: Display matched lawyer information
- **Information**:
  - Name and photo
  - Experience years
  - Rating
  - Specialties
  - State bar information
- **Navigation**: → Call Screen

#### 12. Call Screen
- **Purpose**: Active call interface
- **Features**:
  - Call duration timer
  - Mute/unmute toggle
  - Speaker toggle
  - Location sharing toggle
  - End call button
- **Restrictions**: No back navigation (gesture disabled)
- **State Update**: Updates current case with duration
- **Navigation**: → Post Call Summary Screen

#### 13. Post Call Summary Screen
- **Purpose**: Display call summary and next steps
- **Information**:
  - Call duration
  - Total cost
  - Lawyer information
  - Recording download (future)
  - Follow-up options
- **State Update**: Adds case to cases array
- **Navigation**: → Back to Emergency Selection

#### 14. History Screen (Tab)
- **Purpose**: Display past consultation cases
- **Data Source**: Zustand store `cases` array
- **Features**:
  - List of past cases
  - Case details on tap
- **Navigation**: → Post Call Summary Screen (for selected case)

#### 15. Profile Screen (Tab)
- **Purpose**: User settings and account management
- **Sections**:
  - Account Settings (Personal info, phone, state)
  - Emergency Settings (Contacts, location, notifications)
  - Payment Settings (Payment methods, billing history)
  - Legal Settings (Terms, privacy, support)
- **Navigation**: → Payment Methods Screen, Billing History Screen

### Frontend State Management (Zustand)

**Store Structure:**
```typescript
interface AppState {
  // User Data
  user: User | null;
  
  // Case Management
  currentCase: EmergencyCase | null;
  cases: EmergencyCase[];
  
  // Onboarding State
  hasCompletedOnboarding: boolean;
  hasGrantedPermissions: boolean;
  
  // Billing
  billingHistory: Receipt[];
  
  // Actions
  setUser: (user: User) => void;
  setCurrentCase: (case_: EmergencyCase | null) => void;
  addCase: (case_: EmergencyCase) => void;
  completeOnboarding: () => void;
  grantPermissions: () => void;
  savePaymentCard: (card: PaymentCard) => void;
  removePaymentCard: (cardId: string) => void;
  addReceipt: (receipt: Receipt) => void;
}
```

**State Flow:**
1. **Initial State**: All values are `null` or `false`
2. **Registration**: `user` object is set
3. **Onboarding**: `hasCompletedOnboarding` set to `true`
4. **Permissions**: `hasGrantedPermissions` set to `true`
5. **Payment**: Receipt added to `billingHistory`
6. **Call Start**: `currentCase` is created
7. **Call End**: `currentCase` is added to `cases` array

**Note**: Currently, state is in-memory only. No persistence layer implemented yet.

---

## Backend Workflow

### Backend Architecture

```
main.py (FastAPI Application)
  │
  ├─> CORS Middleware (Cross-origin requests)
  │
  ├─> Health Router (/api/health)
  │
  ├─> Profile Router (/api/profile)
  │     │
  │     ├─> POST /upsert (Create/Update profile)
  │     ├─> GET /{profile_id} (Get profile by ID)
  │     ├─> GET /by-email (Get profile by email)
  │     └─> GET /by-phone (Get profile by phone)
  │
  └─> Sessions Router (/api)
        │
        ├─> POST /sessions (Create session)
        └─> GET /sessions/by-profile/{profile_id} (List sessions)
```

### Backend Initialization

1. **Application Startup** (`main.py`)
   ```
   Load environment variables
   → Initialize FastAPI app
   → Configure CORS middleware
   → Register routers
   → Start Uvicorn server
   ```

2. **Database Connection** (`app/database.py`)
   ```
   Check Supabase configuration
   → Create Supabase client (service role)
   → Return client instance
   ```

3. **Configuration** (`app/config.py`)
   ```
   Load .env file
   → Parse settings:
     - HOST (default: 0.0.0.0)
     - PORT (default: 8000)
     - ALLOWED_ORIGINS (CORS)
     - SUPABASE_URL
     - SUPABASE_SERVICE_ROLE_KEY
   ```

### Request Processing Flow

```
HTTP Request
  │
  ├─> CORS Middleware (Validate origin)
  │
  ├─> Router Matching (Route to appropriate handler)
  │
  ├─> Request Validation (Pydantic schemas)
  │
  ├─> Database Operation (Supabase client)
  │
  ├─> Response Serialization (Pydantic models)
  │
  └─> HTTP Response
```

### API Endpoint Details

#### Health Check
- **Endpoint**: `GET /api/health`
- **Purpose**: Service health monitoring
- **Response**: `{"status": "ok"}`
- **No database access**

#### Profile Management

**Create/Update Profile**
- **Endpoint**: `POST /api/profile/upsert`
- **Request Body**: `ProfileUpsertRequest`
  ```json
  {
    "email": "string (optional)",
    "phone": "string (optional)",
    "full_name": "string (optional)",
    "address": "string (optional)",
    "state": "string (optional)"
  }
  ```
- **Logic Flow**:
  1. Validate request (at least email or phone required)
  2. Sanitize empty strings
  3. Check for existing profile by email
  4. If not found, check by phone
  5. If exists: Update profile
  6. If not exists: Create new profile
  7. Return `ProfileResponse`
- **Database**: `profiles` table

**Get Profile by ID**
- **Endpoint**: `GET /api/profile/{profile_id}`
- **Logic Flow**:
  1. Query `profiles` table by ID
  2. If not found: 404 error
  3. Return `ProfileResponse`

**Get Profile by Email**
- **Endpoint**: `GET /api/profile/by-email?email={email}`
- **Logic Flow**:
  1. Query `profiles` table by email
  2. If not found: 404 error
  3. Return `ProfileResponse`

**Get Profile by Phone**
- **Endpoint**: `GET /api/profile/by-phone?phone={phone}`
- **Logic Flow**:
  1. Query `profiles` table by phone
  2. If not found: 404 error
  3. Return `ProfileResponse`

#### Session Management

**Create Session**
- **Endpoint**: `POST /api/sessions`
- **Request Body**: `SessionCreateRequest`
  ```json
  {
    "profile_id": "uuid (required)",
    "emergency_type": "string (optional)",
    "amount_cents": "integer (default: 0)",
    "currency": "string (default: 'usd')",
    "matched_attorney_name": "string (optional)",
    "matched_attorney_id": "string (optional)",
    "matched_at": "ISO string (optional)",
    "notes": "string (optional)",
    "status": "string (default: 'created')"
  }
  ```
- **Logic Flow**:
  1. Validate request (profile_id required)
  2. Insert into `sessions` table
  3. Return `SessionResponse` with generated ID and timestamps
- **Database**: `sessions` table

**List Sessions by Profile**
- **Endpoint**: `GET /api/sessions/by-profile/{profile_id}`
- **Logic Flow**:
  1. Query `sessions` table by profile_id
  2. Order by `created_at` descending
  3. Return array of `SessionResponse`
- **Database**: `sessions` table

### Error Handling

**HTTP Exceptions:**
- `400 Bad Request`: Invalid input data
- `404 Not Found`: Resource not found
- `500 Internal Server Error`: Database or server errors

**Error Response Format:**
```json
{
  "detail": "Error message"
}
```

### Data Validation

**Pydantic Models:**
- `ProfileUpsertRequest`: Profile creation/update
- `ProfileResponse`: Profile data response
- `SessionCreateRequest`: Session creation
- `SessionResponse`: Session data response

**Validation Rules:**
- All models use `extra="forbid"` (no extra fields)
- Field types and constraints enforced
- Optional fields handled appropriately

---

## Database Workflow

### Database Schema

#### Profiles Table

**Structure:**
```sql
CREATE TABLE profiles (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  full_name TEXT,
  email TEXT UNIQUE,
  phone TEXT UNIQUE,
  address TEXT,
  state TEXT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Indexes:**
- Primary key on `id`
- Unique constraint on `email`
- Unique constraint on `phone`

**Triggers:**
- `trg_profiles_updated_at`: Auto-updates `updated_at` on row update

**Workflow:**
1. **Profile Creation**:
   - Generate UUID for `id`
   - Set `created_at` and `updated_at` to current timestamp
   - Store user information

2. **Profile Update**:
   - Update provided fields
   - Trigger automatically updates `updated_at`
   - Preserve `created_at`

3. **Profile Lookup**:
   - Query by `id` (primary key)
   - Query by `email` (unique index)
   - Query by `phone` (unique index)

#### Sessions Table

**Structure:**
```sql
CREATE TABLE sessions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  profile_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  emergency_type TEXT,
  amount_cents INT NOT NULL DEFAULT 0,
  currency TEXT NOT NULL DEFAULT 'usd',
  matched_attorney_name TEXT,
  matched_attorney_id TEXT,
  matched_at TIMESTAMPTZ,
  notes TEXT,
  status TEXT NOT NULL DEFAULT 'created',
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Indexes:**
- Primary key on `id`
- Foreign key index on `profile_id`
- Index on `created_at` (descending) for history queries

**Triggers:**
- `trg_sessions_updated_at`: Auto-updates `updated_at` on row update

**Foreign Key:**
- `profile_id` references `profiles(id)`
- `ON DELETE CASCADE`: Deleting a profile deletes all sessions

**Workflow:**
1. **Session Creation**:
   - Generate UUID for `id`
   - Link to `profile_id` (must exist)
   - Set `created_at` and `updated_at` to current timestamp
   - Store session information

2. **Session Update**:
   - Update status, notes, or other fields
   - Trigger automatically updates `updated_at`
   - Preserve `created_at`

3. **Session Retrieval**:
   - Query by `profile_id` (for user history)
   - Order by `created_at` descending
   - Filter by status if needed

### Database Operations

#### Connection Management
- **Client**: Supabase Python client
- **Authentication**: Service role key (backend-only)
- **Connection**: Singleton pattern (reused connection)

#### Query Patterns

**Insert:**
```python
supabase.table("table_name").insert(data).execute()
```

**Select:**
```python
supabase.table("table_name")
  .select("*")
  .eq("field", value)
  .order("created_at", desc=True)
  .execute()
```

**Update:**
```python
supabase.table("table_name")
  .update(data)
  .eq("id", id)
  .execute()
```

**Upsert Logic:**
1. Try to find existing record
2. If found: Update
3. If not found: Insert

### Data Relationships

```
profiles (1) ──────< (many) sessions
   │
   └─> One profile can have many sessions
       Sessions are deleted when profile is deleted (CASCADE)
```

---

## Data Flow Diagrams

### Complete User Registration Flow

```
┌─────────────┐
│   Frontend  │
│ Registration│
│   Screen    │
└──────┬──────┘
       │
       │ 1. User enters name, state
       │
       ▼
┌─────────────┐
│  Zustand    │
│   Store     │
└──────┬──────┘
       │
       │ 2. setUser({ id, name, phone, state })
       │
       ▼
┌─────────────┐
│   Frontend  │
│  (Mocked)   │
│  API Call   │
└──────┬──────┘
       │
       │ 3. POST /api/profile/upsert
       │    { email, phone, full_name, state }
       │
       ▼
┌─────────────┐
│   Backend   │
│   FastAPI   │
│   Router    │
└──────┬──────┘
       │
       │ 4. Validate request
       │ 5. Check existing profile
       │
       ▼
┌─────────────┐
│  Supabase   │
│   Client    │
└──────┬──────┘
       │
       │ 6. INSERT or UPDATE profiles table
       │
       ▼
┌─────────────┐
│ PostgreSQL  │
│  Database   │
└──────┬──────┘
       │
       │ 7. Return profile data
       │
       ▼
┌─────────────┐
│   Backend   │
│  Response   │
└──────┬──────┘
       │
       │ 8. ProfileResponse JSON
       │
       ▼
┌─────────────┐
│   Frontend  │
│  (Future)   │
│  Update UI  │
└─────────────┘
```

### Emergency Consultation Flow

```
┌─────────────┐
│   User      │
│  Selects    │
│  Emergency  │
└──────┬──────┘
       │
       ▼
┌─────────────┐      ┌─────────────┐
│  Emergency  │─────>│  Payment    │
│   Detail    │      │   Screen    │
└─────────────┘      └──────┬──────┘
                            │
                            │ Process Payment
                            │ (Mocked)
                            │
                            ▼
                    ┌─────────────┐
                    │  Zustand    │
                    │   Store     │
                    └──────┬──────┘
                           │
                           │ addReceipt()
                           │
                           ▼
                    ┌─────────────┐
                    │  Matching   │
                    │   Screen    │
                    └──────┬──────┘
                           │
                           │ Match Lawyer
                           │ (Mocked)
                           │
                           ▼
                    ┌─────────────┐
                    │  Call       │
                    │   Screen    │
                    └──────┬──────┘
                           │
                           │ End Call
                           │
                           ▼
                    ┌─────────────┐
                    │  Zustand    │
                    │   Store     │
                    └──────┬──────┘
                           │
                           │ addCase()
                           │
                           ▼
                    ┌─────────────┐      ┌─────────────┐
                    │   Summary   │─────>│   Backend   │
                    │   Screen    │      │   API       │
                    └─────────────┘      └──────┬──────┘
                                                │
                                                │ POST /api/sessions
                                                │
                                                ▼
                                         ┌─────────────┐
                                         │  Supabase   │
                                         │  Database   │
                                         └─────────────┘
```

### Session Creation Flow

```
┌─────────────┐
│   Frontend  │
│  Post-Call  │
│   Summary   │
└──────┬──────┘
       │
       │ 1. Collect case data
       │    - profile_id
       │    - emergency_type
       │    - amount_cents
       │    - matched_attorney_name
       │    - duration, etc.
       │
       ▼
┌─────────────┐
│  HTTP POST  │
│ /api/sessions│
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Backend   │
│  Validation │
│  (Pydantic) │
└──────┬──────┘
       │
       │ 2. Validate SessionCreateRequest
       │
       ▼
┌─────────────┐
│  Supabase   │
│   Client    │
└──────┬──────┘
       │
       │ 3. INSERT INTO sessions
       │    - Generate UUID
       │    - Set timestamps
       │    - Store all fields
       │
       ▼
┌─────────────┐
│ PostgreSQL  │
│  Database   │
└──────┬──────┘
       │
       │ 4. Return inserted row
       │
       ▼
┌─────────────┐
│   Backend   │
│  Serialize  │
│  Response   │
└──────┬──────┘
       │
       │ 5. SessionResponse JSON
       │
       ▼
┌─────────────┐
│   Frontend  │
│  Update UI  │
│  (Future)   │
└─────────────┘
```

---

## API Endpoints Reference

### Base URL
- **Development**: `http://localhost:8000` or `http://<LAN_IP>:8000`
- **Production**: Configure via environment variables

### Health Check

**GET /api/health**
- **Description**: Check backend service health
- **Authentication**: None required
- **Response**:
  ```json
  {
    "status": "ok"
  }
  ```

### Profile Endpoints

**POST /api/profile/upsert**
- **Description**: Create or update user profile
- **Authentication**: None (to be implemented)
- **Request Body**:
  ```json
  {
    "email": "user@example.com",
    "phone": "+1234567890",
    "full_name": "John Doe",
    "address": "123 Main St",
    "state": "California"
  }
  ```
- **Response**: `ProfileResponse`
  ```json
  {
    "id": "uuid",
    "email": "user@example.com",
    "phone": "+1234567890",
    "full_name": "John Doe",
    "address": "123 Main St",
    "state": "California"
  }
  ```
- **Status Codes**:
  - `200`: Success
  - `400`: Missing email/phone
  - `500`: Server error

**GET /api/profile/{profile_id}**
- **Description**: Get profile by ID
- **Parameters**: `profile_id` (UUID)
- **Response**: `ProfileResponse`
- **Status Codes**:
  - `200`: Success
  - `404`: Profile not found

**GET /api/profile/by-email?email={email}**
- **Description**: Get profile by email
- **Query Parameters**: `email` (string)
- **Response**: `ProfileResponse`
- **Status Codes**:
  - `200`: Success
  - `404`: Profile not found

**GET /api/profile/by-phone?phone={phone}**
- **Description**: Get profile by phone
- **Query Parameters**: `phone` (string)
- **Response**: `ProfileResponse`
- **Status Codes**:
  - `200`: Success
  - `404`: Profile not found

### Session Endpoints

**POST /api/sessions**
- **Description**: Create a new session/history record
- **Request Body**: `SessionCreateRequest`
  ```json
  {
    "profile_id": "uuid",
    "emergency_type": "traffic-pullover",
    "amount_cents": 4999,
    "currency": "usd",
    "matched_attorney_name": "Sarah Mitchell",
    "matched_attorney_id": "lawyer-123",
    "matched_at": "2024-01-15T10:30:00Z",
    "notes": "30-minute consultation",
    "status": "completed"
  }
  ```
- **Response**: `SessionResponse`
  ```json
  {
    "id": "uuid",
    "profile_id": "uuid",
    "emergency_type": "traffic-pullover",
    "amount_cents": 4999,
    "currency": "usd",
    "matched_attorney_name": "Sarah Mitchell",
    "matched_attorney_id": "lawyer-123",
    "matched_at": "2024-01-15T10:30:00Z",
    "notes": "30-minute consultation",
    "status": "completed",
    "created_at": "2024-01-15T10:30:00Z"
  }
  ```
- **Status Codes**:
  - `200`: Success
  - `400`: Invalid request
  - `500`: Server error

**GET /api/sessions/by-profile/{profile_id}**
- **Description**: Get all sessions for a profile
- **Parameters**: `profile_id` (UUID)
- **Response**: Array of `SessionResponse`
- **Ordering**: By `created_at` descending (newest first)
- **Status Codes**:
  - `200`: Success
  - `404`: Profile not found (if profile doesn't exist)

---

## State Management

### Zustand Store Structure

**Location**: `src/store/useAppStore.ts`

**State Properties:**

1. **user: User | null**
   - Current authenticated user
   - Set during registration
   - Contains: id, name, phone, state, emergencyContacts, savedCards

2. **currentCase: EmergencyCase | null**
   - Active emergency case
   - Created when call starts
   - Contains: id, type, timestamp, lawyer, duration, cost

3. **cases: EmergencyCase[]**
   - History of all cases
   - Added after call completion
   - Used in History screen

4. **hasCompletedOnboarding: boolean**
   - Onboarding completion flag
   - Set to true after onboarding screen

5. **hasGrantedPermissions: boolean**
   - Permissions granted flag
   - Set to true after permissions screen

6. **billingHistory: Receipt[]**
   - Payment receipts
   - Added after payment processing
   - Used in Billing History screen

**Actions:**

- `setUser(user: User)`: Set current user
- `setCurrentCase(case_: EmergencyCase | null)`: Set active case
- `addCase(case_: EmergencyCase)`: Add case to history
- `completeOnboarding()`: Mark onboarding complete
- `grantPermissions()`: Mark permissions granted
- `savePaymentCard(card: PaymentCard)`: Add payment card
- `removePaymentCard(cardId: string)`: Remove payment card
- `addReceipt(receipt: Receipt)`: Add payment receipt

### State Persistence

**Current Status**: ❌ Not implemented
- State is in-memory only
- Data lost on app restart
- No offline persistence

**Future Implementation**:
- Use `@react-native-async-storage/async-storage`
- Persist critical state (user, cases)
- Sync with backend on app start

---

## Authentication Flow

### Current Implementation

**Status**: ⚠️ Partially implemented (mocked)

**Flow:**
```
Welcome Screen
  │
  ├─> Phone Authentication
  │     │
  │     ├─> Enter Phone Number
  │     │
  │     ├─> Send OTP (Mocked)
  │     │
  │     ├─> Enter OTP
  │     │
  │     └─> Verify OTP (Mocked)
  │
  └─> Registration Screen
        │
        └─> Set User in Store
```

### Future Implementation

**Planned Features:**
1. **Supabase Auth Integration**
   - Phone OTP via Supabase
   - Email/password authentication
   - Social login (Google, Apple)

2. **Session Management**
   - JWT tokens
   - Refresh tokens
   - Token expiration handling

3. **Backend Authentication**
   - Protected endpoints
   - User context in requests
   - Role-based access control

---

## Emergency Consultation Flow

### Complete Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    EMERGENCY CONSULTATION                    │
└─────────────────────────────────────────────────────────────┘

1. Emergency Selection
   │
   ├─> User selects emergency type
   │     - Traffic Pullover
   │     - DUI / Sobriety
   │     - Arrest
   │     - Other
   │
   ▼

2. Emergency Detail
   │
   ├─> Display legal information
   ├─> Show user rights
   └─> "Get Help Now" button
   │
   ▼

3. Payment Screen
   │
   ├─> Select payment method
   │     - Google Pay
   │     - Credit Card
   ├─> Enter card details (if card)
   ├─> Process payment (Mocked)
   ├─> Save card (optional)
   └─> Create receipt
   │
   ▼

4. Lawyer Matching
   │
   ├─> Show matching animation
   ├─> Display ETA countdown
   ├─> Match lawyer (Mocked)
   └─> Create currentCase
   │
   ▼

5. Lawyer Profile
   │
   ├─> Display lawyer information
   │     - Name, photo
   │     - Experience, rating
   │     - Specialties
   └─> "Start Call" button
   │
   ▼

6. Call Screen
   │
   ├─> Initialize call (Mocked)
   ├─> Start duration timer
   ├─> Call controls
   │     - Mute/unmute
   │     - Speaker toggle
   │     - Location sharing
   │     - End call
   └─> Update currentCase duration
   │
   ▼

7. Post-Call Summary
   │
   ├─> Display call summary
   │     - Duration
   │     - Cost
   │     - Lawyer info
   ├─> Add case to history
   ├─> Create session in backend (Future)
   └─> Follow-up options
   │
   ▼

8. Return to Emergency Selection
```

### Data Flow in Emergency Flow

1. **Emergency Selection** → No data change
2. **Emergency Detail** → No data change
3. **Payment** → `addReceipt()` → `billingHistory` updated
4. **Matching** → `setCurrentCase()` → `currentCase` created
5. **Call** → `currentCase.duration` updated
6. **Summary** → `addCase()` → Case added to `cases` array

---

## Payment Processing Flow

### Current Implementation

**Status**: ⚠️ Mocked (no real payment processing)

**Flow:**
```
Payment Screen
  │
  ├─> User selects payment method
  │     - Google Pay
  │     - Credit Card
  │
  ├─> If Credit Card:
  │     - Enter card number
  │     - Enter cardholder name
  │     - Enter expiry date
  │     - Enter CVV
  │     - Option to save card
  │
  ├─> Process Payment
  │     - Simulate 2-second delay
  │     - Always succeeds (mocked)
  │
  ├─> Create Receipt
  │     - Generate transaction ID
  │     - Set amount ($49.99)
  │     - Set service details
  │
  ├─> Save Card (if selected)
  │     - Add to user.savedCards
  │
  └─> Navigate to Matching
```

### Receipt Structure

```typescript
interface Receipt {
  id: string;                    // Generated timestamp
  date: Date;                    // Current date
  caseId: string;                // Associated case ID
  amount: number;                // $49.99
  serviceName: string;            // "Emergency Legal Consultation"
  duration: number;              // 30 minutes
  lawyerName: string;            // "TBD" initially
  paymentMethod: string;         // "Google Pay" or "Card ending in XXXX"
  transactionId: string;         // "TXN{timestamp}"
  items: ReceiptItem[];          // Line items
}
```

### Future Implementation

**Planned Features:**
1. **Stripe Integration**
   - Real payment processing
   - Payment method tokens
   - Payment verification

2. **Backend Payment Endpoint**
   - Secure payment processing
   - Webhook handling
   - Payment status tracking

3. **Payment History Sync**
   - Store receipts in database
   - Sync with backend
   - Retrieve payment history

---

## Session Management Flow

### Session Lifecycle

```
Session Creation
  │
  ├─> User completes call
  │
  ├─> Frontend collects data:
  │     - profile_id
  │     - emergency_type
  │     - amount_cents
  │     - matched_attorney_name
  │     - matched_attorney_id
  │     - duration
  │     - notes
  │
  ├─> POST /api/sessions
  │     - Validate request
  │     - Insert into database
  │     - Return session data
  │
  └─> Session stored in database
```

### Session Data Structure

**Database Schema:**
- `id`: UUID (auto-generated)
- `profile_id`: UUID (foreign key)
- `emergency_type`: TEXT (optional)
- `amount_cents`: INTEGER (default: 0)
- `currency`: TEXT (default: 'usd')
- `matched_attorney_name`: TEXT (optional)
- `matched_attorney_id`: TEXT (optional)
- `matched_at`: TIMESTAMPTZ (optional)
- `notes`: TEXT (optional)
- `status`: TEXT (default: 'created')
- `created_at`: TIMESTAMPTZ (auto-generated)
- `updated_at`: TIMESTAMPTZ (auto-updated)

**Status Values:**
- `created`: Session created
- `matched`: Lawyer matched
- `in_progress`: Call active
- `completed`: Call finished
- `cancelled`: Session cancelled

### Session Retrieval

**Get User History:**
```
GET /api/sessions/by-profile/{profile_id}
  │
  ├─> Query sessions table
  │     - Filter by profile_id
  │     - Order by created_at DESC
  │
  └─> Return array of sessions
```

**Use Cases:**
- History screen display
- Billing history
- Case management
- Analytics

---

## Integration Points

### Frontend ↔ Backend

**Current Integration:**
- ❌ Not fully implemented
- Frontend uses mocked data
- API calls are simulated

**Planned Integration:**
1. **API Service Layer**
   - Create `src/services/api/` directory
   - Implement API client
   - Handle authentication
   - Error handling

2. **Profile Sync**
   - Sync user profile on registration
   - Update profile on changes
   - Retrieve profile on app start

3. **Session Sync**
   - Create session after call
   - Retrieve session history
   - Update session status

### Backend ↔ Database

**Current Integration:**
- ✅ Fully implemented
- Supabase client configured
- All CRUD operations working

**Connection:**
- Service role key authentication
- Singleton client pattern
- Error handling implemented

### Frontend ↔ Database

**Current Integration:**
- ❌ No direct connection
- All database access through backend

**Future Options:**
- Direct Supabase client (with RLS)
- Real-time subscriptions
- Offline-first with sync

---

## Data Synchronization

### Current State

**Frontend State:**
- In-memory only (Zustand)
- No persistence
- No backend sync

**Backend State:**
- Persistent (PostgreSQL)
- All data stored in database
- No frontend sync

### Synchronization Gaps

1. **User Profile**
   - Frontend: Stored in Zustand
   - Backend: Stored in database
   - ❌ Not synchronized

2. **Cases/Sessions**
   - Frontend: Stored in Zustand `cases` array
   - Backend: Stored in `sessions` table
   - ❌ Not synchronized

3. **Billing History**
   - Frontend: Stored in Zustand `billingHistory`
   - Backend: Not stored (future)
   - ❌ Not synchronized

### Future Synchronization Strategy

1. **On App Start**
   - Retrieve user profile from backend
   - Retrieve session history from backend
   - Populate Zustand store

2. **After Actions**
   - Create/update profile → Sync to backend
   - Complete call → Create session in backend
   - Process payment → Store receipt in backend

3. **Periodic Sync**
   - Background sync every 5 minutes
   - Pull-to-refresh on History screen
   - Conflict resolution

---

## Error Handling

### Frontend Error Handling

**Current Status**: ⚠️ Basic error handling

**Areas:**
- Form validation
- Navigation errors
- State errors

**Future Improvements:**
- Error boundaries
- Retry mechanisms
- User-friendly error messages
- Error logging

### Backend Error Handling

**Current Status**: ✅ Implemented

**Error Types:**
- `400 Bad Request`: Invalid input
- `404 Not Found`: Resource not found
- `500 Internal Server Error`: Server errors

**Error Response:**
```json
{
  "detail": "Error message"
}
```

**Logging:**
- All errors logged
- Exception details captured
- Request context included

### Database Error Handling

**Current Status**: ✅ Handled

**Error Scenarios:**
- Connection failures
- Query errors
- Constraint violations
- Foreign key violations

**Handling:**
- Try-catch blocks
- HTTP exception conversion
- User-friendly messages

---

## Security Considerations

### Current Security Status

**Frontend:**
- ⚠️ No authentication tokens
- ⚠️ No encrypted storage
- ⚠️ No certificate pinning

**Backend:**
- ✅ Service role key in environment
- ✅ CORS configuration
- ⚠️ No rate limiting
- ⚠️ No request validation (basic Pydantic only)

**Database:**
- ✅ Supabase RLS (can be configured)
- ✅ Foreign key constraints
- ⚠️ No encryption at rest (Supabase handles)

### Security Recommendations

1. **Authentication**
   - Implement JWT tokens
   - Token refresh mechanism
   - Secure token storage

2. **API Security**
   - Rate limiting
   - Request signing
   - Input sanitization

3. **Data Protection**
   - Encrypt sensitive data
   - PII masking in logs
   - Secure communication (HTTPS)

---

## Performance Considerations

### Frontend Performance

**Current Optimizations:**
- React Native performance
- Efficient navigation
- Minimal re-renders

**Future Optimizations:**
- React.memo for components
- Virtual lists for long lists
- Lazy loading screens
- Image optimization

### Backend Performance

**Current Optimizations:**
- FastAPI async support
- Database indexes
- Efficient queries

**Future Optimizations:**
- Response caching
- Database connection pooling
- Query optimization
- Background tasks

### Database Performance

**Current Optimizations:**
- Indexes on foreign keys
- Index on created_at
- Efficient query patterns

**Future Optimizations:**
- Additional indexes as needed
- Query analysis
- Connection pooling

---

## Testing Strategy

### Current Testing Status

**Frontend:**
- ❌ No unit tests
- ❌ No integration tests
- ❌ No E2E tests

**Backend:**
- ❌ No unit tests
- ❌ No integration tests
- ❌ No API tests

### Recommended Testing

1. **Frontend Tests**
   - Component unit tests
   - Store/state tests
   - Navigation tests
   - E2E critical flows

2. **Backend Tests**
   - API endpoint tests
   - Database operation tests
   - Validation tests
   - Error handling tests

3. **Integration Tests**
   - Frontend-Backend integration
   - Database integration
   - End-to-end flows

---

## Deployment Considerations

### Frontend Deployment

**Platforms:**
- iOS (App Store)
- Android (Google Play)
- Expo managed workflow

**Requirements:**
- Environment variables
- API endpoint configuration
- Build configuration

### Backend Deployment

**Options:**
- Cloud platforms (AWS, GCP, Azure)
- Container deployment (Docker)
- Serverless (AWS Lambda)

**Requirements:**
- Environment variables
- Database connection
- CORS configuration
- SSL/TLS certificates

### Database Deployment

**Current:**
- Supabase managed PostgreSQL

**Considerations:**
- Backup strategy
- Migration management
- Performance monitoring

---

## Future Enhancements

### Planned Features

1. **Real Payment Processing**
   - Stripe integration
   - Payment verification
   - Receipt generation

2. **Real Call Functionality**
   - Twilio Voice SDK
   - Call recording
   - Location sharing

3. **State Persistence**
   - AsyncStorage integration
   - Offline support
   - Background sync

4. **Push Notifications**
   - Lawyer match notifications
   - Call reminders
   - Payment confirmations

5. **Enhanced Features**
   - In-app messaging
   - Document sharing
   - Search/filter history
   - Analytics dashboard

---

## Conclusion

This documentation provides a comprehensive overview of the LegalGuard application workflow, covering:

- **Frontend**: React Native app structure, navigation, screens, and state management
- **Backend**: FastAPI server, endpoints, and request processing
- **Database**: PostgreSQL schema, relationships, and operations
- **Integration**: How components interact and data flows through the system

The application currently has a solid foundation with:
- ✅ Complete UI/UX flow
- ✅ Backend API structure
- ✅ Database schema
- ⚠️ Partial integration (mocked frontend-backend)
- ⚠️ Missing production features (real payments, calls)

**Next Steps:**
1. Implement frontend-backend integration
2. Add state persistence
3. Integrate real payment processing
4. Add real call functionality
5. Implement authentication
6. Add error handling and retry logic
7. Set up testing infrastructure

---

**Document Version**: 1.0  
**Last Updated**: 2024  
**Maintained By**: Development Team

