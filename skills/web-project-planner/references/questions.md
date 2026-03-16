# Phase 1: Requirements Gathering — Q&A Templates

Use the AskUserQuestion tool to ask questions by category. Bundle up to 4 questions at a time. Proceed with follow-up questions based on user responses.

## Round 1: Project Basic Information

Ask the following 4 questions:

1. **Project Type**
   - header: "Project Type"
   - question: "What kind of website are you building?"
   - options:
     - "Corporate/Brand Site" — Company/brand intro, portfolio
     - "Booking/Order Management System" — Reservations, orders, scheduling
     - "Community/Forum" — User community, forum, blog
     - "Dashboard/Admin System" — Data management, admin panel

2. **User Types**
   - header: "User Types"
   - question: "What types of users will use the system? (Multiple selection allowed)"
   - multiSelect: true
   - options:
     - "General Users (Customers)" — Regular customers using the service
     - "Administrators (Operators)" — System operators/managers
     - "Staff/Employees" — Internal staff with limited permissions
     - "Anonymous Visitors" — Visitors without login

3. **Core Feature**
   - header: "Core Feature"
   - question: "What is the most important core feature?"
   - options:
     - "Booking/Scheduling" — Date/time-based reservation management
     - "Product/Service Management" — Product registration, catalog, pricing
     - "User Management/Auth" — Sign-up, login, permission management
     - "Content Management" — Posts, announcements, page management

4. **Project Name**
   - header: "Project Name"
   - question: "What is the project name (or service name)?"
   - options:
     - "Not decided yet" — Will decide later
     - "Enter manually" — Enter the project name directly

## Round 2: Tech Stack & Infrastructure

If Phase 0 detected an existing tech stack, present it as the default. Ask the following 4 questions:

1. **Frontend Framework**
   - header: "Framework"
   - question: "Which frontend framework will you use?"
   - options:
     - "Next.js (App Router) (Recommended)" — React-based, SSR/SSG, full-stack
     - "React + Vite" — SPA, client-side rendering
     - "Nuxt.js" — Vue.js-based full-stack framework
     - "Astro" — Content-focused static sites

2. **Backend/Database**
   - header: "Backend/DB"
   - question: "How will you set up the backend and database?"
   - options:
     - "Supabase (PostgreSQL) (Recommended)" — BaaS, integrated auth/DB/storage
     - "Firebase (Firestore)" — Google BaaS, NoSQL
     - "Dedicated Backend Server (Express/NestJS)" — Node.js-based custom API server
     - "Enter manually" — Other tech stack

3. **Deployment Environment**
   - header: "Deployment"
   - question: "Where will you deploy?"
   - options:
     - "Vercel (Recommended)" — Next.js optimized, auto deploy
     - "AWS (EC2/ECS)" — Self-managed infrastructure
     - "Cloudflare Pages" — Edge-based deployment
     - "Enter manually" — Other deployment environment

4. **UI Styling**
   - header: "Styling"
   - question: "How will you handle UI styling?"
   - options:
     - "Tailwind CSS + shadcn/ui (Recommended)" — Utility CSS + component library
     - "Tailwind CSS" — Utility-based CSS only
     - "Material UI (MUI)" — Google Material Design components
     - "Enter manually" — Other UI framework

## Round 3: Detailed Feature Requirements

Ask about detailed features based on Round 1 responses. Question content is dynamically composed based on project type.

Example questions (select based on project type):

1. **Notification/Messaging System**
   - header: "Notifications"
   - question: "Do you need notification/messaging features? (Multiple selection allowed)"
   - multiSelect: true
   - options:
     - "KakaoTalk Notifications" — Kakao business messages
     - "Email Notifications" — Email sending
     - "SMS" — Text message sending
     - "Not needed" — No notification features required

2. **External Service Integration**
   - header: "Integrations"
   - question: "Are there external services you need to integrate? (Multiple selection allowed)"
   - multiSelect: true
   - options:
     - "Google Calendar" — Calendar/schedule integration
     - "Google Sheets" — Spreadsheet data integration
     - "Payments (Toss Payments/KakaoPay)" — Online payments
     - "Not needed" — No external integrations required

3. **Design Concept**
   - header: "Design"
   - question: "Do you have a design concept or reference site?"
   - options:
     - "Minimal/Simple" — Clean, no-frills design
     - "Modern/Trendy" — Contemporary, polished design
     - "Have a reference site" — There's a benchmark site (enter manually)
     - "Designer will provide" — Design files from Figma/Zeplin etc.

4. **Additional Requirements**
   - header: "Other"
   - question: "Any additional features or special considerations? (Multiple selection allowed)"
   - multiSelect: true
   - options:
     - "Responsive (Mobile-first)" — Mobile optimization required
     - "Multi-language support" — Languages beyond primary needed
     - "SEO Optimization" — Search engine optimization needed
     - "Enter manually" — Other requirements

## Follow-up Questions (As Needed)

If user responses are unclear or additional information is needed, use AskUserQuestion in free form for follow-up.

Specifically, always ask follow-up when the following are unclear:
- Core business flow (e.g., state flow like booking → approval → confirmation)
- Accessible pages and feature scope per user type
- Data relationships (e.g., 1:N, N:M relationships)
- Special business rules or constraints
