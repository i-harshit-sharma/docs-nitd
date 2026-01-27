# EmailToImage

The `EmailToImage` component is a specialized image component designed to render a user's profile picture based on their email address. It seamlessly handles fetching the image from the backend, manages fallback states, and ensures the image is optimized.

### Usage

```jsx
import EmailToImage from './EmailToImage';

// Basic usage with email
<EmailToImage 
  email="faculty@nitdelhi.ac.in" 
  name="Faculty Name" 
/>

// Usage with custom dimensions and styling
<EmailToImage 
  email="faculty@nitdelhi.ac.in"
  width={200}
  height={250}
  className="rounded-full"
/>

// Usage with direct source (bypassing email lookup)
<EmailToImage 
  src="/path/to/local/image.jpg"
  name="Local User"
/>
```

### Props

| Prop        | Type     | Default            | Description                                                                                      |
| ----------- | -------- | ------------------ | ------------------------------------------------------------------------------------------------ |
| `email`     | `string` | `null`             | The email address to look up the profile image for. If provided, it takes precedence over `src`. |
| `name`      | `string` | `"User image"`     | The alt text for the image, important for accessibility.                                         |
| `src`       | `string` | `undefined`        | A direct image source to use if `email` is not provided.                                         |
| `fallback`  | `string` | Default Avatar URL | The URL of the image to show if the primary image fails to load.                                 |
| `width`     | `number` | `300`              | The width of the rendered image.                                                                 |
| `height`    | `number` | `300`              | The height of the rendered image.                                                                |
| `className` | `string` | `undefined`        | Additional CSS classes. Note: an `aspect-[640/723]` class is always appended.                    |

### How It Works

#### Frontend (`EmailToImage.jsx`)

1. **Initialization**: The component initializes its `src` state.
   * If an `email` is provided, it sets the source to `/api/images/<encoded-email>`.
   * If no `email` is provided, it uses `src` or the `fallback`.
2. **Rendering**: It renders a Next.js `<Image/>` component with the determined source.
3. **Error Handling**: If the image fails to load (e.g., 404 from the API), the `onError` handler triggers and updates the `src` state to the `fallback` URL.
4. **Priority**: The image is always rendered with `priority={true}`, meaning it preloads and is considered high priority.

#### Backend API (`/api/images/[email]`)

The component relies on a dedicated API route to resolve email addresses to image URLs. This route acts as a proxy to the NIT Delhi Faculty Portal.

**Route:** `GET /app/api/images/[email]/route.js`

**Flow:**

1. **Receive Request**: Accepts the `email` parameter from the URL.
2. **Faculty Info Lookup**:
   * Fetches Image from `Faculty Profile`.
   * If the faculty member is not found, returns `404`.
3. **Image Resolution**:
   * Extracts the `profileImage` path from the faculty data.
   * If no image path exists, returns `404`.
4. **Image Proxying**:
   * Fetches the actual image binary from the `profileImage` path.
   * If the image fetch fails, returns `502`.
5. **Response**:
   * Returns the image binary directly.
   * Sets `Content-Type` based on the fetched image (e.g., `image/jpeg`).
   * Sets caching headers: `public, max-age=7200, stale-while-revalidate=60`.

### Implementation Gotchas

#### 1. Hardcoded Aspect Ratio

The component forces a specific aspect ratio via Tailwind classes:

```javascript
className={className + "aspect-[640/723]"}
```

This `aspect-[640/723]` is always appended. If you need a different aspect ratio (e.g., a perfect square), you may need to override this via CSS layers or modify the component to accept an optional prop for aspect ratio.

#### 2. Deep Caching

The API route sets a `max-age=7200` (2 hours).

```javascript
"Cache-Control": "public, max-age=7200, stale-while-revalidate=60",
```

If a user updates their profile picture on the faculty portal, changes might not reflect immediately in the application due to this cache and potentially browser caching.

#### 3. Priority Load

The `<Image>` component has `priority` set to `true` by default:

```javascript
<Image ... priority />
```

This is great for above-the-fold content, but if you render a list of 50 faculty members, setting them all to `priority` will hurt performance and Core Web Vitals (LCP). Consider making this a prop if used in lists.

#### 4. Dependency on External Service

The API route depends entirely on `Faculty server`. If that site goes down or changes its API structure (e.g., renames `profileImage` field), this feature will break.

#### 5. No fallback on API logic

The `onError` in the component handles the case where the _image_ fails to load. However, if the API returns a 404 (Faculty not found), the browser console will show a 404 error, which is expected but can be noisy during development if many emails are invalid.
