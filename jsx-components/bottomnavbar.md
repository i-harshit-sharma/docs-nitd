# BottomNavbar

The `BottomNavbar` component is a fully configuration-driven, responsive navigation bar. It automatically switches between a sticky horizontal bar on desktop and a hamburger-menu drawer on mobile devices based on a configured config.

### Usage

**IMPORTANT:** This component works only if used in client component and provide match: mandatorily.

The component requires a `navConfig` object to define its structure. Below are three real-world examples demonstrating different configurations.

#### Example 1: Standard Link Navigation

**Use Case:** Technical Club Page A simple list of links with icons.

```jsx
import { House, Users, Images, FolderOpen, MailQuestion } from "lucide-react";

export const techClubConfig = {
    heading: "Tech Club",
    breakpoint: 840, // Switch to mobile menu below 840px
    arr: [
        {
            type: "link",
            label: "About",
            href: "/facilities/technical-club/about",
            icon: House,
            match: (path) => path?.[3] === "about",
        },
        {
            type: "link",
            label: "Gallery",
            href: "/facilities/technical-club/gallery",
            icon: Images,
            match: (path) => path?.[3] === "gallery",
        },
        // ... more links
    ],
};
```

#### Example 2: Mixed Links & Dropdowns

**Use Case:** Convocation Page Combines direct links with a dropdown menu for past events.

```jsx
import { House, GraduationCap, Medal, Users } from "lucide-react";

export const convocationConfig = {
    heading: "Convocations",
    breakpoint: 1050,
    arr: [
        {
            type: "link",
            label: "Current Convocation",
            href: "/",
            icon: House,
            // Match logic checks the second path segment
            match: (path) => path?.[1] === "",
        },
        {
            type: "link",
            label: "Medalists",
            href: "/medalists",
            icon: Users,
            match: (path) => path?.[1] === "medalists",
        },
        {
            type: "dropdown",
            key: "past-convocations", // Unique key for state
            label: "Past Convocations",
            icon: Medal,
            // The dropdown parent itself can highlight if a child matches
            match: (path) => ["4th", "3rd", "2nd"].includes(path?.[1]),
            items: [
                {
                    label: "4th Convocation",
                    href: "/4th",
                    match: "4th", // Sub-item match string
                },
                {
                    label: "3rd Convocation",
                    href: "/3rd",
                    match: "3rd",
                },
            ],
        },
    ],
};
```

#### Example 3: Tab-Style Buttons

**Use Case:** RTI Disclosure Uses `type: "btn"` to act as an in-page tab switcher instead of improving navigation. This requires passing `selectID` and `setID` props to the component.

```jsx
// navConfig.js
export const rtiConfig = {
    heading: "RTI Info",
    breakpoint: 1260,
    arr: [
        {
            type: "btn",
            label: "Organisation & Function",
            // No href needed for buttons, logic handled by parent state
            match: () => false, 
        },
        {
            type: "btn",
            label: "Budget & Programmes",
            match: () => false,
        },
    ]
};

// Page Component
function RtiPage() {
    const [activeTab, setActiveTab] = useState(0); 
    
    return (
        <Navbar 
            navConfig={rtiConfig}
            selectID={activeTab}
            setID={setActiveTab}
        />
    );
}
```

### Props

<table><thead><tr><th width="115">Prop</th><th width="111.800048828125">Type</th><th width="112.800048828125">Default</th><th>Description</th></tr></thead><tbody><tr><td><code>navConfig</code></td><td><code>NavConfig</code></td><td>Required</td><td>The configuration object defining the menu structure.</td></tr><tr><td><code>selectID</code></td><td><code>number</code></td><td><code>null</code></td><td>The index of the currently active <em>tab-style</em> button (if using <code>type: "btn"</code>).</td></tr><tr><td><code>setID</code></td><td><code>Function</code></td><td><code>undefined</code></td><td>State setter for <code>selectID</code>. Required if using <code>type: "btn"</code>.</td></tr><tr><td><code>Debug</code></td><td><code>boolean</code></td><td><code>false</code></td><td>If true, logs the current route segments to the console for debugging <code>match</code> functions.</td></tr></tbody></table>

### Configuration (`NavConfig`)

The config object controls the layout. Structure defined in `navConfig.types.js`.

**Passing match: is mandatory for now**

#### Top-Level Properties

* **`breakpoint`** (number): The viewport width (px) below which the navbar switches to mobile mode.
* **`heading`** (string): The title displayed at the top of the mobile drawer.
* **`arr`** (Array): List of items (`Link`, `Button`, or `Dropdown`).

#### Item Types

**1. Link Item**

Navigates to a URL.

```javascript
{
  type: "link",
  label: "Home",
  href: "/home",
  icon: IconComponent, // Optional Lucide icon
  match: (pathArray) => boolean // Returns true if active
}
```

**2. Button Item (`btn`)**

Acts like a tab selector. Does not navigate; matches state based on `selectID`.

```javascript
{
  type: "btn",
  label: "Tabs",
  // requires passing selectID and setID to the component
}
```

**3. Dropdown Item**

Opens a sub-menu.

```javascript
{
  type: "dropdown",
  key: "unique-key", // Unique string for state management
  label: "Menu",
  items: [
    { label: "Sub Link", href: "...", match: "string-segment" }
  ]
}
```

### Implementation Gotchas

#### 1. Hardcoded Sticky Offsets

The component has hardcoded `top` values for its sticky positioning, tailored to a specific header height:

```javascript
className="sticky 400px:top-[153px] sm:top-[120px] max-[400px]:top-[125px] ..."
```

**Risk**: If you change the main site header's height, this navbar might overlap it or leave a gap. You may need to edit these Tailwind classes in `BottomNavbar.jsx`.

#### 2. Match Functions

The `match` function receives the _split_ pathname array (e.g., `['', 'apps', 'home']`).

* **Debugging**: Pass `Debug={true}` to the component to see the exact `path` array in the console, ensuring your match logic is correct.

#### 3. Client-Side Responsiveness

The mobile/desktop switch uses `window.innerWidth`.

* It runs only on the client (`use client`).
* The component returns an empty placeholder div initially until the specific mount check runs to prevent hydration mismatches. This might cause a slight layout shift or "pop" on initial load.

#### 4. Click Outside

The dropdowns use a `document` event listener to close when clicking outside. Ensure `navRef` is correctly bound (it is by default) so this behavior persists.
