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

Works for both regular users and bots. This is the **recommended** method for looking up users.

## Get User Profile Card

```javascript
const result = await api.getUserProfileCard(auth, userId)
```

Returns profile card data: description/signature, display name, avatar key, profile field policies.

**Note**: `userId` must be a 15-20 digit numeric string.

## Get User by ID (deprecated)

```javascript
const result = await api.getUserById(auth, userId)
```

**Deprecated** — use `getUserInfoById` instead. This only works for regular users, not bots.

Returns: `{ success, data: { userId, name, enName, avatarUrl, avatarKey } }`

## Set User Signature

```javascript
const result = await api.setUserSignature(auth, description, options?)
```

| Param | Type | Description |
|-------|------|-------------|
| `description` | string | Signature text |
| `options.descriptionFlag` | number? | Signature flag (default: 0) |

Sets the current authenticated user's profile signature.

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

**Note**: Takes `userHashId` (a number), not `userId` (a string).

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

```javascript
await api.saveContactAlias(auth, '7441015090323947523', { alias: 'Tom' });
await api.saveContactAlias(auth, '7441015090323947523', { memo: 'Met at conference' });
```

## Sync Contact Info

```javascript
await api.syncContactInfo(auth, userId)
```

Call after `saveContactAlias()` to sync updated contact data. This is part of the standard flow:

1. `saveContactAlias()` — save alias
2. `getUserInfoById()` — refresh user data
3. `syncContactInfo()` — sync to server
