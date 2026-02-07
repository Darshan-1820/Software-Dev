Claude - Plan (Conversation - 01)

Context
What this project is: Assure PAY is a 3-tier production system for secure test card management with NFC tap-to-pay:

Android App (Kotlin/Java): Real EMV/HCE implementation with APDU command processing, NFC payments, cryptogram generation
Backend (Express 5 + MySQL + Knex): REST API on AWS Lambda with AES-256-GCM encryption, AWS KMS, JWT auth, Zod validation
Frontend (React + Vite): Admin dashboard for card management, MCC configuration, user onboarding, test case management
Prototype (React 19 + Vite 7): Mobile-first PWA demo in a single src/App.jsx (1,985 lines) - separate from production

Central Repository: https://github.com/Darshan-1820/Software-Dev.git

This repo is the single source of truth for: learning notes, feature plans, debugging guides, API documentation, and implementation tracking
The plan, educational content, and all documentation will be pushed here

Why this plan exists: Two goals running in parallel:

Feature: Add MCC Category-based transaction filtering (currently only individual MCC code matching exists)
Education: Use the production codebase to teach you full-stack development fundamentals

The current MCC gap: The Android app's EmvCommandHandler.java:316-329 checks if a terminal's MCC exists in the user's allowed MCC list using Arrays.asList(strProfileMcc).contains(strTransMcc) - this is individual code matching only. There is no concept of MCC categories. If an admin wants to allow "all restaurants," they must add every individual restaurant MCC code (5811, 5812, 5813, 5814, etc.) instead of selecting the "Restaurants" category.

Part A: MCC Category Feature Implementation
A.1 What Changes and Why
Problem: When an admin assigns a card, they can only select individual MCC codes (e.g., 5411, 5812). If they want to allow an entire category like "Food & Grocery" (which has 20+ MCC codes), they must manually select each one. There's also no way to say "allow ALL MCCs except these excluded ones."
Solution: Add MCC Categories that group individual codes. Support 3 matching modes:

Individual MCC match (existing) - terminal MCC is in the user's specific MCC list
Category match (new) - terminal MCC belongs to a category the user is allowed
Exclusion list (new) - when "All MCCs" is selected, decline specific blocked MCCs

A.2 Database Changes
New table: mcc_category
sqlCREATE TABLE mcc_category (
    id INT AUTO_INCREMENT PRIMARY KEY,
    environment_id INT NOT NULL,
    category_name VARCHAR(100) NOT NULL,
    description TEXT,
    is_active TINYINT(1) DEFAULT 1,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    created_by INT,
    updated_by INT,
    FOREIGN KEY (environment_id) REFERENCES environments(environment_id),
    FOREIGN KEY (created_by) REFERENCES users(user_id),
    FOREIGN KEY (updated_by) REFERENCES users(user_id)
);
New table: mcc_category_mapping (links categories to individual MCC codes)
sqlCREATE TABLE mcc_category_mapping (
    id INT AUTO_INCREMENT PRIMARY KEY,
    category_id INT NOT NULL,
    mcc_code_id INT NOT NULL,
    is_active TINYINT(1) DEFAULT 1,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES mcc_category(id),
    FOREIGN KEY (mcc_code_id) REFERENCES mcc_code(id),
    UNIQUE KEY (category_id, mcc_code_id)
);
New table: card_request_testing_mcc_category_mapping (links card requests to categories)
sqlCREATE TABLE card_request_testing_mcc_category_mapping (
    id INT AUTO_INCREMENT PRIMARY KEY,
    testing_id INT NOT NULL,
    mcc_category_id INT NOT NULL,
    is_active TINYINT(1) DEFAULT 1,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (testing_id) REFERENCES card_request_testing_details(request_test_id),
    FOREIGN KEY (mcc_category_id) REFERENCES mcc_category(id)
);
Alter user_cards table:
sqlALTER TABLE user_cards
    ADD COLUMN mcc_categories TEXT NULL COMMENT 'Comma-separated MCC category IDs',
    ADD COLUMN mcc_excluded_codes TEXT NULL COMMENT 'Comma-separated excluded MCC codes (when mcc_codes_all=1)';
