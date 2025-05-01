---
title: "NextJs App Ensaf Academia"
author: med
categories: [dev]
tags: [NextJS , supabase, React ]
render_with_liquid: false
math: true

img_path: /images/ensafAcademia/
image:
  path:  /images/ensafAcademia/home.png
---

# ENSAF ACADEMIA

**A modern, collaborative academic platform**



**ENSAF ACADEMIA** is a modern, collaborative academic platform designed for students and professors at ENSAF. Built with Next.js, Supabase, and Drizzle ORM, it centralizes the management of academies, courses, members, student access, and certificates, offering advanced administration and statistical tools.

## ✨ Key Features

*   **Role-Based Access Control:** Distinct dashboards and permissions for Students, Professors, and Administrators.
*   **Authentication:** Secure user registration (institutional email validation), login, password reset, and session management via Supabase Auth.
*   **Academy Management:** Professors can create and manage public/private academies, add custom fields, and invite collaborators.
*   **Course Management:** Create, edit, and delete courses within academies, including descriptions, images (Supabase Storage), and categories.
*   **Access Request Workflow:** Students can request access to courses, with professors/admins managing approvals/rejections.
*   **Certificate Management:** Professors/Admins can validate student achievements and issue verifiable certificates.
*   **Customizable Profiles:** Extend user profiles with dynamic fields defined per academy.
*   **Statistics Dashboard:** Visual insights into platform usage, course enrollment, and certificate issuance (role-dependent).
*   **Email Notifications & Logging:** Track important emails (invitations, password resets, notifications) via a dedicated logging system.
*   **Admin Panel:** Global oversight of users, academies, courses, certificates, logs, and system configuration.
*   **Modern UI/UX:** Responsive interface built with Tailwind CSS, shadcn/ui, and animations powered by Framer Motion.

## 🚀 Tech Stack

**Key Technologies:** `Next.js 15` `TypeScript` `PostgreSQL` `Supabase` `Drizzle ORM` `Tailwind CSS` `React Query` `React Hook Form` `Zod` `Framer Motion`


* **Framework:** Next.js 15 (App Router)
* **Language:** TypeScript
* **Database:** PostgreSQL (via Supabase)
* **ORM:** Drizzle ORM
* **Authentication:** Supabase Auth
* **Storage:** Supabase Storage
* **Styling:** Tailwind CSS, shadcn/ui
* **UI/Animation:** React, Framer Motion
* **State Management/Fetching:** React Query (`@tanstack/react-query`)
* **Form Handling:** React Hook Form
* **Schema Validation:** Zod
* **UI Components:** Lucide React (Icons), Recharts (Charts), Sonner (Toasts), `react-day-picker`
* **Email:** Nodemailer (likely, based on logging features)
* **Caching:** `node-cache` (for server-side data like statistics)

## 🏗️ Architecture Overview

The application follows a modern web architecture leveraging Next.js features:

1.  **Client:** Interactive UI built with React and Server Components, styled with Tailwind CSS. Client-side logic for forms and interactions.
2.  **Server (Next.js):** Handles routing (App Router), Server Components rendering, Server Actions for mutations and data fetching, and API routes if needed.
3.  **Middleware:** Manages authentication checks, authorization (role-based routing), and session handling for protected routes.
4.  **Service Layer:** Encapsulated in Next.js Server Actions (`/services/actions/*`). Contains business logic, interacts with the database via Drizzle, and calls external services (like Supabase Auth/Storage).
5.  **Data Layer:** Drizzle ORM provides type-safe database access to the PostgreSQL database hosted on Supabase. Migrations manage schema changes.
6.  **External Services:**
    *   **Supabase:** Provides Authentication, Database hosting, and File Storage.
    *   **Email Service:** Handles sending transactional emails (e.g., verification, password reset).

## 🔄 Data Workflow Examples
![Desktop View](/images/ensafAcademia/flux.png){: width="589" height="972" }


Here's how data typically flows for key actions:

### User Registration:
* **Client:** User fills the registration form (`app/(auth)/register/page.tsx`).
* **Server Action:** `signup` action is called.
* **Supabase Auth:** Creates the user account, sends a verification email.
* **Server Action:** If Supabase Auth succeeds, inserts a corresponding record into the `profiles` table via Drizzle.
* **Client:** User receives feedback (e.g., check email for verification).

