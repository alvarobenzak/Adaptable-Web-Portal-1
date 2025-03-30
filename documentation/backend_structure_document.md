# Backend Structure Document

This document explains the backend setup for the akoreuserportal project in everyday language. It covers how the different backend components work together to provide a secure, scalable, and maintainable portal experience. Below are the key sections detailing the backend architecture, database management, API design, hosting, and more.

## 1. Backend Architecture

The backend of the akoreuserportal is designed to be modular, scalable, and easy to maintain. Key points include:

*   **Overall Structure:**

    *   The system is split into several independent components, such as user authentication, data storage, and business logic, which communicate securely with one another.
    *   The design is loosely coupled so that different pieces can be updated independently without causing widespread changes.

*   **Design Patterns and Frameworks:**

    *   **Server-side API routes:** Managed using Next.js APIs to handle operations like CSV parsing and data interactions.
    *   **Authentication Integration:** Uses Clerk for all sign-up, email verification, and session management. This offloads many security and user management tasks.
    *   **Database Interactions:** Supabase (which uses PostgreSQL) handles the storage of user profiles, facility data, and fuel shipment records.

*   **Supporting Scalability, Maintainability, and Performance:**

    *   **Scalability:** The decoupled design allows the backend to add more modules and scale database interactions as needed. The use of cloud services (Supabase and cloud hosting) ensures that resources can be increased during peak times.
    *   **Maintainability:** Modularity and well-defined components mean developers can easily update parts of the system. Clear separation between authentication (Clerk) and application data (Supabase) simplifies management.
    *   **Performance:** Optimized API endpoints, caching where necessary, and load balancing ensure quick responses to user requests while handling large amounts of data.

## 2. Database Management

The project uses a modern database solution and best practices for handling data:

*   **Database Technologies:**

    *   **Supabase (PostgreSQL):**

        *   *Type:* SQL database
        *   *Usage:* Stores user profiles, primary entity (facility) details, and fuel shipment records.

    *   **Data Separation:** Clerk handles the authentication data, while Supabase stores extended profile data and application-specific details.

*   **Data Structure, Storage, and Access:**

    *   User records and facility data are stored in structured tables where relationships are managed via foreign keys (e.g., using Clerk User ID as a link).
    *   Fuel shipment records are stored in a dedicated table with clear field definitions, matching the format required by the Massachusetts Department of Environmental Protection's Fuel Shipment Information Form.
    *   Applications can easily query data using SQL, and thanks to Supabase’s APIs, developers can access data both securely and efficiently.

## 3. Database Schema

Below is a human-readable overview of the database schema along with an example in SQL (PostgreSQL) format.

*   **Human-Readable Schema Overview:**

    *   **Users Table:**

        *   Stores extended user profiles with details such as first name, last name, email, phone, and address.
        *   Linked via Clerk User ID.

    *   **Primary Entity Table (Facilities):**

        *   Contains facility information including name, operating names, owner names, operator names, storage tank count, and address details.
        *   Associated with the user who registered the facility.

    *   **Fuel Shipments Table:**

        *   Holds CSV parsed data from fuel shipment uploads.
        *   References related user and facility information for data integrity.

    *   **Audit Trail Table:**

        *   Records any changes made to profiles or facility data, including timestamps and user actions.

*   **Example SQL Schema (PostgreSQL):**

/* Users Table */ CREATE TABLE users ( id SERIAL PRIMARY KEY, clerk_user_id VARCHAR(255) UNIQUE NOT NULL, first_name VARCHAR(100) NOT NULL, last_name VARCHAR(100) NOT NULL, email VARCHAR(255) UNIQUE NOT NULL, phone VARCHAR(20), address TEXT, city VARCHAR(100), state VARCHAR(50), zip_code VARCHAR(20), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP );

/* Facilities (Primary Entity) Table */ CREATE TABLE facilities ( id SERIAL PRIMARY KEY, clerk_user_id VARCHAR(255) NOT NULL, facility_name VARCHAR(255) NOT NULL, operating_names TEXT, owners_names TEXT, operators_names TEXT, number_of_heating_oil_tanks INTEGER, number_of_propane_tanks INTEGER, facility_address TEXT, city VARCHAR(100), state VARCHAR(50), zip_code VARCHAR(20), mailing_address TEXT, mailing_city VARCHAR(100), mailing_state VARCHAR(50), mailing_zip VARCHAR(20), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP );

/* Fuel Shipments Table */ CREATE TABLE fuel_shipments ( id SERIAL PRIMARY KEY, clerk_user_id VARCHAR(255) NOT NULL, facility_id INTEGER REFERENCES facilities(id), shipment_date DATE, month INTEGER, year INTEGER, data JSONB, -- Parsed CSV data stored as JSON created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP );

/* Audit Trail Table */ CREATE TABLE audit_trail ( id SERIAL PRIMARY KEY, clerk_user_id VARCHAR(255) NOT NULL, action TEXT NOT NULL, entity VARCHAR(50), entity_id INTEGER, timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP );

## 4. API Design and Endpoints

APIs are essential for communication between the front end and the back end. The design is centered around simplicity and security:

*   **API Style:**

    *   RESTful APIs are used for standard operations (GET, POST, PUT, DELETE) managed by Next.js server-side routes.

*   **Key Endpoints and Purpose:**

    *   **/api/register:** Handles multi-step registration. Initiates user data collection, email verification, and final account setup in Supabase.
    *   **/api/auth:** Manages login, logout, and password reset (leveraging Clerk's built-in functionalities).
    *   **/api/profile:** Allows viewing and editing user profiles stored in Supabase, ensuring updates are linked to the Clerk user ID.
    *   **/api/facilities:** Provides data for facility details (read-only view initially) and supports future updates or swapping of users by staff.
    *   **/api/fuel-shipments:** Manages CSV uploads, validates the content against MASSDEP specifications, saves parsed data into Supabase, and offers endpoints for downloading or template retrieval.
    *   **/api/config:** Returns configuration details (from environment variables or JSON) to the frontend for dynamic feature toggling.

## 5. Hosting Solutions

The backend hosting is set up to ensure reliability, scalability, and cost effectiveness.

*   **Cloud Providers & Solutions:**

    *   **Supabase:** Cloud-based solution for hosting the PostgreSQL database and related backend APIs.
    *   **Next.js API routes:** Likely deployed on a cloud platform like Vercel or Netlify, which integrate seamlessly with Next.js.
    *   **Other Cloud Services:** Monitoring and logging tools are integrated into the cloud setup for better oversight.

*   **Benefits of the Chosen Hosting:**

    *   **Reliability:** Cloud providers offer robust uptime and managed services that ensure the backend is always available.
    *   **Scalability:** Resources can be scaled automatically to handle increased traffic.
    *   **Cost-Effectiveness:** Pay-as-you-go models mean that costs are aligned with usage, reducing upfront investments.

## 6. Infrastructure Components

The system uses several infrastructure components to bolster performance and ensure a smooth user experience:

*   **Load Balancers:**

    *   Evenly distribute incoming API requests to avoid overloading any single server instance.

*   **Caching Mechanisms:**

    *   Utilize caching to store frequently accessed data, reducing database load and speeding up response times.

*   **Content Delivery Networks (CDNs):**

    *   Static files and assets are served via a CDN to ensure quick load times regardless of the user's location.

*   **Additional Tools:**

    *   **Logging Tools:** Winston for server-side logging, with Sentry or LogRocket for error tracking and performance monitoring.
    *   **Third-Party Integrations:** Notification services (like Twilio or SendGrid) and analytics (Google Analytics) help monitor user engagement.

## 7. Security Measures

Security is a top priority with multiple layers to protect user data and ensure regulatory compliance:

*   **Authentication and Authorization:**

    *   **Clerk:** Manages secure email-only login, multi-step registration, and role-based access (user, admin, and staff variations).
    *   **User Roles:** Managed with Clerk metadata and linked Supabase tables to define permissions and access rights.

*   **Data Encryption:**

    *   All data is encrypted during transit (using HTTPS) and at rest (leveraging Supabase’s encryption capabilities).

*   **Validation and Rate Limiting:**

    *   Both client-side and server-side validations are implemented to sanitize and check data inputs (especially for CSV uploads).
    *   Rate limiting is applied to critical endpoints such as user registration to prevent abuse.

*   **Additional Practices:**

    *   Regular security audits and updated SSL/TLS certificates help maintain robust security standards.

## 8. Monitoring and Maintenance

Keeping the backend running smoothly is ensured by proactive monitoring and routine maintenance:

*   **Monitoring Tools:**

    *   **Winston:** For detailed server-side logging.
    *   **Sentry/LogRocket:** For real-time error tracking and alerting about issues in production.
    *   Regular performance audits via Supabase and host-provider dashboards.

*   **Maintenance Strategies:**

    *   Scheduled updates to dependencies and libraries.
    *   Backup routines for the PostgreSQL database to ensure data safety.
    *   Routine security checks to address vulnerabilities promptly.

## 9. Conclusion and Overall Backend Summary

The backend of the akoreuserportal is built with clear separation of concerns, using state-of-the-art tools and best practices:

*   **Modular and Scalable:** Designed to easily accommodate new features and scale with user demands.
*   **Secure by Design:** Uses modern protocols for encryption, validation, and role-based access, ensuring the highest levels of data protection.
*   **User-Focused Integration:** The system smoothly integrates Clerk for authentication with Supabase for extended user and application data, ensuring consistency across the platform.

Unique aspects include the flexible configuration mechanism (using both environment variables and JSON files) which allows the portal to adapt quickly to new requirements, as well as a robust audit trail, error handling, and logging system that guarantees reliability and transparency.

This backend structure ensures that the akoreuserportal not only meets the current requirements for the FSTS application but is also well-prepared to expand into other configurable modules in the future.

By following this document, anyone—whether technical or not—should be able to understand how the backend components work together to support a secure and efficient user portal.
