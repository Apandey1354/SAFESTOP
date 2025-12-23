# Safestop - Complete Workflow Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [Complete User Journey](#complete-user-journey)
3. [Step-by-Step Workflow](#step-by-step-workflow)
4. [Architecture Overview](#architecture-overview)
5. [Backend Workflow](#backend-workflow)
6. [Database Workflow](#database-workflow)
7. [API Endpoints Reference](#api-endpoints-reference)
8. [State Management](#state-management)
9. [Integration Points](#integration-points)

---

## Project Overview

**Safestop** is a React Native mobile application that connects users with legal professionals during emergency situations. The application provides on-demand legal consultation services through a streamlined mobile interface.

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
│                    FastAPI Backend (Safestop)                 │
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

## Complete User Journey

This section follows the user's journey from the moment they open the app to completing an emergency consultation.

### High-Level Flow

```
App Launch
  │
  ├─> Splash Screen (2.5 seconds)
  │
  ├─> Welcome Screen
  │
  ├─> Phone Authentication
  │
  ├─> Registration
  │
  ├─> Onboarding
  │
  ├─> Permissions
  │
  ├─> Main App (Tabs)
  │     │
  │     ├─> Emergency Selection
  │     │     │
  │     │     └─> Emergency Consultation Flow
  │     │           │
  │     │           ├─> Emergency Detail
  │     │           ├─> Payment
  │     │           ├─> Lawyer Matching
  │     │           ├─> Lawyer Profile
  │     │           ├─> Call
  │     │           └─> Post-Call Summary
  │     │
  │     ├─> History (View past cases)
  │     │
  │     └─> Profile (Settings & Account)
```

---

## Step-by-Step Workflow

### Step 1: App Launch & Splash Screen

**What Happens:**
1. User opens the Safestop app
2. App initializes (`App.tsx` loads)
3. Splash Screen displays with animated logo
4. App checks authentication state (currently mocked)
5. After 2.5 seconds, automatically navigates to next screen

**Technical Details:**
- **Screen**: `SplashScreen.tsx`
- **Duration**: 2.5 seconds (auto-advance)
- **State**: No state changes
- **Navigation**: → Welcome Screen (or MainTabs if returning user)

**Backend/Database**: No API calls at this stage

---

### Step 2: Welcome Screen

**What Happens:**
1. User sees welcome screen with authentication options
2. User can choose:
   - Phone authentication (primary)
   - Apple Sign-In (future)
   - Google Sign-In (future)
3. User taps "Continue with Phone"

**Technical Details:**
- **Screen**: `WelcomeScreen.tsx`
- **User Action**: Select authentication method
- **State**: No state changes yet
- **Navigation**: → PhoneAuth Screen

**Backend/Database**: No API calls at this stage

---

### Step 3: Phone Authentication

**What Happens:**
1. User enters phone number
2. User taps "Send Code"
3. OTP code is sent (currently mocked - no real SMS)
4. User enters 6-digit OTP code
5. Code is verified (currently mocked - always succeeds)
6. User is authenticated

**Technical Details:**
- **Screen**: `PhoneAuthScreen.tsx`
- **User Input**: Phone number, OTP code
- **State**: No state changes (authentication mocked)
- **Navigation**: → Registration Screen

**Backend/Database**: 
- ⚠️ Currently mocked
- Future: Will call Supabase Auth API for OTP

---

### Step 4: Registration

**What Happens:**
1. User enters personal information:
   - Full Name (required)
   - State (required)
2. User taps "Continue"
3. User data is saved to app state
4. Profile is created (currently only in frontend state)

**Technical Details:**
- **Screen**: `RegistrationScreen.tsx`
- **User Input**: Full name, state
- **State Update**: 
  ```typescript
  setUser({
    id: '1',
    name: enteredName,
    phone: '+1 (555) 000-0000', // From phone auth
    state: enteredState,
    emergencyContacts: [],
    savedCards: []
  })
  ```
- **Navigation**: → Onboarding Screen

**Backend/Database**: 
- ⚠️ Currently mocked (simulated API call)
- Future: Will call `POST /api/profile/upsert` to save profile

**Data Flow:**
```
User Input → Zustand Store (setUser) → [Future: Backend API] → Database
```

---

### Step 5: Onboarding

**What Happens:**
1. User sees 4 informational slides about the app:
   - Slide 1: App introduction
   - Slide 2: How it works
   - Slide 3: Features
   - Slide 4: Get started
2. User can swipe through slides or tap "Skip"
3. User taps "Get Started" or "Skip"
4. Onboarding completion is recorded

**Technical Details:**
- **Screen**: `OnboardingScreen.tsx`
- **User Action**: Swipe slides or skip
- **State Update**: 
  ```typescript
  completeOnboarding() // Sets hasCompletedOnboarding: true
  ```
- **Navigation**: → Permissions Screen

**Backend/Database**: No API calls

---

### Step 6: Permissions

**What Happens:**
1. App requests device permissions:
   - **Location** (Required) - To share with attorney during emergency
   - **Microphone** (Required) - For voice calls
   - **Notifications** (Optional) - For updates
2. User grants or denies permissions
3. User taps "Continue"
4. Permissions status is saved

**Technical Details:**
- **Screen**: `PermissionsScreen.tsx`
- **User Action**: Grant/deny permissions
- **State Update**: 
  ```typescript
  grantPermissions() // Sets hasGrantedPermissions: true
  ```
- **Navigation**: → MainTabs (main app)

**Backend/Database**: No API calls

---

### Step 7: Main App - Emergency Selection Tab

**What Happens:**
1. User lands on the Emergency Selection screen (default tab)
2. User sees 4 emergency type options:
   - 🚔 **Traffic Pullover** - Pulled over by police
   - 🍺 **DUI / Sobriety Test** - DUI checkpoint or sobriety test
   - 👮 **Arrest / Detainment** - Being arrested or detained
   - ⚠️ **Other** - Other legal emergency
3. User taps on an emergency type

**Technical Details:**
- **Screen**: `EmergencySelectionScreen.tsx` (Tab 1)
- **User Action**: Select emergency type
- **State**: No state changes
- **Navigation**: → Emergency Detail Screen

**Backend/Database**: No API calls

---

### Step 8: Emergency Detail Screen

**What Happens:**
1. User sees detailed information about their selected emergency type
2. Content includes:
   - Legal tips specific to the emergency
   - User rights information
   - What to do/not do
3. User taps "Get Help Now" button

**Technical Details:**
- **Screen**: `EmergencyDetailScreen.tsx`
- **User Action**: Tap "Get Help Now"
- **State**: No state changes
- **Navigation**: → Payment Screen

**Backend/Database**: No API calls

---

### Step 9: Payment Screen

**What Happens:**
1. User sees payment screen with:
   - Service: Emergency Legal Consultation
   - Amount: $49.99
   - Payment method options
2. User selects payment method:
   - **Google Pay** (quick option)
   - **Credit/Debit Card** (requires card details)
3. If card selected:
   - User enters card number
   - User enters cardholder name
   - User enters expiry date
   - User enters CVV
   - User can choose to save card for future
4. User taps "Pay $49.99"
5. Payment is processed (currently mocked - always succeeds)
6. Receipt is created and saved
7. If "Save Card" was checked, card is saved to user profile

**Technical Details:**
- **Screen**: `PaymentScreen.tsx`
- **User Input**: Payment method, card details (if applicable)
- **State Updates**: 
  ```typescript
  // Create receipt
  addReceipt({
    id: timestamp,
    date: new Date(),
    caseId: timestamp,
    amount: 49.99,
    serviceName: 'Emergency Legal Consultation',
    duration: 30,
    lawyerName: 'TBD',
    paymentMethod: selectedMethod,
    transactionId: `TXN${timestamp}`,
    items: [...]
  })
  
  // Save card if selected
  if (saveCard) {
    savePaymentCard({
      id: timestamp,
      cardNumber: last4Digits,
      cardHolderName: name,
      expiryDate: expiry,
      cardType: 'visa',
      isDefault: isFirstCard
    })
  }
  ```
- **Navigation**: → Lawyer Matching Screen

**Backend/Database**: 
- ⚠️ Payment processing mocked
- Future: Will integrate Stripe for real payments

---

### Step 10: Lawyer Matching Screen

**What Happens:**
1. User sees animated matching screen
2. Display shows:
   - Pulsing animation
   - "Finding the right attorney..." message
   - ETA countdown (e.g., "Connecting in 5... 4... 3...")
   - Status updates
3. After ~6 seconds, lawyer is matched
4. Current case is created in app state

**Technical Details:**
- **Screen**: `LawyerMatchingScreen.tsx`
- **Duration**: ~6 seconds (simulated)
- **State Update**: 
  ```typescript
  setCurrentCase({
    id: timestamp,
    type: emergencyType,
    timestamp: new Date(),
    lawyer: matchedLawyer, // From mockLawyers array
    duration: 0, // Will be updated during call
    cost: 49.99
  })
  ```
- **Navigation**: → Lawyer Profile Screen (auto-advance)

**Backend/Database**: 
- ⚠️ Lawyer matching mocked (uses `mockLawyers` array)
- Future: Will call backend API to match with real lawyers

---

### Step 11: Lawyer Profile Screen

**What Happens:**
1. User sees matched lawyer's profile:
   - Name and photo
   - Years of experience
   - Rating (e.g., ⭐ 4.9)
   - Specialties (e.g., "Traffic Law", "DUI Defense")
   - State bar information
2. User reviews lawyer information
3. User taps "Start Call" button

**Technical Details:**
- **Screen**: `LawyerProfileScreen.tsx`
- **User Action**: Tap "Start Call"
- **State**: No state changes (lawyer already in currentCase)
- **Navigation**: → Call Screen

**Backend/Database**: No API calls

---

### Step 12: Call Screen

**What Happens:**
1. Call interface appears
2. Display shows:
   - "LIVE CALL" status badge
   - "🔒 Encrypted" badge
   - Lawyer's photo and name
   - Call duration timer (starts counting)
3. User can interact with call controls:
   - **Mute/Unmute** - Toggle microphone
   - **Speaker** - Toggle speakerphone
   - **Location Sharing** - Share location with attorney
   - **End Call** - Large red button to end call
4. User ends call by tapping "End Call"
5. Call duration is recorded
6. Current case is updated with duration

**Technical Details:**
- **Screen**: `CallScreen.tsx`
- **User Actions**: Mute, speaker, location, end call
- **State Updates**: 
  ```typescript
  // Duration updates every second during call
  currentCase.duration = elapsedSeconds
  
  // On call end
  const emergencyCase = {
    ...currentCase,
    duration: finalDuration,
    cost: 49.99
  }
  ```
- **Navigation**: → Post Call Summary Screen (no back button)

**Backend/Database**: 
- ⚠️ Call functionality mocked (no real calling)
- Future: Will integrate Twilio Voice SDK for real calls

---

### Step 13: Post-Call Summary Screen

**What Happens:**
1. User sees call summary:
   - Call duration (e.g., "15 minutes")
   - Total cost ($49.99)
   - Lawyer information
   - Service details
2. Options available:
   - Download call recording (future feature)
   - View receipt
   - Follow-up actions
3. Case is saved to history
4. User taps "Done" or back button
5. Returns to Emergency Selection screen

**Technical Details:**
- **Screen**: `PostCallSummaryScreen.tsx`
- **User Action**: Review summary, tap "Done"
- **State Updates**: 
  ```typescript
  // Add case to history
  addCase(emergencyCase)
  
  // Clear current case
  setCurrentCase(null)
  ```
- **Navigation**: → Back to Emergency Selection Tab

**Backend/Database**: 
- ⚠️ Session creation mocked
- Future: Will call `POST /api/sessions` to save session to database

**Data Flow:**
```
Call End → Add to cases array → [Future: POST /api/sessions] → Database
```

---

### Step 14: History Tab

**What Happens:**
1. User navigates to History tab (bottom navigation)
2. User sees list of all past consultation cases
3. Each case shows:
   - Emergency type
   - Date and time
   - Lawyer name
   - Duration
   - Cost
4. User can tap on a case to view details
5. Tapping a case shows Post-Call Summary for that case

**Technical Details:**
- **Screen**: `HistoryScreen.tsx` (Tab 2)
- **Data Source**: Zustand store `cases` array
- **User Action**: Tap case to view details
- **Navigation**: → Post Call Summary Screen (for selected case)

**Backend/Database**: 
- ⚠️ Currently shows only in-memory cases
- Future: Will call `GET /api/sessions/by-profile/{profile_id}` to load from database

---

### Step 15: Profile Tab

**What Happens:**
1. User navigates to Profile tab (bottom navigation)
2. User sees settings sections:
   - **Account Settings**
     - Personal Information
     - Phone Number
     - State
   - **Emergency Settings**
     - Emergency Contacts
     - Location Sharing preferences
     - Notifications
   - **Payment Settings**
     - Payment Methods (tap to manage)
     - Billing History (tap to view)
   - **Legal Settings**
     - Terms of Service
     - Privacy Policy
     - Support
     - Sign Out
3. User can tap on any setting to view/edit

**Technical Details:**
- **Screen**: `ProfileScreen.tsx` (Tab 3)
- **User Actions**: Navigate to sub-screens
- **Navigation**: 
  - → Payment Methods Screen
  - → Billing History Screen

**Backend/Database**: No API calls (settings are local)

---

### Step 16: Payment Methods Screen

**What Happens:**
1. User sees list of saved payment cards
2. Each card shows:
   - Card type (Visa, Mastercard, etc.)
   - Last 4 digits
   - Cardholder name
   - Expiry date
   - Default indicator
3. User can:
   - Add new card
   - Remove card
   - Set default card

**Technical Details:**
- **Screen**: `PaymentMethodsScreen.tsx`
- **Data Source**: `user.savedCards` from Zustand store
- **State Updates**: 
  ```typescript
  savePaymentCard(card) // Add card
  removePaymentCard(cardId) // Remove card
  ```

**Backend/Database**: No API calls (cards stored locally)

---

### Step 17: Billing History Screen

**What Happens:**
1. User sees list of all payment receipts
2. Each receipt shows:
   - Date
   - Service name
   - Amount
   - Payment method
   - Transaction ID
3. User can tap receipt to view details

**Technical Details:**
- **Screen**: `BillingHistoryScreen.tsx`
- **Data Source**: Zustand store `billingHistory` array
- **User Action**: View receipt details

**Backend/Database**: No API calls (receipts stored locally)

---

## Screen Flow Details (Reference)

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

This documentation provides a comprehensive overview of the Safestop application workflow, covering:

- **Complete User Journey**: Step-by-step flow from app launch to completing an emergency consultation
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

