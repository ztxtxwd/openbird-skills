# Document Editor API

OpenBird provides two related document layers:

1. **Low-level read APIs** via `FeishuApi`
2. **Stateful editing** via `DocxEditor`
3. **Agent-facing MCP document tools** in current OpenBird releases

## Read-Only: Resolve Wiki Token

When you have a wiki URL (`https://xxx.feishu.cn/wiki/ABC123`), first resolve the wiki token to get the document `objToken`:

```javascript
const result = await api.resolveWikiToken(auth, origin, wikiToken)
// result: { success, data: { objToken, objType, spaceId, wikiToken, wikiVersion } }
```

**Note**: this follows redirects and parses SSR HTML.

## Read-Only: Get Document Block Tree

```javascript
const result = await api.getDocxBlockTree(auth, origin, objToken, options?)
// result: { success, data: { token, structureVersion, totalBlocks, tree, rawBlockMap } }
```

| Param | Type | Description |
|-------|------|-------------|
| `origin` | string | Feishu origin |
| `objToken` | string | Document obj_token |
| `options.mode` | number? | Fetch mode |
| `options.limit` | number? | Blocks per page |
| `options.maxPages` | number? | Max pages to fetch |

Large documents are automatically paginated and merged.

## Current MCP Document Tools

Current OpenBird MCP exposes higher-level document tools with these exact names:

- `get_docx_block_tree`
- `docx_open_document`
- `docx_insert_block`
- `docx_edit_text`
- `docx_delete_blocks`
- `docx_change_block_type`
- `docx_set_block_properties`
- `docx_move_block`

These tools accept a Feishu wiki/docx URL directly and handle wiki-token resolution internally.

### MCP URL rules

- URL must contain `/wiki/` or `/docx/`
- For `/wiki/`, OpenBird resolves the wiki token first
- Only wiki object type `22` (docx document) is supported
- Non-Feishu origins are rejected at the MCP boundary

## DocxEditor: Stateful Document Editing

`DocxEditor` wraps low-level document change APIs with a high-level, stateful interface. It maintains a local copy of the block tree and generates correct change maps.

### Setup

```javascript
import FeishuApi from 'openbird/core/api.js';
import DocxEditor from 'openbird/core/docx-editor.js';

const api = new FeishuApi();
const editor = new DocxEditor(api, auth, 'https://xxx.feishu.cn', pageId);
```

### Open Document

You must call `open()` before any other operation:

```javascript
const result = await editor.open()
// result: { success, data: { blockCount, structureVersion } }
```

### Common Editing Operations

```javascript
const tree = editor.getBlockTree()
const block = editor.getBlock(blockId)
await editor.insertBlock(parentId, position, 'text', { text: 'Hello world' })
await editor.editText(blockId, 'Updated content')
await editor.deleteBlocks(['block_abc'])
await editor.changeBlockType(blockId, 'heading1')
await editor.setBlockProperties(blockId, { done: true })
await editor.moveBlock(blockId, newParentId, newPosition)
```

Common block types include `text`, `heading1-9`, `todo`, `ordered`, `bullet`, `code`, `quote`, `callout`, `divider`, `image`, `table`, and `grid`.

## Complete Example: Read and Edit a Wiki Document

```javascript
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';
import DocxEditor from 'openbird/core/docx-editor.js';

const auth = new FeishuAuth();
auth.prepareAuth(process.env.OPENBIRD_COOKIE);
const api = new FeishuApi();

const { xCsrfToken } = await api.getCsrfToken(auth);
await api.getUserInfo(auth, xCsrfToken);

const origin = 'https://xxx.feishu.cn';
const wiki = await api.resolveWikiToken(auth, origin, 'ABC123wikiToken');
const objToken = wiki.data.objToken;

const editor = new DocxEditor(api, auth, origin, objToken);
await editor.open();

const tree = editor.getBlockTree();
await editor.insertBlock(objToken, 0, 'text', { text: 'Added by OpenBird' });
await editor.editText(tree.children[0].id, 'Updated text');
```
