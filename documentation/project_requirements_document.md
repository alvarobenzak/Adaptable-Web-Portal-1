# Project Requirements Document for akoreuserportal

## 1. Project Overview

The akoreuserportal is a configurable and reusable web-based user portal designed as the central point for authentication, registration, and basic interactions for various Akore software applications. Its initial implementation is focused on the Fuel Shipment Tracking System (FSTS), where users can sign up, verify their identity, and manage their profile and application-specific data through an intuitive dashboard. This centralized portal offers a secure, unified experience, minimizes redundant development efforts for future projects, and ensures consistency across all implementations.

The portal is being built to simplify the user onboarding process and provide a single touchpoint for essential interactions, from secure login to application-specific tasks like CSV fuel shipment data management. The key objectives include high security (using Clerk for authentication and Supabase for data storage), flexibility through configurable settings (leveraging environment variables and JSON files), and an extensible architecture that accommodates future modules or third-party integrations. Success in this project will be measured by a seamless, secure user experience, scalability for future software products, and a maintainable, modular codebase.

## 2. In-Scope vs. Out-of-Scope

**In-Scope:**

*   Secure user authentication and authorization using Clerk.

    *   Email-only login interface and password reset flows.
    *   Multi-step registration process that collects both user profile and Primary Entity (FSTS: heating fuel storage facility) data.
    *   Email verification and password creation steps.

*   User roles and permissions management utilizing Clerk metadata and Supabase.

*   A unified dashboard that allows users to:

    *   View and update their Designated Representative information.
    *   View (and review) associated Primary Entity data.
    *   Access application-specific modules.

*   FSTS-specific module featuring:

    *   A grid-style interface for fuel shipment data.
    *   CSV upload functionality with validation (required, recommended, and optional fields).
    *   CSV download and template generation.

*   Configurable system architecture:

    *   Use of environment variables (in a dedicated file) for high-level settings/feature flags.
    *   JSON configuration files to define specific fields for registration modules and application-specific modules.

*   Mobile responsiveness and accessibility following WAI-ARIA guidelines.

*   Comprehensive error handling, logging, and an audit trail for capturing all user changes.

*   Modular and extensible design that allows the portal to be cloned/adapted for future applications (e.g., enabling Stripe, AI, etc.).

**Out-of-Scope:**

*   Integration of Stripe and AI features for the FSTS configuration (they are to be architected but remain disabled/code removed for the current version).
*   Editing of Primary Entity information on the dashboard (view only for now).
*   Advanced third-party integrations beyond the basic notification (e.g., Twilio, SendGrid, etc.) unless required in subsequent iterations.
*   Multi-factor authentication (MFA), although may be suggested for future security improvements.
*   In-depth custom reporting or analytics dashboards beyond basic CSV interactions and audit logging.
*   Extensive customizations to the UI beyond the clean, minimalistic, state department standard interface with green accents.

## 3. User Flow

A typical user journey begins with the registration process. A new user lands on the registration page where they enter basic personal information along with details for a Primary Entity. For FSTS, the registration includes detailed fields for both the designated representative (such as first name, last name, email, phone, address, etc.) and the heating fuel storage facility (including facility name, operating names, owner/operator details, tank numbers, and various address fields). Upon completing the initial data collection, the system prompts the user to verify their email via a time-sensitive token.

After email verification, the user proceeds to create a secure password following best practices enforced by Clerk. Once the password is set, the account is activated and the data collected gets stored in Supabase, linked via the Clerk User ID. The user is then redirected to a unified dashboard that provides easy navigation via a sidebar layout. Here, users can access their profile details, view associated facility information, and manage fuel shipment data through a dedicated, configurable module. This dashboard design also incorporates settings, notification preferences, and an audit trail feature to maintain transparency for all user activities.

## 4. Core Features

*   **Secure User Authentication & Authorization:**

    *   Email-only login interface.
    *   Password reset and email verification via Clerk.
    *   Multi-step registration process collecting both user and Primary Entity details.
    *   Role management using Clerk metadata and Supabase tables.
    *   Ability for staff or staff-admin to swap user accounts associated with a facility.

*   **Unified User Dashboard:**

    *   Display and update personal profile (Designated Representative info).
    *   View associated Primary Entity (e.g., facility details for FSTS).
    *   Standard settings including notification toggles and audit trail access.

*   **FSTS Application-Specific Module:**

    *   Grid-style interface for managing fuel shipment data.
    *   Dropdown selectors for Month, Year, and Facility.
    *   CSV file upload with robust validation against the Massachusetts Department of Environmental Protection’s Fuel Shipment Information Form.
    *   Buttons/links for downloading CSV data and a blank CSV template.
    *   Parsing and storage of CSV data into Supabase tables (e.g., `fuel_shipments`).

*   **Configurable & Extensible Architecture:**

    *   Environment variables (stored in a dedicated file) for high-level configurations and feature toggles.
    *   JSON configuration files to specify the fields, validation, and module structure for different software.
    *   Modular code structure to easily clone, enable, or disable components like Stripe and AI integrations.