Alter card_request_testing_details table:
sqlALTER TABLE card_request_testing_details
    ADD COLUMN mcc_excluded_codes TEXT NULL COMMENT 'Excluded MCC codes when all MCCs selected';

### A.3 Backend Changes

**Files to create:**
- `models/MccCategory.js` - CRUD model (pattern: copy `models/MccCodes.js`)
- `models/MccCategoryMapping.js` - Category-to-code mapping model
- `controllers/mccCategoryController.js` - REST controller (pattern: copy `controllers/mccCodeController.js`)
- `routes/mccCategoryRoutes.js` - Route definitions (pattern: copy `routes/mccCodeRoutes.js`)
- `migrations/YYYYMMDD_add_mcc_categories.js` - Knex migration for all new tables + alterations

**Files to modify:**
- `index.js` - Add `mccCategoryRoutes` to Express app (~line 60)
- `service/CardRequestService.js` - `handleTestInfoData()`: Add category mapping logic alongside existing MCC mapping (currently handles `mccCodes` array, needs to also handle `mccCategories` array and `mccExcludedCodes`)
- `service/CardRequestMappingService.js` - Add methods: `createRequestTestingMccCategoryMapping()`, `removeRequestTestingMccCategoryMappingsAll()`, `checkMccCategoriesWithIDS()`
- `models/UserCard.js` - `findRequestData()`: Extend SQL to JOIN `card_request_testing_mcc_category_mapping` and `mcc_category` tables, returning `mcc_categories` alongside `mcc_codes`
- `models/Card.js` - `findById()`: Extend MCC resolution logic (lines 281-362) to also resolve categories to their individual codes for the mobile app
- `controllers/UserCardController.js` - `create()`: Add `mcc_categories` and `mcc_excluded_codes` to the snapshot object (around line 500)
- `service/UserCard.js` - `updateAssignedProfileDetailsWithLimits()`: When building the profile data, resolve categories to individual MCC codes and merge with individual selections, handle exclusions

**Key backend logic - MCC resolution for mobile profile:**
```
When building the comma-separated MCC string for the mobile app profile:
1. Start with individually selected MCC codes
2. For each selected category, query all MCC codes in that category
3. Merge both lists (deduplicate)
4. If mcc_codes_all = true, set mcc = "" (empty = allow all)
   BUT also send excluded codes separately
5. Store resolved codes in user_cards.mcc_codes
6. Store excluded codes in user_cards.mcc_excluded_codes
```
New API Endpoints:

GET /api/v2/mcc-categories?environment=<id> - List all categories with their MCC codes
POST /api/v2/mcc-categories - Create category
PUT /api/v2/mcc-categories/:id - Update category
DELETE /api/v2/mcc-categories/:id - Soft delete
POST /api/v2/mcc-categories/:id/codes - Add MCC codes to category
DELETE /api/v2/mcc-categories/:id/codes/:codeId - Remove code from category

A.4 Android App Changes
File: EmvCommandHandler.java (lines 316-329)
Current MCC check:
javaString strTransMcc = transactionContext.getMerchantCategoryCode();
String[] strProfileMcc = personalization.getMcc();
if (strProfileMcc.length >= 1 && !strProfileMcc[0].replace(" ", "").equals("")) {
    boolean isContained = Arrays.asList(strProfileMcc).contains(strTransMcc);
    if (!isContained) {
        return new byte[]{(byte) 0x69, (byte) 0x85}; // Decline
    }
}
New MCC check (replaces above):
javaString strTransMcc = transactionContext.getMerchantCategoryCode();
String[] strProfileMcc = personalization.getMcc();
String[] strExcludedMcc = personalization.getExcludedMcc(); // NEW METHOD

