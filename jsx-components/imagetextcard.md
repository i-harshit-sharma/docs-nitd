# ImageTextCard

The `ImageTextCard` component displays an image alongside a text block. It features a responsive layout where the text height is initially clamped to match the image height, with a "Read more" expander for overflowing content.

### Usage

```jsx
import ImageTextCard from '@/components/imageText';

export default function FacilityPage() {
  return (
    <div className="container mx-auto p-4">
      <ImageTextCard 
        imageSrc="/images/lab.jpg" 
        text={`Lorem ipsum dolor sit amet, consectetur adipiscing elit...`}
        imgWidth={40} 
      />
    </div>
  );
}
```

### Props

<table><thead><tr><th width="105.4000244140625">Prop</th><th width="93.20001220703125">Type</th><th width="100.4000244140625">Default</th><th>Description</th></tr></thead><tbody><tr><td><code>imageSrc</code></td><td><code>string</code></td><td><code>required</code></td><td>Path to the image file.</td></tr><tr><td><code>text</code></td><td><code>string</code></td><td><code>required</code></td><td>The content text to be displayed.</td></tr><tr><td><code>imgWidth</code></td><td><code>number</code></td><td><code>50</code></td><td>The percentage width of the image column (0-100). The text column will take the remaining <code>100 - imgWidth</code>.</td></tr><tr><td><code>reverse</code></td><td><code>boolean</code></td><td><code>false</code></td><td><em>Reserved for future use.</em> Intended to switch the visual order of image and text.</td></tr></tbody></table>

### Features

#### 1. Dynamic Text Truncation

The component measures the rendered height of the image.

* **Collapsed State**: The text container's `maxHeight` is set to the image's height.
* **Overflow Detection**: If the full text height exceeds the image height, a "Read more" link appears.
* **Gradient Overlay**: When collapsed and overflowing, a white gradient fade is applied over the last few words to smooth the transition.

#### 2. Flexible Layout

* The `imgWidth` prop allows fine-grained control over the image-to-text ratio (e.g., 40/60 split).
* Uses inline `flexBasis` styles to strictly enforce widths based on the passed percentage.
* On small screens (`sm` breakpoint and below), it stacks vertically (`flex-col`).

### Technical Implementation

* **`ResizeObserver`**: Monitors the image height changes (e.g., on window resize or load) to update the text container clamp.
* **State Management**:
  * `imageHeight`: Stores the current pixel height of the image.
  * `isOverflowing`: Boolean to determine if the text exceeds the clamp height.
  * `expanded`: Boolean to toggle between clamped and full view.
