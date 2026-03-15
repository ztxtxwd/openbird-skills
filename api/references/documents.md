# Document Editor API

OpenBird provides two levels of document access:

1. **Read-only**: `FeishuApi` methods to resolve wiki tokens and fetch block trees
2. **Read-write**: `DocxEditor` class for stateful document editing

## Read-Only: Resolve Wiki Token

When you have a wiki URL (`https://xxx.feishu.cn/wiki/ABC123`), you first need to resolve the wiki token to get the document's `objToken`:

```javascript
const result = await api.resolveWikiToken(auth, origin, wikiToken)
// result: { success, data: { objToken, objType, spaceId, wikiToken, wikiVersion } }
```

| Param | Type | Description |
|-------|------|-------------|
| `origin` | string | Feishu origin (e.g. `'https://xxx.feishu.cn'`) |
| `wikiToken` | string | Wiki token from URL path |

**Note**: This method follows up to 5 redirects and parses server-side rendered HTML.

## Read-Only: Get Document Block Tree

```javascript
const result = await api.getDocxBlockTree(auth, origin, objToken, options?)
// result: { success, data: { token, structureVersion, totalBlocks, tree, rawBlockMap } }
```

| Param | Type | Description |
|-------|------|-------------|
| `origin` | string | Feishu origin |
| `objToken` | string | Document obj_token |
| `options.mode` | number? | Fetch mode (default: `0`) |
| `options.limit` | number? | Blocks per page (default: `200`) |
| `options.maxPages` | number? | Max pages to fetch (default: `100`) |

The returned `tree` is a nested structure:

```javascript
{
  id: 'block_abc',
  type: 'text',        // text, heading1, heading2, ..., todo, code, quote, etc.
  version: 5,
  parentId: 'page_root',
  text: 'Hello world',
  children: [...]
}
```

Large documents are automatically paginated — all pages are fetched and merged.

---

## DocxEditor: Stateful Document Editing

`DocxEditor` wraps the low-level `submitDocxChange` API with a high-level, stateful interface. It maintains a local copy of the block tree and generates correct change maps.

### Setup

```javascript
import FeishuApi from 'openbird/core/api.js';
import DocxEditor from 'openbird/core/docx-editor.js';

const api = new FeishuApi();
const editor = new DocxEditor(api, auth, 'https://xxx.feishu.cn', pageId);
```

| Param | Type | Description |
|-------|------|-------------|
| `api` | FeishuApi | API instance |
| `auth` | Object | Auth object with cookies |
| `origin` | string | Feishu origin (e.g. `'https://xxx.feishu.cn'`) |
| `pageId` | string | Document obj_token (page root block ID) |

### Open Document

You **must** call `open()` before any other operation:

```javascript
const result = await editor.open()
// result: { success, data: { blockCount, structureVersion } }
```

This fetches the current block tree and initializes internal state.

### Get Block Tree

```javascript
const tree = editor.getBlockTree()
// Returns: { id, type, version, parentId, text, children: [...] }
```

### Get Single Block

```javascript
const block = editor.getBlock(blockId)
// Returns: block object or null
```

### Insert Block

```javascript
const result = await editor.insertBlock(parentId, position, type, options?)
// result: { success, data: { blockId } }
```

| Param | Type | Description |
|-------|------|-------------|
| `parentId` | string | Parent block ID |
| `position` | number | Index in parent's children array |
| `type` | string | Block type (see below) |
| `options.text` | string? | Initial text content |
| `options.folded` | boolean? | Collapsed state |
| `options.done` | boolean? | Todo done state |
| `options.level` | number? | Heading level |
| `options.align` | string? | Text alignment: `'left'`, `'center'`, `'right'` |
| `options.language` | string? | Code block language |

**Block types**: `text`, `heading1`, `heading2`, `heading3`, `heading4`, `heading5`, `heading6`, `heading7`, `heading8`, `heading9`, `todo`, `ordered`, `bullet`, `code`, `quote`, `callout`, `divider`, `image`, `table`, `grid`

```javascript
// Insert a text block as the first child of the page
await editor.insertBlock(pageId, 0, 'text', { text: 'Hello world' });

// Insert a todo item
await editor.insertBlock(parentId, 2, 'todo', { text: 'Review PR', done: false });

// Insert a code block
await editor.insertBlock(parentId, 0, 'code', { text: 'console.log("hi")', language: 'javascript' });
```

### Edit Text

```javascript
const result = await editor.editText(blockId, newText)
// result: { success }
```

Replaces the entire text content of a block. Uses EasySync changesets internally.

```javascript
await editor.editText(blockId, 'Updated content with **bold**');
```

### Delete Blocks

```javascript
const result = await editor.deleteBlocks(blockIds)
// result: { success }
```

| Param | Type | Description |
|-------|------|-------------|
| `blockIds` | string[] | Block IDs to delete |

Deletes blocks and all their descendants. Blocks are validated before deletion.

```javascript
await editor.deleteBlocks(['block_abc', 'block_def']);
```

### Change Block Type

```javascript
const result = await editor.changeBlockType(blockId, newType)
// result: { success }
```

```javascript
// Convert a text block to heading1
await editor.changeBlockType(blockId, 'heading1');

// Convert to todo
await editor.changeBlockType(blockId, 'todo');
```

### Set Block Properties

```javascript
const result = await editor.setBlockProperties(blockId, properties)
// result: { success }
```

Common properties:

| Property | Type | Description |
|----------|------|-------------|
| `done` | boolean | Todo completion state |
| `folded` | boolean | Collapsed/expanded state |
| `align` | string | `'left'`, `'center'`, `'right'` |
| `language` | string | Code block language |
| `emoji_id` | string | Callout block emoji |
| `text_indent` | number | Text indentation level |

```javascript
// Mark todo as done
await editor.setBlockProperties(blockId, { done: true });

// Set code language
await editor.setBlockProperties(blockId, { language: 'python' });
```

### Move Block

```javascript
const result = await editor.moveBlock(blockId, newParentId, newPosition)
// result: { success }
```

| Param | Type | Description |
|-------|------|-------------|
| `blockId` | string | Block to move |
| `newParentId` | string | Target parent block ID |
| `newPosition` | number | Target index in new parent's children |

```javascript
// Move block to be the first child of another parent
await editor.moveBlock('block_abc', 'block_parent', 0);
```

---

## Complete Example: Read and Edit a Wiki Document

```javascript
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';
import DocxEditor from 'openbird/core/docx-editor.js';

const auth = new FeishuAuth();
auth.prepareAuth(process.env.OPENBIRD_COOKIE);
const api = new FeishuApi();

// Bootstrap
const { xCsrfToken } = await api.getCsrfToken(auth);
await api.getUserInfo(auth, xCsrfToken);

const origin = 'https://xxx.feishu.cn';

// 1. Resolve wiki token to get objToken
const wiki = await api.resolveWikiToken(auth, origin, 'ABC123wikiToken');
const objToken = wiki.data.objToken;

// 2. Open document for editing
const editor = new DocxEditor(api, auth, origin, objToken);
await editor.open();

// 3. Read the block tree
const tree = editor.getBlockTree();
console.log(tree);

// 4. Insert a new block
await editor.insertBlock(objToken, 0, 'text', { text: 'Added by OpenBird' });

// 5. Edit existing text
const firstChild = tree.children[0];
await editor.editText(firstChild.id, 'Updated text');
```