// Case 1: Specific MCCs allowed (individual + category-resolved codes merged by backend)
if (strProfileMcc.length >= 1 && !strProfileMcc[0].replace(" ", "").equals("")) {
    boolean isContained = Arrays.asList(strProfileMcc).contains(strTransMcc);
    if (!isContained) {
        Logger.debug(TAG, "MCC Not allowed: " + strTransMcc);
        if (transCallback != null) {
            transCallback.onTransDetailProcessed(personalization, transactionContext, false);
        }
        return new byte[]{(byte) 0x69, (byte) 0x85};
    }
}
// Case 2: All MCCs allowed BUT check exclusion list
else if (strExcludedMcc != null && strExcludedMcc.length >= 1
         && !strExcludedMcc[0].replace(" ", "").equals("")) {
    boolean isExcluded = Arrays.asList(strExcludedMcc).contains(strTransMcc);
    if (isExcluded) {
        Logger.debug(TAG, "MCC Excluded: " + strTransMcc);
        if (transCallback != null) {
            transCallback.onTransDetailProcessed(personalization, transactionContext, false);
        }
        return new byte[]{(byte) 0x69, (byte) 0x85};
    }
}
// Case 3: All MCCs allowed, no exclusions -> proceed
Files to modify:

Personalization.java - Add interface method: String[] getExcludedMcc()
JsonPersonalization.java - Implement getExcludedMcc() reading from "excludedmcc" JSON field (pattern: same as existing getMcc() at lines 1317-1325)
EmvCommandHandler.java - Replace MCC check block (lines 316-329) with the 3-case logic above
MccCode.kt - No changes needed (already has mcc_code + description)

Profile JSON changes:
json{
  "mcc": "5411,5412,5812,5813",       // existing: individual + category-resolved codes
  "excludedmcc": "7995,5933",         // NEW: excluded codes when mcc="" (all allowed)
  "countrycode": "0840,0356"          // existing: unchanged
}

### A.5 Frontend Changes

**New file:** `src/pages/manage-mcc-categories/ManageMccCategories.jsx`
- Admin page to create/edit MCC categories
- Form: Category name, description, multi-select of MCC codes to include
- Table: List of categories with expandable rows showing included codes
- Pattern: Follow `ManageMccv2.jsx` structure

**New file:** `src/services/mccCategory.js`
- API service for MCC category CRUD
- Pattern: Copy `src/services/mccCode.js`

**Files to modify:**
- `src/Entry.jsx` - Add route: `/dashboard/system-defaults-v2/manage-mcc-categories` -> `ManageMccCategories`
- `src/services/index.js` - Register `mccCategory` service
- `src/pages/test-card-request/files/TestInfo.jsx` - Add MCC category selection alongside existing individual MCC selection:
  - New section: "Select MCC Categories" (multi-select dropdown)
  - New section: "Excluded MCCs" (only shown when "All" is checked)
  - Existing individual MCC selection remains
- `src/pages/cards/UpdateCardAssign.jsx` - Add category selection fields and exclusion fields

### A.6 Data Flow Summary
```
Admin selects MCCs for card assignment:
  ├── Individual MCCs: [5411, 5812]
  ├── Categories: [Food & Grocery, Travel]
  └── Exclusions (if "All"): [7995]
        │
        ▼
Backend resolves categories to individual codes:
  Food & Grocery = [5411, 5412, 5422, 5441, 5451, 5462, 5499]
  Travel = [3000-3299, 4511, 4722, 7011, 7032, 7033]
  Merged + Individual = [5411, 5412, 5422, ..., 5812]
  Deduplicated → stored in user_cards.mcc_codes
  Exclusions → stored in user_cards.mcc_excluded_codes
        │
        ▼
Mobile profile JSON built:
  { "mcc": "5411,5412,5422,...,5812", "excludedmcc": "" }
  OR (if "All"): { "mcc": "", "excludedmcc": "7995" }
        │
        ▼
Transaction occurs at terminal:
  Terminal sends MCC (e.g., "5812") via PDOL
        │
        ▼
EmvCommandHandler checks:
  Case 1: mcc list not empty → check if 5812 in list → Allow/Decline
  Case 2: mcc list empty + exclusions exist → check if 5812 excluded → Allow/Decline
  Case 3: mcc list empty + no exclusions → Allow all
```

