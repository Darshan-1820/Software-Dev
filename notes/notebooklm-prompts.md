# NotebookLM Research Prompts

These prompts are designed for NotebookLM to search, import sources, and answer only from those sources. Use one prompt per module.

## Global Prompt (Research-first)
```
You are my NotebookLM research assistant.

Your job:
1) Find and import the best free beginner-friendly resources.
2) Summarize only from those sources.
3) Cite the source titles in every section.

Topics (do NOT answer without sources):
JavaScript, DSA, DSA with Python, React, Node.js, Databases, System Design, System Architecture, APIs, Cloud (AWS), CLI, Linux commands, Git, GitHub, Debugging AI code, Debugging production code, Problem solving, Planning how to approach a concept, Planning my study and taking notes, Using a repository to record, Using Obsidian for learning.

How to work:
- Step 1: Search and import 5–10 high-quality free sources per topic.
- Step 2: Give a short learning order (what first, what later).
- Step 3: Give a “what to ignore for now” list.
- Step 4: Summarize each topic in simple words.
- Step 5: Provide 3–5 best free resources (from imported sources).
- Step 6: End with a 7-day starter plan.

Rules:
- No opinions without sources.
- Keep answers short.
- Use bullets.
- Assume I am a complete beginner.
- Explain like I’m 5, but don’t be shallow.
```

---

## Module 01 — JavaScript Fundamentals
```
You are my NotebookLM research assistant.

Module: JavaScript Fundamentals

First: search and import the best free beginner-friendly sources for JavaScript fundamentals.
Use only sources you import.

Focus topics (do not skip any):
- Variables (`const`, `let`, `var`)
- Primitive types (string, number, boolean, null, undefined)
- Functions (declaration, expression, arrow)
- Parameters and return values
- Control flow (`if/else`, `switch`, ternary)
- Logical operators (`&&`, `||`, `!`)
- Arrays (create, read, update)
- Objects (create, read, update)
- Template literals
- Destructuring
- Spread and rest operators
- Basic loops (`for`, `while`, `for...of`)
- Basic error handling (`try/catch`)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 02 — Arrays & Data Structures
```
You are my NotebookLM research assistant.

Module: Arrays & Data Structures

First: search and import the best free beginner-friendly sources for arrays and basic data structures in JavaScript.
Use only sources you import.

Focus topics (do not skip any):
- Arrays vs objects (when to use each)
- Array methods: `find`, `filter`, `map`, `reduce`, `includes`, `some`, `every`
- Mutating vs non-mutating methods
- Objects as hash maps (key/value lookup)
- Sets (deduplication)
- Stacks and queues (basic idea)
- Big-O basics (O(1), O(n))
- Deduplication patterns

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 03 — Async Programming in JavaScript
```
You are my NotebookLM research assistant.

Module: Async Programming (Promises, Async/Await)

First: search and import the best free beginner-friendly sources for JavaScript async programming.
Use only sources you import.

Focus topics (do not skip any):
- Why async exists (single-threaded JS)
- Callbacks
- Promises (states, `then`, `catch`, `finally`)
- Async/await (basic usage)
- Error handling in async (`try/catch`)
- `Promise.all` and parallel work
- Common async mistakes (not awaiting, race conditions)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 04 — Databases & SQL Fundamentals
```
You are my NotebookLM research assistant.

Module: Databases & SQL Fundamentals

First: search and import the best free beginner-friendly sources for SQL and relational databases.
Use only sources you import.

Focus topics (do not skip any):
- What is a database, table, row, column
- Primary keys and foreign keys
- Basic SQL: SELECT, INSERT, UPDATE, DELETE
- WHERE filtering
- JOINs (INNER, LEFT)
- Indexes (why they matter)
- Normalization (basic idea)
- Schema vs data
- Migrations (concept)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 05 — REST APIs & Express.js
```
You are my NotebookLM research assistant.

Module: REST APIs & Express.js

First: search and import the best free beginner-friendly sources for REST APIs and Express.js.
Use only sources you import.

Focus topics (do not skip any):
- What is an API
- HTTP methods (GET, POST, PUT, DELETE)
- Request/response cycle
- Status codes (200, 201, 400, 401, 403, 404, 500)
- REST basics (resources, URLs)
- Express routing
- Middleware (concept)
- Validation (why it matters)
- JSON request/response

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 06 — Auth & Security Basics
```
You are my NotebookLM research assistant.

Module: Authentication & Security Basics

First: search and import the best free beginner-friendly sources for auth and security basics.
Use only sources you import.