*   **Enhanced Security, Error Handling & Logging:**

    *   Client-side & server-side validation (try-catch blocks, middleware).
    *   Logging via libraries such as Winston with integration to external monitoring services like Sentry.
    *   Comprehensive audit trail feature that records all user profile changes and data modifications.

*   **Mobile Responsiveness & Accessible Design:**

    *   Responsive layout using Tailwind CSS to support mobile and desktop devices.
    *   Compliance with WAI-ARIA guidelines for keyboard navigation, screen reader compatibility, and sufficient contrast.

## 5. Tech Stack & Tools

*   **Frontend:**

    *   Next.js (React-based framework) with TypeScript.
    *   Tailwind CSS for styling and responsive design.
    *   Shadcn/ui and Shadcn Icons (or Lucide React via Shadcn) for UI components and icons.

*   **Backend & Storage:**

    *   Supabase (PostgreSQL) for storing user profiles, facility data, fuel shipment data, and audit trails.
    *   Next.js API routes for server-side logic (such as CSV parsing and secure data operations).

*   **Authentication & Authorization:**

    *   Clerk for handling user sign-up, login, email verification, password resets, and role management.

*   **Configuration:**

    *   Environment variables (kept in a dedicated file) for high-level settings and feature toggles.
    *   JSON configuration files for defining software-specific registration fields and module structures.

*   **Additional Tools:**

    *   Cursor (advanced AI-powered IDE suggestions).
    *   V0 by Vercel (AI-powered frontend component builder).
    *   Bolt (quick project setup and scaffolding tool).
    *   Claude 3.7 Sonnet (for intelligent hybrid reasoning, if any backend AI integration is needed).

## 6. Non-Functional Requirements

*   **Performance:**

    *   Fast load times, with API response times ideally under 200ms for interactive sessions.
    *   Optimization for both desktop and mobile devices using Tailwind CSS.

*   **Security:**

    *   Robust data encryption at rest and in transit.
    *   Client-side and server-side data validation and sanitation to prevent injections.
    *   Use of unique, time-sensitive tokens for email verification to mitigate replay attacks.
    *   Rate limiting on registration and authentication endpoints to prevent brute force attacks.

*   **Compliance:**

    *   Adhere to industry best practices and WAI-ARIA guidelines for accessibility.
    *   Ensure all PII is stored securely with appropriate encryption and auditing.

*   **Usability:**

    *   Clean, professional, and minimalistic interface with clear instructions and error messaging.
    *   Mobile responsiveness and accessible design to cater to all users.
    *   Intuitive navigation with a sidebar layout and clear call-to-action buttons.

## 7. Constraints & Assumptions

*   **Constraints:**

    *   Reliance on Clerk for authentication means the system depends on its uptime and API rate limits.
    *   Supabase is used for the backend data store; any changes to its structure or API will require coordinated updates.
    *   The CSV upload mechanism expects a specific format based on the Massachusetts Department of Environmental Protection’s form. This may require future adjustments as the form evolves.
    *   Future features (like Stripe or AI integrations) are not part of the initial implementation and should be architecturally isolated.

*   **Assumptions:**

    *   The registration process is designed to be highly flexible, taking a list of fields from configuration files to easily swap in new fields for different applications.
    *   User roles will be managed primarily via Clerk metadata with additional role details stored in Supabase if needed.
    *   Error handling and logging are critical to capturing both user-facing and debug-level information, so external logging services will be integrated.
    *   The modular architecture will allow seamless cloning and future adaptations without major codebase changes.
    *   The development environment will support Next.js, TypeScript, and the outlined tech stack without major integration issues.

## 8. Known Issues & Potential Pitfalls

*   **API Rate Limits and Dependence on External Services:**

    *   Clerk and Supabase both have API rate limits; plan for caching strategies and load balancing to avoid disruptions during high traffic.
    *   Mitigation: Monitor API usage closely and use retries/backoff strategies in API calls.

*   **CSV Data Parsing and Validation:**

    *   The CSV format may change over time, requiring updates to the parsing and validation logic.
    *   Mitigation: Design CSV processing code to be modular so that validation rules can be updated easily through configuration files without major code changes.

*   **Configuration Management Complexity:**

    *   Managing both environment variables and JSON configuration files may lead to synchronization issues.
    *   Mitigation: Maintain detailed documentation for configuration structure and implement configuration loading checks at startup.

*   **Scalability of Role Management:**

    *   As new roles (e.g., admin, designee) are added, ensuring consistent and secure role-based access control across both Clerk and Supabase may be challenging.
    *   Mitigation: Utilize middleware and centralized functions to enforce role checks and keep role logic in sync between the frontend and backend.

*   **Error Handling and Logging Robustness:**

    *   Capturing comprehensive logs (including the audit trail feature) may affect performance if not segmented properly.
    *   Mitigation: Use asynchronous logging and ensure that logging services do not introduce excessive latency, while keeping audit trail data secure and compliant.

This document serves as the main reference for further technical documents, ensuring that each subsequent phase—from Tech Stack to Frontend Guidelines and Backend Structures—has a precise, clear roadmap to follow.