---

## Part B: Learning Plan - Become a Full-Stack Developer

### How Learning Works

**Method: "Learn by Building"** - Every lesson uses YOUR production code as the textbook. No abstract tutorials. You learn a concept, then immediately see it in your own system, then apply it while building the MCC Category feature.

**Structure:** 10 Learning Modules, each one produces a markdown file pushed to `learning/` in the Software-Dev repo. Each module follows this pattern:
1. **Concept** - What it is, why it matters (explained simply)
2. **Your Code** - Where this concept lives in your production system (exact files + line numbers)
3. **Walkthrough** - I explain your code line by line
4. **Build Exercise** - You write similar code for the MCC Category feature
5. **Debug Challenge** - I break something, you find the bug

**Schedule:** Learning modules alternate with implementation steps. You learn the concept, then immediately use it.

---

### Module 1: JavaScript Fundamentals - Variables, Functions, Control Flow
**File produced:** `learning/01-javascript-fundamentals.md`
**Your code:** `src/App.jsx` (prototype) - lines 9-20 (constants), lines 23-39 (functions), lines 229-302 (helpers)
**Topics:**
- Variables: `const` vs `let` vs `var` and why your code uses `const` everywhere
- Functions: Regular vs arrow functions, parameters, return values
- Control flow: `if/else`, ternary operators, short-circuit evaluation (`&&`, `||`)
- Objects and arrays: Creating, reading, modifying data structures
- Template literals, destructuring, spread operator
**Build exercise:** Write a function that takes an MCC code and returns which category it belongs to

### Module 2: Arrays & Data Structures
**File produced:** `learning/02-arrays-and-data-structures.md`
**Your code:** Prototype `ALL_USERS` (line 52), `ALL_CARDS` (line 66), `ALL_TXNS` (line 97)
**Topics:**
- Array methods: `.find()`, `.filter()`, `.map()`, `.reduce()`, `.includes()`, `.some()`
- When to use which method (decision tree)
- Objects as hash maps: O(1) lookup vs O(n) array search
- Stack pattern: `navHistory` in your app (push/pop navigation)
- Sets: deduplication (needed for merging MCC codes from categories)
**Build exercise:** Write the MCC category resolver - take an array of category IDs, look up all codes in each category, merge and deduplicate
**Ties to feature:** This is exactly what the backend needs to do in Step 4 (Profile Building)

### Module 3: Async Programming - Promises, Async/Await, Error Handling
**File produced:** `learning/03-async-programming.md`
**Your code:** `controllers/UserCardController.js` (the 887-line `create()` method)
**Topics:**
- Why async exists: JavaScript is single-threaded, can't wait for database
- Callbacks → Promises → Async/Await (evolution)
- `try/catch` for error handling (your controller wraps everything in it)
- `Promise.all()` for parallel operations (your MCC mapping uses this)
- Database connections and transactions (`connection.beginTransaction()`, `commit()`, `rollback()`)
**Build exercise:** Write the `mccCategoryController.create()` method with proper async/await and error handling
**Ties to feature:** This is Step 2 (Backend Models & Controllers)

### Module 4: SQL & Database Design
**File produced:** `learning/04-sql-and-databases.md`
**Your code:** `database.ddl.sql` (1123 lines), `models/UserCard.js:findRequestData()`, `models/MccCodes.js`
**Topics:**
- Tables, rows, columns - what they are (using your `mcc_code` table)
- Primary keys, foreign keys, indexes - why they matter
- CRUD: INSERT, SELECT, UPDATE, DELETE
- JOINs: LEFT JOIN, INNER JOIN (your `findRequestData` has 4 JOINs)
- Aggregation: GROUP BY, GROUP_CONCAT (how your code combines MCC codes into one string)
- Migrations: Version-controlled schema changes with Knex
- Normalization: Why `card_request_testing_mcc_mapping` exists as a junction table instead of storing codes directly
**Build exercise:** Write the Knex migration for `mcc_category`, `mcc_category_mapping`, and `card_request_testing_mcc_category_mapping`
**Ties to feature:** This is Step 1 (Database Migration)