### User Login:
* **Client:** User submits login credentials (`app/(auth)/login/page.tsx`).
* **Server Action:** `login` action validates credentials against Supabase Auth.
* **Supabase Auth:** Verifies credentials, creates a session.
* **Next.js:** Sets a session cookie.
* **Middleware:** (`middleware.ts`) Checks for the session cookie on subsequent requests to protected routes.
* **Client:** Redirected to the appropriate dashboard (`/dashboard` or `/admin/dashboard`).

### Professor Creates an Academy:
* **Client:** Professor fills the academy creation form (`app/dashboard/academies/new/page.tsx` or similar).
* **Server Action:** `createAcademy` (`services/actions/academy-actions.ts`) is invoked.
* **Action Logic:** Verifies user role (Professor/Admin).
* **Drizzle:** Inserts a new record into the `academies` table. Inserts the creator into `academy_members`. Handles `academy_custom_fields` and `academy_field_mappings` if custom fields are provided.
* **Next.js:** `revalidatePath` might be called to update relevant cached pages.
* **Client:** Receives success/error feedback, potentially redirected.

### Professor Creates a Course:
* **Client:** Professor fills the course creation form within an academy context (`app/dashboard/academies/[id]/page.tsx` ).
* **Server Action:** `createCourse` (`services/actions/db-actions.ts`) is called.
* **Action Logic:** Verifies user is a member/creator of the academy or an admin.
* **Supabase Storage:** If an image is uploaded, it's sent to Supabase Storage via `uploadImageDemande` (`services/actions/image-actions.ts`).
* **Drizzle:** Inserts a new record into the `courses` table, linking it to the academy and creator. Inserts image URL into `course_images`.
* **Next.js:** `revalidatePath` is called for academy and course pages.
* **Client:** Receives feedback.

### Student Requests Course Access:
* **Client:** Student clicks "Request Access" on a course page (`app/courses/[id]/page.tsx` or similar).
* **Server Action:** `createAccessRequest` (`services/actions/db-actions.ts`) is triggered.
* **Action Logic:** Checks if a request already exists. Verifies user is logged in.
* **Drizzle:** Inserts a new record into the `access_requests` table with 'pending' status.
* **Next.js:** `revalidatePath` might be called for dashboard pages.
* **Client:** Receives feedback (request submitted).

### Professor Approves Access Request:
* **Client:** Professor views pending requests (e.g., in `app/dashboard/page.tsx` or admin dashboard) and clicks "Approve".
* **Server Action:** `updateAccessRequest` (`services/actions/db-actions.ts`) or similar action via React Query mutation (`AdminDashboard.tsx`) is called with the request ID and 'approved' status.
* **Action Logic:** Verifies the professor has permission (member/creator of the academy or admin).
* **Drizzle:** Updates the status of the corresponding record in the `access_requests` table.
* **Next.js:** `revalidatePath` is called for relevant dashboards.
* **Client:** UI updates to reflect the change (optimistic update + refetch via React Query).

### Student Uploads Certificate:
* **Client:** Student uploads a PDF certificate for an approved course access (`app/dashboard/page.tsx`).
* **Server Action:** `uploadCertif` (`services/actions/profile-actions.ts`) is called.
* **Action Logic:** Verifies user.
* **Supabase Storage:** Uploads the certificate PDF.
* **Drizzle:** Inserts a new record into the `certificates` table with the URL and `verified: false`. Updates the corresponding `access_requests` record with the new `certificateId`.
* **Next.js:** `revalidatePath` for the student dashboard.
* **Client:** Receives feedback, UI updates.

### Professor Verifies Certificate:
* **Client:** Professor/Admin views pending certificate verifications and clicks "Verify".
* **Server Action:** `UpadteVerificationCertificate` (`services/actions/db-actions.ts`) is called.
* **Action Logic:** Verifies user role/permissions.
* **Drizzle:** Updates the `verified` status to `true` in the `certificates` table.
* **Next.js:** `revalidatePath` for relevant dashboards/certificate pages.
* **Client:** Receives feedback, UI updates.

## 📁 Project Structure

