# PagesHeading

The `PagesHeading` component is a stylized header used at the top of pages. It features a gradient background, a prominent title with a distinctive shadow effect, and a decorative transparent image.

### Usage

```jsx
import PagesHeading from './PagesHeading';

export default function AboutPage() {
  return (
    <main>
       <PagesHeading title="About Us" />
       {/* Page content... */}
    </main>
  );
}
```

### Props

| Prop    | Type     | Default    | Description                                   |
| ------- | -------- | ---------- | --------------------------------------------- |
| `title` | `string` | `required` | The text to be displayed as the main heading. |

### Styling & Features

* **Background**: Uses a horizontal linear gradient (`from-[#1760a8] via-[#4889b7] to-[#a3c4df]`).
* **Title**:
  * Dark blue background (`#0b3666`).
  * White text.
  * Distinctive solid shadow effect (white offset shadow), which scales with screen size (`4px` on mobile, `8px` on larger screens).
  * Responsive font sizes (ranging from `text-md` to `text-4xl`).
* **Image**:
  * Displays a static asset `/images/admin-transparent.png`.
  * Positioned to the right end (`flex-end`).
  * Non-selectable (`select-none`).
