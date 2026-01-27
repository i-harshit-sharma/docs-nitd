# Gallery

The `Gallery` component renders a responsive grid of images that opens into a feature-rich, full-screen lightbox (modal) when clicked. It includes keyboard navigation, touch support, downloading, and a thumbnail strip.

### Usage

Basic usage requires passing an array of images.

#### Example: Campus Event Album

```jsx
import Gallery from "@/components/ui/Gallery";

// Data can be simple strings or objects
const eventImages = [
  "https://example.com/photo1.jpg",
  { 
      src: "https://example.com/photo2.jpg", 
      caption: "Opening Ceremony" // Optional metadata
  }
];

export default function EventPage() {
  return (
    <Gallery 
      images={eventImages} 
      title="Zeal 2024" 
      backLink="/campus/events/zeal"
      showBackLink={true}
    />
  );
}
```

### Props

<table><thead><tr><th width="155.79998779296875">Prop</th><th width="217">Type</th><th width="135.800048828125">Default</th><th width="335.40008544921875">Description</th></tr></thead><tbody><tr><td><code>images</code></td><td><code>Array&#x3C;string | Object></code></td><td>Required</td><td>The content to display. Can be an array of URL strings OR objects. If objects are used, they <strong>must</strong> have a <code>src</code> property.</td></tr><tr><td><code>title</code></td><td><code>string | undefined</code></td><td><code>undefined</code></td><td>The large title text displayed above the gallery grid. If undefined the backLink will also not be shown.</td></tr><tr><td><code>showBackLink</code></td><td><code>boolean</code></td><td><code>true</code></td><td>If true, displays a circular back arrow button next to the title.</td></tr><tr><td><code>backLink</code></td><td><code>string</code></td><td><code>""</code></td><td>The URL helper for the back button. Required if <code>showBackLink</code> is true.</td></tr><tr><td><code>renderData</code></td><td><code>boolean</code></td><td><code>false</code></td><td><em>Reserved for future use.</em> Currently unused by the component logic.</td></tr></tbody></table>

### How It Works

1. **Grid Layout**:
   * The component initially renders a CSS Grid.
   * Columns: 2 on mobile, 4 on medium screens and up.
   * Aspect Ratio: Forces a strict `4/3` aspect ratio for grid thumbnails using `aspect-[4/3]` and `object-cover`.
2. **Lightbox (Modal)**:
   * Clicking an image sets `isOpen` to `true` and the clicked index to `current`.
   * Uses `framer-motion`'s `AnimatePresence` for smooth fade-in/out transitions.
   * **Scroll Locking**: When open, it sets `document.body.style.overflow = "hidden"` to prevent the background page from scrolling.
3. **Navigation**:
   * **Keyboard**: Listens for global `keydown` events when open.
     * `ArrowRight` / `ArrowLeft`: Navigate images.
     * `Escape`: Close modal.
     * `f`: Toggle fullscreen.
     * `s`: Download current image.
   * **Thumbnails**: A scrollable strip at the bottom. It uses `scrollIntoView` to automatically keep the active thumbnail centered in the strip.

### Implementation Gotchas

#### 1. Image Object Structure

The component supports mixed arrays, but if you use objects, you **must** nest the URL under `src`.

* **Correct**: `[{ src: "/img.jpg" }]`
* **Incorrect**: `[{ url: "/img.jpg" }]` (Will break, as code expects `img.src`)

#### 2. Z-Index Conflicts

The modal uses `z-50`.

```javascript
className="fixed inset-0 ... z-50 ..."
```

If your application uses a navbar or toast notification system with `z-index > 50`, they might appear on top of the lightbox. Ensure your stacking context is managed (e.g., usually navbars are z-40 or z-50).

#### 3. Download Restrictions (CORS)

The download button uses a client-side synthetic anchor click:

```javascript
const link = document.createElement("a");
link.href = imgSrc;
link.download = `image-${current + 1}.jpg`;
```

If your images are served from a different domain (e.g., an S3 bucket or external CDN) and strictly encforce CORS, this programmatic download might fail or open in a new tab instead of downloading, depending on browser security policies.

#### 4. Empty State

If `images` is empty, it returns a specific "No Images Here" fallback UI with an `ImageOff` icon, taking up `h-64`. This is different from returning `null` or an empty fragment.