```
ensaf-academia4/
├── app/                  # Next.js App Router: Contains all routes, pages, layouts, and API endpoints.
│   ├── (auth)/           # Routes related to authentication (login, register, password reset). Grouped layout.
│   ├── admin/            # Routes and components specific to the admin panel.
│   ├── api/              # API routes (e.g., for webhooks or specific serverless functions).
│   ├── dashboard/        # Routes for the main user dashboard (student/professor).
│   ├── layout.tsx        # Root layout component, wraps all pages.
│   └── page.tsx          # Homepage component.
├── components/           # Shared React components used across the application.
│   ├── admin/            # Components specifically used within the admin section.
│   ├── dashboard/        # Components used within the user dashboard.
│   └── ui/               # Base UI components, often generated by shadcn/ui (Button, Card, etc.).
├── lib/                  # Core utilities, libraries, and configuration files.
│   ├── db/               # Drizzle ORM setup, schema definitions, and migration files.
│   │   └── migrations/   # SQL migration files generated by Drizzle Kit.
│   ├── i18n/             # Internationalization setup (language context, translations).
│   └── utils.ts          # General utility functions used throughout the application.
├── services/             # Business logic layer, often containing server-side operations.
│   └── actions/          # Next.js Server Actions for data fetching, mutations, and backend logic.
├── public/               # Static assets accessible directly via URL (images, fonts, favicons).
├── styles/               # Global CSS styles and Tailwind CSS configuration.
├── .env.local            # Local environment variables (ignored by Git). Contains secrets like API keys.
├── drizzle.config.ts     # Configuration file for Drizzle Kit (ORM tooling).
├── next.config.mjs       # Configuration file for Next.js (build options, redirects, etc.).
├── package.json          # Project metadata and dependencies.
├── pnpm-lock.yaml        # Exact dependency versions managed by pnpm.
└── tsconfig.json         # TypeScript compiler configuration.
```

## ⚙️ Getting Started

### Prerequisites

*   Node.js (v18 or later recommended)
*   pnpm (Package manager)
*   Supabase Account & Project

### Installation

1.  Clone the repository:
    ```bash
    git clone <repository-url>
    cd ensaf-academia4
    ```
2.  Install dependencies using pnpm:
    ```bash
    pnpm install
    ```

### Environment Variables

1.  Create a `.env.local` file in the project root.
2.  Obtain your Supabase project URL and `anon` key from your Supabase project settings (API section).
3.  Add the following variables to `.env.local`:

    ```bash
    # Supabase
     DATABASE_URL= ##############
     SUPABASE_URL=##############
     SUPABASE_ANON_KEY=##############
     NEXT_PUBLIC_APP_URL=http://localhost:3000
     SUPABASE_SERVICE_ROLE_KEY=##############
    
     # Configuration email
     EMAIL_USER=##############
     EMAIL_PASS=##############
     RESET_API=##############
    
     NEXT_PUBLIC_RECAPTCHA_SITE_KEY=##############
     RECAPTCHA_SECRET=##############
    ```
    *Replace placeholders with your actual credentials.*

### Database Setup

1.  Ensure your `DATABASE_URL` in `.env.local` points to your Supabase PostgreSQL database.
2.  Apply database migrations using Drizzle Kit:
    ```bash
    pnpm drizzle:push
    ```
    *This command compares your Drizzle schema (`lib/db/schema.ts`) with the database and applies the necessary changes. For production, consider generating SQL migration files (`pnpm drizzle:generate`) and applying them manually or via a migration tool.*

## ▶️ Running the Application

Start the development server:

```bash
pnpm dev
```

The application should now be running on [http://localhost:3000](http://localhost:3000).

## 📜 Available Scripts

*   `pnpm dev`: Starts the development server.
*   `pnpm build`: Creates a production build of the application.
*   `pnpm start`: Starts the production server (requires `pnpm build` first).
*   `pnpm lint`: Lints the codebase using ESLint.
*   `pnpm drizzle:generate`: Generates SQL migration files based on schema changes.
*   `pnpm drizzle:push`: Pushes schema changes directly to the database (useful for development).
*   `pnpm drizzle:studio`: Opens Drizzle Studio to browse your database.

---

---

**ENSAF ACADEMIA - Documentation**  
*Last updated: May 1, 2025*

