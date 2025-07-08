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
* **Server Action:** `createAcademy` is invoked.
* **Action Logic:** Verifies user role (Professor/Admin).

* **Next.js:** `revalidatePath` might be called to update relevant cached pages.
* **Client:** Receives success/error feedback, potentially redirected.

### Professor Creates a Course:
* **Client:** Professor fills the course creation form within an academy context (`app/dashboard/academies/[id]/page.tsx` ).
* **Server Action:** `createCourse` is called.
* **Action Logic:** Verifies user is a member/creator of the academy or an admin.
* **Supabase Storage:** If an image is uploaded, it's sent to Supabase Storage via `uploadImageDemande` (`services/actions/image-actions.ts`).
* **Next.js:** `revalidatePath` is called for academy and course pages.
* **Client:** Receives feedback.

### Student Requests Course Access:
* **Client:** Student clicks "Request Access" on a course page (`app/courses/[id]/page.tsx` or similar).
* **Server Action:** `createAccessRequest` is triggered.
* **Action Logic:** Checks if a request already exists. Verifies user is logged in.
* **Next.js:** `revalidatePath` might be called for dashboard pages.
* **Client:** Receives feedback (request submitted).

### Professor Approves Access Request:
* **Client:** Professor views pending requests (e.g., in `app/dashboard/page.tsx` or admin dashboard) and clicks "Approve".
* **Server Action:** `updateAccessRequest` or similar action via React Query mutation (`AdminDashboard.tsx`) is called with the request ID and 'approved' status.
* **Action Logic:** Verifies the professor has permission (member/creator of the academy or admin).
* **Next.js:** `revalidatePath` is called for relevant dashboards.
* **Client:** UI updates to reflect the change (optimistic update + refetch via React Query).

### Student Uploads Certificate:
* **Client:** Student uploads a PDF certificate for an approved course access (`app/dashboard/page.tsx`).
* **Server Action:** `uploadCertif`  is called.
* **Action Logic:** Verifies user.
* **Supabase Storage:** Uploads the certificate PDF.

* **Next.js:** `revalidatePath` for the student dashboard.
* **Client:** Receives feedback, UI updates.

### Professor Verifies Certificate:
* **Client:** Professor/Admin views pending certificate verifications and clicks "Verify".
* **Server Action:** `UpadteVerificationCertificate`  is called.
* **Action Logic:** Verifies user role/permissions.
* **Next.js:** `revalidatePath` for relevant dashboards/certificate pages.
* **Client:** Receives feedback, UI updates.


**ENSAF ACADEMIA - Documentation**  
*Last updated: May 1, 2025*

