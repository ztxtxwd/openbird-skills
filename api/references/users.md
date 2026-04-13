# User API

## Get User Info by ID

```javascript
const result = await api.getUserInfoById(auth, userId)
```

Returns:

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | |
| `userId` | string | User ID |
| `name` | string | Display name |
| `isBot` | boolean | Whether this is a bot/app |
| `userType` | number | `1` = user, `2` = bot |
| `avatarKey` | string | Avatar image key |

Works for both regular users and bots. This is the recommended method for looking up users. Current MCP tools in this area include `get_user_info`, `get_user_profile_card`, `set_user_signature`, and `get_user_relation`.

## Get User Profile Card

```javascript
const result = await api.getUserProfileCard(auth, userId)
```

Returns profile card data such as signature/description, display name, avatar key, and profile field policies.

**Note**: `userId` must be a 15-20 digit numeric string.

## Set User Signature

```javascript
const result = await api.setUserSignature(auth, description, options?)
```

| Param | Type | Description |
|-------|------|-------------|
| `description` | string | Signature text |
| `options.descriptionFlag` | number? | Signature flag (default: 0) |

Sets the current authenticated user's profile signature. Current MCP tool: `set_user_signature`.

## Get User Presence

```javascript
const result = await api.getUserPresence(auth, userId)
```

Returns online/presence status for a user.

## Get User Relation

```javascript
const result = await api.getUserRelation(auth, userHashId)
// result.relationType: 1 = connected
```

| Param | Type | Description |
|-------|------|-------------|
| `userHashId` | number | Target user hash ID (numeric, not string) |

**Note**: Takes `userHashId`, not `userId`. Current MCP tool: `get_user_relation`.

## Save Contact Alias

```javascript
await api.saveContactAlias(auth, userId, { alias?, memo?, memoImageKey? })
```

| Param | Type | Description |
|-------|------|-------------|
| `userId` | string | Target user ID |
| `alias` | string? | Alias name |
| `memo` | string? | Memo/note text |
| `memoImageKey` | string? | Image key for memo |

## Sync Contact Info

```javascript
await api.syncContactInfo(auth, userId)
```

Call after `saveContactAlias()` to sync updated contact data.

## Deprecated User Lookup

```javascript
const result = await api.getUserById(auth, userId)
```

Deprecated — prefer `getUserInfoById()` because it works for both regular users and bots.
