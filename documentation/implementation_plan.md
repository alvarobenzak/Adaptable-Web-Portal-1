# Implementation plan

Below is an initial step-by-step implementation plan for the akoreuserportal project, based on the summarized requirements. This plan is organized in 5 phases: Environment Setup, Frontend Development, Backend Development, Integration, and Deployment. Each step includes explicit actions and references to the relevant requirements (e.g., PRD, Tech Stack) to ensure compliance.

## Phase 1: Environment Setup

1.  **Prevalidation:** Before starting, check if the current directory already contains a project (e.g., package.json, .env) to avoid reinitialization. (*Project requirements: General Prevalidation*)

2.  **Verify Node.js Installation:** Check if Node.js v20.2.1 is installed. If not, install Node.js v20.2.1 per core tool instructions.

    *   **Validation:** Run `node -v` and verify it prints v20.2.1. (*Tech Stack: Core Tools*)

3.  **Install Next.js 15: Initialize a new Next.js project with Next.js version 15 (this version is chosen for compatibility with current AI coding tools and LLM models). Action: Run **`npx create-next-app@15 akoreuserportal --ts`** to set up a TypeScript**`project. (Tech Stack: Frontend, Next.js 15)`

4.  **Configure Tailwind CSS & Shadcn/ui:** Install and set up Tailwind CSS along with Shadcn/ui and your chosen icons (Shadcn Icons or Lucide React) inside the newly created project.

    *   **Action:** Follow the Tailwind CSS installation guide for Next.js and integrate Shadcn/ui components. (*Design Preferences: Shadcn/ui aesthetics*)

5.  **Set Up Environment Variables:** Create a dedicated environment configuration file (e.g., `.env.local`) containing high-level settings, feature flags (stripe, AI disabled initially), and other secrets required (e.g., Clerk API keys, Supabase credentials).

    *   **Validation:** Confirm that the file contains variables such as `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY`, `SUPABASE_URL`, and `SUPABASE_ANON_KEY`. (*Configuration & Extensibility*)

6.  **MCP Setup (for Supabase with Cursor):**

    *   **Prevalidation:** Check if a `.cursor` directory exists in the project root. If not, create it.
    *   **Action:** Create a file at `.cursor/mcp.json` (if it doesn’t already exist).
    *   **Insert Configuration:** For macOS, add:

7.  `{ "mcpServers": { "supabase": { "command": "npx", "args": ["-y", "@modelcontextprotocol/server-postgres", "<connection-string>"] } } } `For Windows, add:

8.  `{ "mcpServers": { "supabase": { "command": "cmd", "args": ["/c", "npx", "-y", "@modelcontextprotocol/server-postgres", "<connection-string>"] } } }`

    *   **Display the Connection String Link:** Instruct the user to get the connection string from [Supabase MCP docs](https://supabase.com/docs/guides/getting-started/mcp#connect-to-supabase-using-mcp). Once provided, replace `<connection-string>` accordingly. (*Tech Stack: Supabase, MCP configuration*)
    *   **Validation:** Navigate to Settings/MCP in Cursor and confirm the server status is green active.

## Phase 2: Frontend Development

1.  **Authentication Pages Setup:** Create pages for login and registration.

    *   **Action:** Create `/pages/login.tsx` and `/pages/register.tsx` to integrate with Clerk’s email-only authentication and multi-step registration flows. (*Core Features: Authentication & Authorization, Clerk integration*)

2.  **Implement Multi-step Registration Flow:**

    *   **Step 1:** Create a form in `/components/RegistrationStep1.tsx` to collect user details and Primary Entity (e.g. Fuel Storage Facility) data per FSTS requirements.
    *   **Step 2:** Implement an email verification page in `/pages/verify-email.tsx` that leverages Clerk’s verification process.
    *   **Step 3:** Once verified, process activation and account linking by triggering Clerk events and then calling backend endpoints for profile creation in Supabase. (*Core Features: Registration Steps with Clerk and Supabase*)

3.  **Sidebar & Dashboard UI:**

    *   **Action:** Develop a sidebar-layout dashboard component in `/components/DashboardLayout.tsx` using Shadcn/ui components with a professional, minimalistic style with green accents (MASSDEP branding). (*Design Preferences: Professional, Shadcn/ui, MASSDEP green accent*)

4.  **User Profile & Primary Entity Screen:**

    *   **Action:** Create a page `/pages/dashboard/profile.tsx` for managing user profiles, including Designated Representative information and view-only primary entity (facility) details. (*Core Features: Unified User Dashboard*)

5.  **CSV Grid Interface for FSTS Module:**

    *   **Action:** Build a grid view component `/components/FSTSCsvGrid.tsx` that includes Month/Year/Facility selectors, “Upload CSV”, “Download Data”, and “Download Template” buttons.
    *   **Validation:** Verify UI responsiveness and run initial component tests. (*Core Features: Application-Specific Module for FSTS, CSV upload interface*)

6.  **Mobile Responsiveness & Accessibility:**

    *   **Action:** Ensure all pages use responsive layouts and include proper WAI-ARIA labels for accessibility. (*Design Preferences: Mobile responsive, WAI-ARIA standards*)

## Phase 3: Backend Development

1.  **Supabase Database Setup:**

    *   **Action:** Within your Supabase project, configure the PostgreSQL database with required tables. Specifically create:

        *   A `users` table (if not managed exclusively by Clerk).
        *   A `user_profiles` table to store profile extension data with a Clerk User ID foreign key.
        *   A `fuel_shipments` table with columns that match the CSV headers from the Massachusetts DEP Fuel Shipment Information Form (e.g., 'Propane or oil?', 'Dyed (Y/N)', 'Biofuel %', etc.).

    *   **Validation:** Verify tables and relations in the Supabase dashboard. (*Core Features: Data storage in Supabase, CSV headers alignment*)

2.  **Create API Endpoint for CSV Upload:**

    *   **Action:** Implement a Next.js API route at `/pages/api/v1/csv/upload.ts` that:

        *   Accepts CSV files,
        *   Parses and validates CSV data according to Massachusetts DEP rules (e.g., required, recommended, optional fields),
        *   Returns specific error feedback if validations fail.

    *   **Validation:** Test endpoint using a tool like Postman or cURL with CSV sample data. (*Core Features: CSV upload and validation*)

3.  **User Profile Extension Endpoint:**

    *   **Action:** Develop an API route at `/pages/api/v1/user/profile.ts` responsible for creating/updating user profile extensions in Supabase linked with Clerk User IDs.
    *   **Validation:** Verify with test requests and check Supabase for correct data records. (*Core Features: Clerk + Supabase integration*)

4.  **Logging & Error Handling:**

    *   **Action:** Integrate logging middleware (using Winston, for example) in API routes to capture errors and log critical events.
    *   **Validation:** Trigger error scenarios and check logs for proper recording. (*Error Handling & Logging*)

5.  **Security Measures:**

    *   **Action:** Ensure email verification endpoints enforce unique, time-sensitive tokens, and apply rate limiting middleware on registration endpoints. Also, implement input sanitization for CSV data in the API endpoint. (*Core Features: Security & Data Validation*)

## Phase 4: Integration

1.  **Integrate Clerk with Frontend:**

    *   **Action:** Use Clerk’s React SDK to connect `/pages/login.tsx` and `/pages/register.tsx` with Clerk’s authentication methods. (*Core Features: Authentication & Authorization*)
    *   **Validation:** Test login and registration flows in the browser.

2.  **Connect Frontend CSV Upload to Backend:**

    *   **Action:** In `/components/FSTSCsvGrid.tsx`, add client-side integration (using axios or fetch) to send CSV files to `/api/v1/csv/upload`.
    *   **Validation:** Upload a test CSV and verify the backend response.

3.  **Synchronize User Data Across Systems:**

    *   **Action:** After registration, add logic to trigger backend API call (to `/api/v1/user/profile.ts`) for creating the user profile extension in Supabase using the Clerk User ID. (*Core Features: Data Separation between Clerk and Supabase*)
    *   **Validation:** Confirm the new profile record in Supabase after user registration.

4.  **Error & Notification Handling:**

    *   **Action:** Implement client-side error handling (try-catch blocks) and display notifications using a consistent messaging system, integrating with Clerk’s built-in email notification functionalities. (*Error Handling & Logging, Security & Data Validation*)

## Phase 5: Deployment

1.  **Vercel Deployment for Next.js Frontend:**

    *   **Action:** Configure and deploy the Next.js app to Vercel. Ensure environment variables are correctly added in the Vercel dashboard.
    *   **Validation:** Confirm the live URL is accessible and all pages load as expected. (*Deployment: Cloud setup*)

2.  **Supabase MCP Deployment:**

    *   **Action:** Use the configured .cursor MCP settings to deploy the Supabase MCP server. Check the MCP interface to verify connection is active.
    *   **Validation:** In the MCP Settings, confirm the green active status and test database connections.

3.  **CI/CD Setup:**

    *   **Action:** Implement CI/CD pipelines (using GitHub Actions or similar) to automate tests and deployments on pushes to the main branch.
    *   **Validation:** Run a test commit and verify pipeline execution and deployment logs. (*Deployment: CI/CD integration*)

4.  **Final End-to-End Testing:**

    *   **Action:** Run both frontend and backend tests (e.g., using Cypress for E2E tests and Jest for unit tests) to confirm overall system integrity including authentication, CSV upload, and profile management.
    *   **Validation:** All tests pass with 100% coverage on critical components.

This initial plan covers all key project requirements and technical details as outlined. Please review and let me know if any section needs further clarification or if there are additional requirements to incorporate before proceeding with implementation.