### Module 5: REST APIs & Express.js
**File produced:** `learning/05-rest-apis-express.md`
**Your code:** `index.js` (route mounting), `routes/mccCodeRoutes.js`, `controllers/mccCodeController.js`
**Topics:**
- HTTP methods: GET, POST, PUT, DELETE and when to use each
- Express routing: How `app.use('/api/v2/mcc-code', mccCodeRoutes)` connects URLs to code
- Request/Response cycle: `req.params`, `req.body`, `req.query` → processing → `res.json()`
- Middleware chain: authMiddleware → roleCheck → controller
- Input validation with Zod: Why validate on the server even if frontend validates
- Error responses: Status codes (200, 400, 401, 403, 404, 500)
**Build exercise:** Build the complete MCC Category API (routes + controller + model)
**Ties to feature:** This is Step 2 (Backend Models & Controllers)

### Module 6: Authentication & Security
**File produced:** `learning/06-auth-and-security.md`
**Your code:** `middlewares/authMiddleware.js`, `utils/encryptDecrypt.js`, `utils/kms.js`
**Topics:**
- Password hashing: Why bcrypt, how salting works (your `UserCardController.create()` uses it)
- JWT tokens: How login produces a token, how middleware verifies it on every request
- Encryption at rest: AES-256-GCM (your card data encryption)
  - Symmetric vs asymmetric encryption
  - IV (Initialization Vector): Why encrypt the same data differently each time
  - Auth Tag: Tamper detection
- AWS KMS: Why you don't store encryption keys in your code
- Role-based access control: `checkRoleAccess("getAllMccCodes")` in your routes
- Audit trails: Logging who changed what, when, and the before/after values
**Build exercise:** Add proper role-based access to MCC Category endpoints

### Module 7: EMV & Card Processing
**File produced:** `learning/07-emv-card-processing.md`
**Your code:** `EmvCommandHandler.java` (615 lines), `Personalization.java`, `TransactionContext.java`
**Topics:**
- What happens when you tap your phone at a terminal (step by step)
- APDU commands: SELECT, READ RECORD, GET PROCESSING OPTIONS, GET DATA
- TLV (Tag-Length-Value): How card data is encoded in bytes
- PDOL: What data the terminal sends to the card (amount, MCC, country, date)
- Cryptogram generation: TC (approve), ARQC (ask bank), AAC (decline)
- HCE (Host Card Emulation): How Android pretends to be a physical card
- The MCC check: Where in the APDU flow the MCC gets checked (GPO command, line 316)
**Build exercise:** Add the exclusion list check to the EMV handler
**Ties to feature:** This is Step 5 (Android Changes)

### Module 8: React & Frontend Architecture
**File produced:** `learning/08-react-frontend.md`
**Your code:** `src/App.jsx` (prototype, 1985 lines), production `assure-frontend-live/src/`
**Topics:**
- React component model: Functions that return JSX
- useState: Why `const [x, setX] = useState()` and how re-rendering works
- useEffect: Side effects, cleanup, dependency arrays
- Props: Parent-to-child data flow
- Component composition: Breaking big components into small reusable ones
- State management patterns: Context API, lifting state up
- Forms: Controlled inputs, validation, submission
- Comparing prototype (single file) vs production (proper structure with pages/components/services)
**Build exercise:** Build the MCC Category management page for the admin dashboard
**Ties to feature:** This is Step 6 (Frontend Admin Pages)

