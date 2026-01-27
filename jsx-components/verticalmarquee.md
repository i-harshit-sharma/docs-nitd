# VerticalMarquee

The `VerticalMarquee` component renders a vertically scrollable list of announcements or releases. It is designed to display time-sensitive information, filtering out future posts, and supports expanding detail views for items with multiple attachments.

### Usage

```jsx
import VerticalMarquee from './VerticalMarquee';

const announcements = [
  {
    _id: "1",
    announcementTitle: "Exam Schedule Release",
    websiteReleaseDate: "2023-10-25T00:00:00.000Z",
    relatedDocuments: [
      {
        _id: "d1",
        documentTitle: "Final Schedule PDF",
        documentReleasedDate: "2023-10-25T00:00:00.000Z",
        documentURL: "Uploads/Notice Board/schedule@nitdelhi123.pdf"
      }
    ]
  }
];

export default function NewsSection() {
  return (
    <div className="h-[400px]">
      <VerticalMarquee items={announcements} showMore={true} />
    </div>
  );
}
```

### Props

<table><thead><tr><th width="107">Prop</th><th width="120.4000244140625">Type</th><th width="125">Default</th><th>Description</th></tr></thead><tbody><tr><td><code>items</code></td><td><code>Array</code></td><td><code>required</code></td><td>Array of announcement objects to display. See Data Structure below.</td></tr><tr><td><code>showMore</code></td><td><code>boolean</code></td><td><code>false</code></td><td>If <code>true</code>, displays a "More" button at the bottom linking to <code>/archive</code>.</td></tr></tbody></table>

### Data Structure

The `items` prop expects an array of objects matching this structure:

```typescript
type ReleaseItem = {
  _id: string;
  announcementTitle: string;
  websiteReleaseDate: string; // ISO Date string
  relatedDocuments: RelatedDocument[];
};

type RelatedDocument = {
  _id: string;
  documentTitle: string;
  documentReleasedDate: string; // ISO Date string
  documentURL?: string; // Path for file download
  externalLink?: string; // URL for external navigation
};
```

### Key Features

#### 1. Date Filtering

The component automatically filters the `items` array. Only items where `websiteReleaseDate` is less than or equal to the current date (end of today) are displayed.

#### 2. Expandable Content (`ReleaseCard`)

Each announcement is rendered as a `ReleaseCard`.

* If an announcement has more than 3 related documents, only the first 3 are shown initially.
* A "Read More" / "Show Less" toggle button appears to expand and reveal the remaining documents.
* The expansion uses a smooth height transition.

#### 3. File Downloads (`DocRow`)

The component handles file downloads specifically for the NIT Delhi backend structure.

* **Download Logic**: It calls a proxy API route `/api/announcements/download/:type/:filename`.
* **Filename Parsing**: It expects `documentURL` in a format like `Folder/Type/name@nitdelhiID.ext`. It extracts the clean filename and the announcement type to construct the API request.

### Dependencies

* **Lucide React**: Icons (`ChevronDown`, `ChevronUp`, `SquareArrowOutUpRight`, `Link2`).
* **Axios**: Used for fetching the download blob.
* **UI Components**: Uses a `DownloadIcon` from local UI components.
