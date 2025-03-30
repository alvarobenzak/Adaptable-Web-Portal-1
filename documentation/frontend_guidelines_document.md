# Frontend Guideline Document

This document outlines how we have set up and designed the frontend for the Akore User Portal. It explains our architecture, design principles, styling choices, component organization, and more. It’s written in plain language so that everyone, even those with a non-technical background, can understand the overall picture.

## 1. Frontend Architecture

Our portal is built with a focus on scalability, maintainability, and performance. Here’s a rundown of the key points:

*   **Framework and Runtime:** We use Next.js. It is built on top of React and TypeScript, running on a Node.js runtime. This combination allows us to create a fast, server-rendered web application that can also handle static pages.
*   **Authentication & Authorization:** For user login, registration, and session management, we utilize Clerk. It handles email-based sign-in, password resets, and verification, while also linking user profiles with backend data stored in Supabase.
*   **Data Separation:** We keep our user authentication (using Clerk) separate from our application data (managed via Supabase). The two connect through the Clerk User ID, ensuring security and clarity in data management.
*   **API Routes and Server-Side Logic:** Using Next.js’s API routes, we implement server-side processes like CSV parsing and interactions with Supabase. This means complex operations can be handled securely on the server instead of in the browser.
*   **Modularity:** Our components are loosely coupled, meaning each piece can work independently and is easily reusable. This supports faster scaling and easier maintenance in the future.

## 2. Design Principles

Our design is grounded in several key principles to ensure a modern, user-friendly experience:

*   **Usability:** We have created a clean, minimalistic, and professional interface. The goal is to make sure everything is intuitive and easy to navigate, with clear calls to action and thoughtful feedback (like friendly error messages).
*   **Accessibility:** Our portal follows WAI-ARIA guidelines to ensure that it is accessible to all users, including those relying on assistive technologies.
*   **Responsiveness:** Using Tailwind CSS, our design automatically adjusts across devices. Whether on a desktop or mobile, the layout, especially with a sidebar navigation, will adapt gracefully.
*   **Security Focus:** Email verification, rate limiting, input validation, and considerations for future MFA (multi-factor authentication) are built-in to keep our users and their data safe.

## 3. Styling and Theming

Our project has a modern, professional, and clean aesthetic, enhanced by a few key choices:

*   **CSS Framework:** Tailwind CSS is central to our styling. It provides responsive utilities and a customizable design system that fits perfectly with our design needs.

*   **Component Library:** Shadcn/ui is used for pre-styled components, ensuring consistency and saving time during development.

*   **Icons:** We use Shadcn Icons (or Lucide React as an alternative) to maintain visual consistency throughout the application.

*   **Methodology and Pre-Processors:** We follow utility-first principles with Tailwind, keeping our CSS modular and avoiding clutter. If we need to extend styling, we do so using Tailwind’s configuration files and occasionally SASS for custom implementations.

*   **Theming:** Branding is achieved with a neutral color palette accented by green tones (aligned with State Department Standard and MASSDEP). The visual style is clean and flat, mixing hints of modern material design with a touch of glassmorphism for components such as cards or overlays.

*   **Color Palette & Fonts:**

    *   **Primary Colors:** Neutrals (gray shades) with green accents.
    *   **Secondary Colors:** Whites and light neutrals for backgrounds.
    *   **Accent Colors:** A specific shade of green (for state standards) that is consistent throughout the application.
    *   **Font:** We use a clean, sans-serif font that complements the modern and professional feel. If not explicitly provided, fonts like Inter or Helvetica are ideal choices.

## 4. Component Structure

*   **Organization:** All frontend components are designed to be modular and reusable. This means each component (like the login form, data grid, or sidebar) is self-contained.
*   **Component Approach:** The portal employs a component-based architecture. This means that common UI elements are built as standalone components and then reused across the application. This helps maintain consistency and simplifies future updates.
*   **Directory Layout:** The project, inspired by the CodeGuide Starter Pro kit, organizes components into logical folders based on functionality (e.g., authentication, dashboard, settings, and application-specific modules like FSTS).

## 5. State Management

*   **Local State:** Within individual components or features, local component state (using React hooks like useState) is used for immediate UI updates.
*   **Global State:** For sharing state across components, we rely on built-in patterns in Next.js and React Context when needed. For data that needs to be shared widely or updated globally (like user information), this approach minimizes the complexity that comes with heavier libraries.
*   **API-Managed Data:** Data comes from API routes that interact with Supabase and Clerk. This separation of concerns ensures that real-time data and authentication states are updated properly.

## 6. Routing and Navigation

*   **Routing:** All navigation is managed through Next.js’s file-based routing system. The routes are well structured, supporting server-side rendering where needed, and easy-to-read for developers.
*   **Navigation Structure:** The user interface is built around a sidebar layout. This sidebar allows users to easily navigate between the dashboard, profile management, and application-specific modules such as Fuel Shipment Data Management.
*   **Dynamic Paths & Protected Routes:** Protected routes ensure users who aren’t authenticated don’t see data they shouldn’t. Routing also handles dynamic paths, such as those for unique entities or user profiles.

## 7. Performance Optimization

*   **Lazy Loading & Code Splitting:** We use strategies like lazy loading non-critical components and functions, as well as code splitting, to make sure the portal loads quickly even if feature sets grow.
*   **Asset Optimization:** Images, fonts, and other assets are optimized to ensure they load fast without compromising on quality.
*   **Server-Side Rendering:** Leveraging Next.js capabilities, parts of the site are pre-rendered for faster initial page loads.
*   **Caching & Minification:** We adopt caching strategies on both the client and server, and our assets, especially JavaScript and CSS, are minified to speed up downloads.

## 8. Testing and Quality Assurance

*   **Unit Testing:** Each UI component is unit tested to ensure it functions as expected. Tools like Jest and React Testing Library may be used to simulate user interactions and validate component behavior.
*   **Integration Testing:** Components working together are tested to ensure that the flow (such as registration and CSV upload validations) operates smoothly across the different modules.
*   **End-to-End Testing:** Automated tests simulate user journeys (i.e., signing in, navigating the dashboard, CSV uploads), using tools such as Cypress to ensure that the entire application meets our quality standards.
*   **Error Handling:** Both the frontend and backend have robust error handling. Frontend errors are caught and displayed via user-friendly messages, with client-side logs sent to external services such as Sentry or LogRocket.

## 9. Conclusion and Overall Frontend Summary

In summary, our frontend guidelines emphasize a clean, modular, and secure approach that aligns with the overall project goals of building a configurable and reusable portal. Key aspects include:

*   A robust Next.js and React-based architecture ensuring both server-side and client-side performance.
*   Thoughtful design principles focusing on usability, accessibility, and responsiveness.
*   A consistent visual experience provided through Tailwind CSS, Shadcn/ui components, and a carefully chosen color palette and font strategy.
*   Flexible and reusable component structures that keep development agile, along with clear strategies for state management.
*   A well-planned routing system and performance optimizations to deliver a fast and reliable user experience.
*   Rigorous testing practices to guarantee that every part of the frontend is both robust and secure.

This well-thought-out setup not only meets the current needs of our Fuel Shipment Tracking System but is also designed to scale and adapt to future Akore software applications. Every decision, from component organization to error handling and styling, is made to ensure that our portal is both highly functional and visually appealing.

We hope this document provides a clear picture of our frontend setup for the Akore User Portal and feel confident in both our implementation and future direction.
