# Media API (Upload & Download)

## Upload Image

```javascript
const result = await api.uploadImage(auth, imageBuffer, options?)
// result: { success, key: 'img_v3_02ve_...' }
```

| Param | Type | Description |
|-------|------|-------------|
| `imageBuffer` | Buffer | Raw image data |
| `options.chunkSize` | number? | Chunk size in bytes (default: 4 MB) |

```javascript
import fs from 'fs';

const imgBuf = fs.readFileSync('photo.png');
const result = await api.uploadImage(auth, imgBuf);
// result.key -> 'img_v3_02ve_...'

// Then send as image message
await api.sendImageMessage(auth, result.key, chatId);
```

## Upload File

```javascript
const result = await api.uploadFile(auth, fileBuffer, filename, options?)
// result: { success, key: 'file_v3_00ve_...' }
```

| Param | Type | Description |
|-------|------|-------------|
| `fileBuffer` | Buffer | Raw file data |
| `filename` | string | Original filename (e.g. `'report.pdf'`) |
| `options.mimeType` | string? | MIME type (default: `'application/octet-stream'`) |
| `options.chunkSize` | number? | Chunk size in bytes (default: 4 MB) |

```javascript
const fileBuf = fs.readFileSync('song.mp3');
const result = await api.uploadFile(auth, fileBuf, 'song.mp3', {
  mimeType: 'audio/mpeg'
});
// result.key -> 'file_v3_00ve_...'

// Then send as file message
await api.sendFileMessage(auth, result.key, chatId);
```

Upload uses a three-step chunked protocol (create -> part(s) -> complete). This is handled internally — you just pass the buffer.

## Download Image

```javascript
const result = await api.downloadImage(imageKey, options?)
// result: { success, data: Buffer, base64: string, mimeType: string }
```

| Param | Type | Description |
|-------|------|-------------|
| `imageKey` | string | Image key (e.g. `'img_v3_02uc_...'`) |
| `options.size` | string? | `'ORIGIN'` (default), `'MIDDLE_WEBP'`, `'SMALL_WEBP'` |

```javascript
const result = await api.downloadImage('img_v3_02uc_067a474f-300f-46d2-b602-c604ea22c5eg');
if (result.success) {
  fs.writeFileSync('image.png', result.data);
  console.log(result.mimeType); // 'image/png'
}
```

**Note**: `downloadImage` does NOT require `auth` — it's a public CDN fetch. If the original size fails, it automatically falls back to `MIDDLE_WEBP`.

For encrypted images (from chat history), call `getChatHistory()` first — it auto-caches decryption keys. Subsequent `downloadImage()` calls will decrypt transparently.

Current MCP tool: `download_image`. MCP returns image content inline, not a filesystem path.

## Download File to Local Temp Path

```javascript
const result = await api.downloadFile(auth, messageId, fileKey, {
  chatId,
  scene: 'chat'
})
// result: { success, fileName?, mimeType?, size?, path?, ... }
```

| Param | Type | Description |
|-------|------|-------------|
| `messageId` | string | Message ID containing the file |
| `fileKey` | string | File key from the message |
| `options.chatId` | string | Chat ID where the file message lives |
| `options.scene` | string? | Download scene (default: `'chat'`) |

Current OpenBird writes the downloaded attachment to a local temp directory and returns an absolute `path`.

Current MCP tool: `download_file`.

MCP input parameters:

| Param | Type | Description |
|-------|------|-------------|
| `message_id` | string | Message ID containing the file |
| `file_key` | string | File key from the message content |
| `chat_id` | string | Chat ID where the file message belongs |
| `scene` | string? | Download scene (default: `'chat'`) |

Current MCP behavior saves files under `/tmp/openbird-downloads` and returns JSON metadata including the local `path`.

## Complete Upload-Send Flow

```javascript
// Upload image then send
const imgBuf = fs.readFileSync('screenshot.png');
const upload = await api.uploadImage(auth, imgBuf);
if (upload.success) {
  await api.sendImageMessage(auth, upload.key, chatId);
}

// Upload file then send
const fileBuf = fs.readFileSync('doc.pdf');
const upload = await api.uploadFile(auth, fileBuf, 'doc.pdf', {
  mimeType: 'application/pdf'
});
if (upload.success) {
  await api.sendFileMessage(auth, upload.key, chatId);
}
```