Focus topics (do not skip any):
- Authentication vs authorization
- Password hashing (what and why)
- JWT basics (what it is, why used)
- Sessions vs tokens (high level)
- Encryption basics (symmetric vs asymmetric)
- HTTPS (what it protects)
- Input validation (security reason)
- Common risks (SQL injection, XSS - basic idea)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 07 — EMV & Card Processing (Beginner)
```
You are my NotebookLM research assistant.

Module: EMV & Card Processing (Beginner)

First: search and import the best free beginner-friendly sources for EMV and card processing.
Use only sources you import.

Focus topics (do not skip any):
- What is EMV
- What happens in a card tap (high-level)
- APDU commands (basic idea)
- TLV format (basic idea)
- PDOL (what it sends)
- Cryptograms (TC, ARQC, AAC - high-level)
- HCE (Host Card Emulation) on Android
- Where MCC is used in a payment

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 08 — React & Frontend Fundamentals
```
You are my NotebookLM research assistant.

Module: React & Frontend Fundamentals

First: search and import the best free beginner-friendly sources for React fundamentals.
Use only sources you import.

Focus topics (do not skip any):
- Components (function components)
- JSX basics
- Props (data flow)
- State (`useState`)
- Effects (`useEffect`)
- Controlled inputs/forms
- Conditional rendering
- Lists and keys
- Component composition
- Basic folder structure

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 09 — System Design & Architecture (Beginner)
```
You are my NotebookLM research assistant.

Module: System Design & Architecture (Beginner)

First: search and import the best free beginner-friendly sources for system design and architecture.
Use only sources you import.

Focus topics (do not skip any):
- Client-server model
- 3-tier architecture
- MVC pattern (high level)
- Service layer (why it exists)
- Data flow (end-to-end thinking)
- Scalability basics (what it means)
- Reliability basics (what it means)
- Trade-offs (simplicity vs scale)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 10 — Debugging & Problem Solving
```
You are my NotebookLM research assistant.

Module: Debugging & Problem Solving

First: search and import the best free beginner-friendly sources for debugging and problem solving.
Use only sources you import.

Focus topics (do not skip any):
- Reproduce the problem
- Read the error message
- Logging and tracing
- Narrowing the scope
- Fix smallest thing first
- Retest and verify
- Debugging production vs local
- Debugging AI-generated code (extra verification)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 11 — Git & GitHub (Beginner)
```
You are my NotebookLM research assistant.

Module: Git & GitHub (Beginner)

First: search and import the best free beginner-friendly sources for Git and GitHub.
Use only sources you import.

Focus topics (do not skip any):
- What git is vs GitHub
- Repository, commit, branch
- Add/commit/push flow
- Pulling and merging
- `.gitignore`
- Commit messages (good vs bad)
- Pull requests (basic idea)
- Resolving conflicts (basic idea)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 12 — CLI & Linux Commands (Beginner)
```
You are my NotebookLM research assistant.

Module: CLI & Linux Commands (Beginner)

First: search and import the best free beginner-friendly sources for CLI/Linux commands.
Use only sources you import.

Focus topics (do not skip any):
- What is CLI and why it matters
- Navigation (`pwd`, `ls`, `cd`)
- Files/folders (`mkdir`, `touch`, `rm`, `mv`, `cp`)
- Viewing files (`cat`, `less`, `head`, `tail`)
- Searching (`grep`, `find`)
- Permissions (basic idea)
- Environment variables (basic idea)
- Windows vs Linux differences (basic idea)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 13 — Node.js Fundamentals
```
You are my NotebookLM research assistant.

Module: Node.js Fundamentals

First: search and import the best free beginner-friendly sources for Node.js.
Use only sources you import.

Focus topics (do not skip any):
- What Node.js is
- `npm` basics
- Modules (`require`, `import`)
- Simple server idea
- File system basics (read/write)
- Environment variables (basic idea)
- Common errors (missing packages)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 14 — APIs (General)
```
You are my NotebookLM research assistant.

Module: APIs (General)

First: search and import the best free beginner-friendly sources for APIs.
Use only sources you import.

Focus topics (do not skip any):
- What is an API
- Request/response basics
- JSON format
- Authentication basics (API keys, tokens)
- Rate limits (basic idea)
- Error handling (basic idea)
- API documentation (why it matters)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 15 — Cloud (AWS) Basics
```
You are my NotebookLM research assistant.

Module: Cloud (AWS) Basics

First: search and import the best free beginner-friendly sources for AWS basics.
Use only sources you import.

Focus topics (do not skip any):
- What is cloud computing
- Regions and availability zones (basic idea)
- EC2 (what it is)
- S3 (what it is)
- IAM basics (permissions)
- Serverless basics (Lambda)
- Pricing basics (why it matters)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 16 — DSA Fundamentals
```
You are my NotebookLM research assistant.

