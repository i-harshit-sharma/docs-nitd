# SearchButton

The `SearchButton` component provides a "headless" search UI pattern. It renders a search input and manages the filtering of a list based on a search query, but leaves the rendering of the results entirely to the parent component. It includes keyboard shortcuts (`Ctrl + k`) and fuzzy search capabilities.

### Usage

The component expects a `list` of items and a `setList` function to update the state in the parent component.

#### Example: Institute Assignments Page

This example demonstrates how to use `SearchButton` to filter a list of notifications/assignments.

```jsx
import { useState } from 'react';
import SearchButton, { highlightMatch } from '@/components/SearchButton';

// 1. Define your initial data
const initialData = [
  { name: "Assignment 1", link: "/path/to/1" },
  { name: "Assignment 2", link: "/path/to/2" },
];

function Page() {
    // 2. maintain state for the filtered list, initialized with full data
    const [list, setList] = useState(initialData);

    return (
        <div>
            {/* 3. Render SearchButton, passing the FULL list and the setter */}
            <div className='max-w-4xl mx-auto mb-10'>
                <SearchButton 
                    list={initialData} 
                    setList={setList} 
                />
            </div>

            {/* 4. Render the filtered 'list' */}
            <table>
                <tbody>
                    {list.map((item, index) => (
                        <tr key={index}>
                            <td>
                                {/* 5. (Optional) Use highlightMatch to show fuzzy matches */}
                                {highlightMatch(item.name, item._highlightIndexes)}
                            </td>
                            <td>
                                <a href={item.link}>Download</a>
                            </td>
                        </tr>
                    ))}
                </tbody>
            </table>
        </div>
    )
}
```

### Props

<table><thead><tr><th width="117.39996337890625">Prop</th><th width="140.60003662109375">Type</th><th width="161.39990234375">Default</th><th>Description</th></tr></thead><tbody><tr><td><code>list</code></td><td><code>Array</code></td><td>Required</td><td>The <strong>full, original</strong> list of items to search through. <strong>Important:</strong> Do not pass the filtered state here; always pass the source of truth.</td></tr><tr><td><code>setList</code></td><td><code>Function</code></td><td>Required</td><td>The state setter function from <code>useState</code> in the parent. This is called with the filtered results.</td></tr><tr><td><code>searchFn</code></td><td><code>Function</code></td><td><code>defaultSearchFn</code></td><td>Optional custom function to handle search logic. Defaults to <code>fuzzysort</code> on the key <code>"name"</code>.</td></tr></tbody></table>

### How It Works

1. **State Management**: The component maintains its own local state for the search query (`search` string).
2. **Filtering**: On every keystroke (technically, on every render where `search` changes), a `useEffect` runs:
   * It calls `searchFn` (or the default `fuzzysort`) with the _full_ `list` and the current `search` string.
   * The `searchFn` returns a new filtered array.
   * The component calls the parent's `setList` with this new array.
3. **Result Augmentation**: The default search function adds a special `_highlightIndexes` property to each item in the results. This allows the parent to highlight the matching characters using `highlightMatch`.
4. **Interaction**:
   * When the user types, the list filters in real-time.
   * Ctrl+K focuses the input and smooth-scrolls to the search bar.

### Helper Functions

#### `highlightMatch(text, indexes)`

A utility function exported from `SearchButton` to render highlighted text based on fuzzy match indexes.

* **text**: The original string.
* **indexes**: The `_highlightIndexes` array added to the object by the default search function.

### Keyboard Shortcuts

* **`Ctrl + k` / `Cmd + k`**: Focuses the search input field.
* **`Esc`**: (Standard browser behavior) unfocuses input.

### Implementation Gotchas

#### 1. Data Source of Truth

**CRITICAL**: You must pass the _full, original_ list to the `list` prop, NOT the state variable that holds the filtered results.

* **Correct**: `list={initialData}` (where `initialData` is constant)
* **Incorrect**: `list={list}` (where `list` is the state being updated)
* _Why?_ If you pass the filtered state, typing "a" reduces the list to only "a" items. Deleting "a" will search against the _filtered_ list, meaning you can never recover the original items without a page reload.

#### 2. Default Key Dependency

By default, the component assumes every object in your list has a `name` property.

```javascript
// Default behavior in SearchButton.js
const results = fuzzysort.go(query, list, { key: "name", ... });
```

If your data uses a different key (e.g., `title`, `label`), the search will quietly fail. You must provide a custom `searchFn` prop to override this.

#### 3. Performance on Large Lists

The filtering happens on the main thread inside a `useEffect`.

* For lists < 1,000 items, this is fine.
* For very large lists (10,000+), this might cause UI jank while typing. The component does not include built-in debouncing or Web Worker offloading.

#### 4. Layout Shift

The `InputGroup` has a class `scroll-mt-32`. When `Ctrl+K` is pressed, the browser smooth-scrolls the page to bring the search bar into view. Ensure your layout accommodates this behavior to prevent jarring jumps.
