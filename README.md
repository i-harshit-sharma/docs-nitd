# Overview

NITD website is a **monorepo**, built using **Turborepo** and **PNPM Workspaces**. It separates the main website, department portals, and specific functional applications into distinct apps while sharing UI and configuration.

### Project Structure

The project is organized into `apps` (applications) and `packages` (shared libraries).

#### **Applications (`apps/`)**

* **`home_page`**: The primary public-facing website. It is the largest application and serves as the main entry point.
* **Department Portals**: Separate Next.js apps for each department:
  * `cse_department` (Computer Science)
  * `ece_department` (Electronics & Comm)
  * `ee_department` (Electrical)
  * `mae_department` (Mechanical)
  * `ce_department` (Civil)
  * `ashm_department` (Applied Sciences)
* **Functional Apps**:
  * `tnp`: Training & Placement Cell portal.
  * `convocation`: Dedicated app for convocation events.
  * `faculty_profile`: Faculty profile management/display system.

#### **Shared Packages (`packages/`)**

* **`ui`**: Shared UI component library to ensure design consistency across all apps.
* **`lib`**: Common utility functions and logic.
* **`eslint-config`** & **`tailwind-config`**: Shared configurations for linting and styling.

### 🛠️ Tech Stack (Main Stack)

* **Framework**: **Next.js 15** (App Router)
* **Language**: JavaScript / TypeScript (Files show `.js`, `.jsx` mixed with TS awareness)
* **Core Library**: **React 19**
* **Styling**:
  * **Tailwind CSS v4** (Primary styling engine)
  * **HeroUI** & **Radix UI** (Accessible UI primitives)
  * **MUI (Material UI)** & **Emotion** (Used for specific components)
* **Animations**: **Framer Motion**, **GSAP**, and **Motion** for high-fidelity interactions.
* **Data & State**:
  * **Mongoose**: MongoDB object modeling (likely for direct DB routes).
  * **SWR**: React hooks for data fetching.
  * **Axios**: HTTP client.

### 🚀 Key Features & libraries

* **Interactive UI**: Uses `swiper`, `keen-slider`, and `react-image-gallery` for carousels and media.
* **Data Visualization**: `chart.js` and `react-chartjs-2`.
* **Search**: `fuzzysort` for client-side fuzzy searching.
* **Build System**: `turbo` for high-performance builds and caching.