Module: Data Structures & Algorithms Fundamentals

First: search and import the best free beginner-friendly sources for DSA fundamentals.
Use only sources you import.

Focus topics (do not skip any):
- Big-O basics (O(1), O(n), O(log n))
- Arrays and strings
- Linked lists (basic idea)
- Stacks and queues
- Hash maps / dictionaries
- Sorting (basic idea)
- Searching (linear vs binary)
- Recursion (basic idea)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 17 — DSA with Python
```
You are my NotebookLM research assistant.

Module: DSA with Python

First: search and import the best free beginner-friendly sources for DSA with Python.
Use only sources you import.

Focus topics (do not skip any):
- Python lists vs tuples
- Python dictionaries (hash maps)
- Sets in Python
- List comprehensions (basic idea)
- Basic sorting and searching in Python
- Implementing stack and queue in Python
- Recursion in Python (basic idea)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 18 — Problem Solving (Beginner)
```
You are my NotebookLM research assistant.

Module: Problem Solving (Beginner)

First: search and import the best free beginner-friendly sources for problem solving in programming.
Use only sources you import.

Focus topics (do not skip any):
- Understanding the problem statement
- Inputs and outputs
- Constraints (why they matter)
- Breaking into steps
- Writing a simple plan
- Testing with small examples
- Common mistakes (off-by-one, nulls)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
- I want to learn by doing, not memorizing.
```

## Module 19 — Planning How to Learn & Take Notes
```
You are my NotebookLM research assistant.

Module: Planning Learning & Taking Notes

First: search and import the best free beginner-friendly sources for learning planning and note-taking.
Use only sources you import.

Focus topics (do not skip any):
- Learning goals (short vs long)
- Spaced repetition (basic idea)
- Active recall (basic idea)
- Project-based learning
- Note-taking systems (simple)
- Avoiding overwhelm (scoping)
- Tracking progress

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software learning
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
```

## Module 20 — GitHub Repo for Learning
```
You are my NotebookLM research assistant.

Module: Using a GitHub Repo for Learning

First: search and import the best free beginner-friendly sources for using GitHub to document learning.
Use only sources you import.

Focus topics (do not skip any):
- README purpose
- Organizing folders
- Writing learning logs
- Issues for questions
- Commit history as progress
- What to keep private vs public

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software learning
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
```

## Module 21 — Using Obsidian for Learning
```
You are my NotebookLM research assistant.

Module: Using Obsidian for Learning

First: search and import the best free beginner-friendly sources for Obsidian note-taking.
Use only sources you import.

Focus topics (do not skip any):
- Vaults and folders
- Markdown basics (headings, lists, code)
- Links between notes
- Tags and search
- Simple templates
- Daily notes

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software learning
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
```

## Module 22 — Debugging Production Systems
```
You are my NotebookLM research assistant.

Module: Debugging Production Systems

First: search and import the best free beginner-friendly sources for debugging production systems.
Use only sources you import.

Focus topics (do not skip any):
- Monitoring and logging (why it matters)
- Reproducing safely
- Rollbacks (basic idea)
- Feature flags (basic idea)
- Incident timelines
- Postmortems (basic idea)
- Customer impact vs urgency

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
```

## Module 23 — Debugging AI-Generated Code
```
You are my NotebookLM research assistant.

Module: Debugging AI-Generated Code

First: search and import the best free beginner-friendly sources for verifying AI-generated code.
Use only sources you import.

Focus topics (do not skip any):
- Why AI code can be wrong
- Reading the code carefully
- Testing assumptions
- Searching docs for APIs
- Adding minimal tests
- Security risks (basic idea)
- Licensing/attribution (basic idea)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
```

## Module 24 — System Architecture (Beginner)
```
You are my NotebookLM research assistant.

Module: System Architecture (Beginner)

First: search and import the best free beginner-friendly sources for system architecture.
Use only sources you import.

Focus topics (do not skip any):
- What architecture means
- Layers and boundaries
- Monolith vs microservices (basic idea)
- Data flow between components
- Latency and performance basics
- Security boundaries (basic idea)

Then answer in this structure (cite sources in every section):
1) Simple Explanation (1–2 lines each topic)
2) Real-World Example per topic
3) 10-minute practice exercise
4) How this appears in real software
5) Best free resources (3–5 from sources)
6) What NOT to learn yet
7) Next-step checklist

Rules:
- No answer without sources.
- Use only imported sources.
- Keep it practical and beginner-friendly.
```