### Module 9: System Design & Architecture
**File produced:** `learning/09-system-design.md`
**Your code:** All three codebases together
**Topics:**
- Client-Server architecture: How your Android app talks to the backend
- 3-tier architecture: Presentation (React) → Logic (Express) → Data (MySQL)
- MVC pattern: Models → Controllers → Routes (your backend structure)
- Service layer: Why `CardRequestService.js` exists between controllers and models
- API design: RESTful conventions, versioning (`/api/v2/`)
- Data flow tracing: Follow an MCC code from admin dashboard → backend → database → mobile app → terminal
- Offline-first: Why the Android app checks MCC locally instead of calling the backend during payment
- Scalability: Why AWS Lambda, connection pooling, KMS key management
**Build exercise:** Draw the complete data flow for the MCC Category feature (all 3 tiers)

### Module 10: Debugging & Problem Solving
**File produced:** `learning/10-debugging-problem-solving.md`
**Your code:** Real bugs and issues from the production system
**Topics:**
- Reading error messages: Stack traces, error codes (0x6985 = "conditions of use not satisfied")
- Console debugging: `console.log()` vs `Logger.debug()` vs proper logging with Winston
- Database debugging: How to verify data with SQL queries
- API debugging: Using Postman/Thunder Client to test endpoints
- Mobile debugging: ADB logs, APDU command traces
- Common patterns: Off-by-one errors, null checks, async race conditions
- Systematic approach: Reproduce → Isolate → Fix → Verify
**Build exercise:** I'll introduce 3 intentional bugs in the MCC Category code. You find and fix them.

---

### Learning + Building Schedule

| Order | Activity | What You Learn/Build |
|-------|----------|---------------------|
| 1 | **Module 1**: JS Fundamentals | Variables, functions, control flow |
| 2 | **Module 2**: Arrays & Data Structures | Array methods, deduplication |
| 3 | **Module 4**: SQL & Databases | Tables, JOINs, migrations |
| 4 | **Step 1**: Database Migration | *Apply Module 4* - write the migration |
| 5 | **Module 3**: Async Programming | Promises, async/await, transactions |
| 6 | **Module 5**: REST APIs | Express routes, controllers, validation |
| 7 | **Steps 2-3**: Backend Implementation | *Apply Modules 3+5* - build API |
| 8 | **Module 6**: Auth & Security | JWT, encryption, roles |
| 9 | **Step 4**: Profile Building | *Apply Module 6* - handle encrypted profiles |
| 10 | **Module 7**: EMV Processing | APDU, cryptograms, HCE |
| 11 | **Step 5**: Android Changes | *Apply Module 7* - modify EMV handler |
| 12 | **Module 8**: React & Frontend | Components, state, forms |
| 13 | **Step 6**: Frontend Pages | *Apply Module 8* - build admin UI |
| 14 | **Module 9**: System Design | Architecture, data flow |
| 15 | **Step 7**: Prototype Update | *Apply all modules* |
| 16 | **Module 10**: Debugging | Find and fix bugs in everything we built |

Each completed module = a markdown file pushed to `learning/` in the Software-Dev repo. Each completed step = code changes documented in `plan/` and committed to the appropriate production repos.

---

## Implementation Order

### Step 0: Repository Setup & Push Plan
- Add `https://github.com/Darshan-1820/Software-Dev.git` as the remote for the worktree (replace current `origin` which points to Assure-Pay)
- OR: Clone Software-Dev repo separately and copy plan + docs into it
- Push this plan file as the first commit so you can see it on GitHub
- Create folder structure in the repo:
```
  Software-Dev/
  ├── plan/
  │   └── mcc-category-feature-plan.md     ← This plan
  ├── learning/
  │   ├── javascript-fundamentals.md
  │   ├── emv-card-processing.md
  │   ├── system-architecture.md
  │   ├── databases-sql.md
  │   └── debugging-guide.md
  ├── api-docs/
  │   └── mcc-category-api.md
  └── README.md                            ← Overview + links
```

Each learning session will produce a markdown file pushed to learning/
Each feature implementation will be documented in plan/

Step 1: Database Migration

Create Knex migration file with all new tables and alterations
File: migrations/YYYYMMDD_add_mcc_categories.js

Step 2: Backend Models & Controllers

Create MccCategory model and controller
Add routes to index.js

Step 3: Backend Service Logic

Update CardRequestMappingService with category mapping methods
Update CardRequestService.handleTestInfoData() for categories
Update UserCard.findRequestData() SQL to include categories
Update Card.findById() to resolve categories

Step 4: Profile Building

Update UserCard.js service to resolve categories to individual codes
Handle exclusion list in profile JSON

Step 5: Android Changes

Add getExcludedMcc() to Personalization interface and JsonPersonalization
Update EmvCommandHandler.java MCC check with 3-case logic

Step 6: Frontend Admin Pages

Create MCC Category management page
Update TestInfo.jsx with category and exclusion selection
Update card assignment forms

Step 7: Prototype Update

Mirror the MCC category selection in the prototype's share flow (App.jsx lines 1304-1519)
Add exclusion list UI to the limits form (lines 1187-1299)


Files to Modify/Create (Complete List)
Create:

assure-backend-live/migrations/YYYYMMDD_add_mcc_categories.js
assure-backend-live/models/MccCategory.js
assure-backend-live/models/MccCategoryMapping.js
assure-backend-live/controllers/mccCategoryController.js
assure-backend-live/routes/mccCategoryRoutes.js
assure-frontend-live/src/pages/manage-mcc-categories/ManageMccCategories.jsx
assure-frontend-live/src/services/mccCategory.js

Modify:

assure-backend-live/index.js (add category routes)
assure-backend-live/service/CardRequestService.js (category handling in handleTestInfoData)
assure-backend-live/service/CardRequestMappingService.js (category mapping methods)
assure-backend-live/models/UserCard.js (extend findRequestData SQL)
assure-backend-live/models/Card.js (extend findById for category resolution)
assure-backend-live/controllers/UserCardController.js (categories in snapshot)
assure-backend-live/service/UserCard.js (resolve categories for profile)
Assure_Mobile-production/.../Personalization.java (add getExcludedMcc)
Assure_Mobile-production/.../JsonPersonalization.java (implement getExcludedMcc)
Assure_Mobile-production/.../EmvCommandHandler.java (3-case MCC logic)
assure-frontend-live/src/Entry.jsx (add category route)
assure-frontend-live/src/services/index.js (register category service)
assure-frontend-live/src/pages/test-card-request/files/TestInfo.jsx (category + exclusion UI)
assure-frontend-live/src/pages/cards/UpdateCardAssign.jsx (category fields)
quizzical-hodgkin/src/App.jsx (prototype MCC category UI)


Verification Plan

Migration: Run npx knex migrate:latest - verify tables created in MySQL
Backend API: Test CRUD endpoints with Postman/Thunder Client:

Create category with MCC codes
List categories with resolved codes
Assign card with category selection
Verify profile JSON includes resolved individual codes


Android: Build APK, test with NFC terminal:

Card with specific categories → allowed MCC in category → approved
Card with specific categories → MCC NOT in any category → declined (0x6985)
Card with "All" + exclusion [7995] → MCC 7995 → declined
Card with "All" + exclusion [7995] → MCC 5411 → approved


Frontend: Test admin flows:

Create MCC category with codes
Select categories during card request
Select "All" and configure exclusions
View assigned card shows categories


Prototype: npm run dev in quizzical-hodgkin → verify share flow shows categories

-----------------------------------------------------------
Create the repo structure and follow claude guidelines, no code implementation (regarding mcc improvement right now)
----
Find out Software Developers practices, how they plan,
Before diving in the coding part, we will understand the current structure, we will create the system design, architecture then understand everything then we will develop that feature together.

----------------------------------------------------------
Additionally I'm adding the three folders frontend, backend and mobile production code into this folder too. You can help me in learning too along with claude.
Don't push these files into the github repo.
