# Update Modal Remote Config Schema

This document defines the full schema used to power the Blinq Update / Changelog modal.

The config is delivered remotely (JSON) and rendered dynamically by the client.

---

# Root Structure

```json
{
	"schemaVersion": 3,
	"configVersion": "2026.02.11",
	"featuredUpdateId": "2026-02-11-portfolio-v2",
	"historyUrl": "https://blinq.fi/changelog",
	"updates": []
}
```

---

## Root Fields

### schemaVersion (number) — REQUIRED

Used to detect breaking schema changes.

- Increment only when structure changes.
- Client should reject unsupported versions.

---

### configVersion (string) — REQUIRED

Used for cache invalidation.

Recommended format:

```
YYYY.MM.DD
```

---

### featuredUpdateId (string) — OPTIONAL

Specifies which update should be prioritized.

Must match `updates[].id`.

---

### historyUrl (string) — OPTIONAL

URL to full changelog page.

---

### updates (Update[]) — REQUIRED

Array of update objects.

---

# Update Object

```json
{
	"id": "2026-02-11-portfolio-v2",
	"version": "1.4.0",
	"title": "Portfolio V2",
	"description": "Track holdings, PnL, and positions — all in one place.",
	"publishedAt": "2026-02-11T00:00:00Z",
	"expiresAt": "2026-03-15T00:00:00Z",
	"enabled": true,
	"priority": 1,
	"media": {},
	"rules": {},
	"theme": {},
	"analytics": {},
	"slides": [],
	"cta": {}
}
```

---

## Update Fields

### id (string) — REQUIRED

Unique identifier.

Example:

```
"2026-02-11-portfolio-v2"
```

---

### version (string) — REQUIRED

App version associated with this update.

---

### title (string) — REQUIRED

Main modal title. Used as fallback when a slide has no title.

---

### description (string) — REQUIRED

Short description shown in modal. Used as fallback when a slide has no body.

> **Backward compatibility:** The client also accepts `"body"` as an alias for `"description"` to support v1/v2 configs.

---

### publishedAt (ISO string) — REQUIRED

UTC timestamp.

---

### expiresAt (ISO string) — OPTIONAL

If current date > expiresAt, the update must not render.

---

### enabled (boolean) — REQUIRED

Controls whether update is active.

---

### priority (number) — REQUIRED

Lower number = higher priority.

---

### media (Media) — OPTIONAL

Fallback media for slides that don't specify their own.

If a slide has no `media`, the renderer falls back to this update-level media object.

---

# Rules Object

```json
{
	"minAppVersion": "1.3.0",
	"showOnce": true,
	"dismissible": true,
	"platforms": ["web"],
	"audience": {
		"roles": ["retail", "pro"],
		"minTradingVolumeUsd": 0
	}
}
```

---

## Rules Fields

### showOnce (boolean)

Default: `true`

If true:

- Store dismissal state locally.
- Do not show again after user dismisses.

---

### dismissible (boolean)

Default: `true`

If false:

- Clicking outside the modal does not close it.
- User must click the CTA button.

---

### minAppVersion (string)

Minimum app version required to show this update.

---

### platforms (string[])

Supported values:

- `"web"`
- `"mobile"`
- `"desktop"`

---

### audience.roles (string[])

Custom user roles.

Examples:

- `"retail"`
- `"pro"`
- `"beta"`

---

### audience.minTradingVolumeUsd (number)

Minimum required lifetime trading volume.

---

# Theme Object

```json
{
	"accent": "emerald",
	"backgroundVariant": "subtle-gradient",
	"layout": "media-right"
}
```

---

## accent (string)

Supported values:

- `"emerald"`
- `"blue"`
- `"purple"`
- `"orange"`
- `"red"`

Mapped internally to design tokens.

---

## backgroundVariant (string)

Supported values:

- `"subtle-gradient"`
- `"flat-dark"`
- `"glass"`

---

## layout (string)

Default slide layout.

Supported values:

- `"media-right"` (default)
- `"media-left"`
- `"full-width"`
- `"text-only"`

---

# Analytics Object (Update Level)

```json
{
	"impressionEvent": "update_viewed",
	"dismissEvent": "update_dismissed",
	"ctaEvent": "update_cta_clicked"
}
```

All fields optional but recommended.

---

# Analytics Object (Slide Level)

```json
{
	"viewEvent": "slide1_viewed"
}
```

Fired when the slide becomes active in the carousel.

---

# Slide Object

```json
{
	"id": "portfolio-slide-1",
	"layout": "media-right",
	"badge": {
		"label": "New",
		"tone": "success"
	},
	"title": "Unified Portfolio View",
	"subtitle": "Everything at a glance",
	"body": "See balances and PnL instantly.",
	"media": {},
	"analytics": {
		"viewEvent": "slide1_viewed"
	}
}
```

---

## Slide Fields

### id (string) — REQUIRED

Unique per slide.

---

### layout (string) — OPTIONAL

Overrides update-level `theme.layout`.

Same supported values as `theme.layout`.

---

### badge (object) — OPTIONAL

Visual label displayed above the slide title.

#### badge.label (string) — REQUIRED

Display text (e.g. `"New"`, `"Improved"`, `"Beta"`).

#### badge.tone (string) — OPTIONAL

Controls badge color. Supported values:

- `"success"` — green
- `"info"` — blue
- `"warning"` — amber
- `"danger"` — red
- `"neutral"` — gray (default)

---

### title (string) — OPTIONAL

Slide title. Falls back to `update.title` if omitted.

---

### subtitle (string) — OPTIONAL

Secondary text displayed below the title.

---

### body (string) — OPTIONAL

Slide description. Falls back to `update.description` if omitted.

Supports multi-line text (split by `\n`), which renders as bullet points.

---

### media (Media) — OPTIONAL

Slide media. Falls back to `update.media` if omitted.

---

### analytics (SlideAnalytics) — OPTIONAL

Per-slide analytics events.

---

# Media Object

```json
{
	"type": "video",
	"src": "https://cdn.blinq.fi/updates/demo.mp4",
	"fallbackSrc": "https://cdn.blinq.fi/updates/demo.jpg",
	"poster": "https://cdn.blinq.fi/updates/poster.jpg",
	"alt": "Demo showing portfolio",
	"aspectRatio": "16/9",
	"objectFit": "cover",
	"autoplay": true,
	"loop": true,
	"muted": true,
	"controls": false
}
```

---

## Media Fields

### type (string) — REQUIRED

Supported:

- `"image"`
- `"video"`
- `"gif"`

---

### src (string) — REQUIRED

URL to the media asset.

---

### fallbackSrc (string) — OPTIONAL

Static image URL used when `src` fails to load or is an example/placeholder URL.

For videos, this should be a screenshot or poster image.

---

### alt (string) — OPTIONAL

Alt text for accessibility. Applies to all media types.

---

### aspectRatio (string) — OPTIONAL

Format:

```
"16/9"
"4/3"
"1/1"
```

Renderer converts to CSS `aspect-ratio`. Default: `"16/9"`.

---

### objectFit (string) — OPTIONAL

CSS object-fit value for the media element.

Supported:

- `"cover"` (default)
- `"contain"`
- `"fill"`

---

### Video-only Fields

| Field    | Type    | Default | Description                    |
| -------- | ------- | ------- | ------------------------------ |
| poster   | string  | —       | Poster image URL               |
| autoplay | boolean | true    | Auto-play when slide is active |
| loop     | boolean | true    | Loop video playback            |
| muted    | boolean | true    | Muted by default               |
| controls | boolean | false   | Show native video controls     |

---

# CTA Object

```json
{
	"label": "Explore Portfolio",
	"action": {
		"type": "navigate",
		"payload": {
			"url": "/portfolio"
		}
	}
}
```

---

## label (string) — REQUIRED

Button text. Default fallback in client: `"Finish"`.

---

## action (object) — REQUIRED

### action.type (string) — REQUIRED

| Type         | Description                      | Payload               |
| ------------ | -------------------------------- | --------------------- |
| navigate     | Internal route navigation        | `{ url: string }`     |
| openExternal | Open external URL in new tab     | `{ url: string }`     |
| dismiss      | Close modal (no navigation)      | —                     |
| openModal    | Open another modal (future)      | `{ modalId: string }` |
| triggerEvent | Fire internal app event (future) | `{ event: string }`   |

### action.payload (object) — OPTIONAL

Additional data for the action. Shape depends on `action.type`.

---

## CTA Examples

Dismiss:

```json
{
	"label": "Got It",
	"action": {
		"type": "dismiss"
	}
}
```

Internal navigation:

```json
{
	"label": "Explore Portfolio",
	"action": {
		"type": "navigate",
		"payload": { "url": "/portfolio" }
	}
}
```

External link:

```json
{
	"label": "Read More",
	"action": {
		"type": "openExternal",
		"payload": { "url": "https://blog.blinq.fi/portfolio-v2" }
	}
}
```

---

# Rendering Requirements

Client must:

- Ignore expired updates (`expiresAt`)
- Respect `rules.minAppVersion`
- Respect `enabled` flag
- Respect `rules.showOnce` (persist locally, default `true`)
- Respect `rules.dismissible` (block outside-click when `false`)
- Support mixed media types (image, video, gif)
- Use `fallbackSrc` when primary `src` fails to load
- Apply `aspectRatio` to media container (default `16/9`)
- Apply `objectFit` to media element (default `cover`)
- Use video-specific fields (`autoplay`, `loop`, `muted`, `controls`) when available
- Render `badge` with correct tone styling
- Render `subtitle` below title when present
- Fall back to `update.title` / `update.description` / `update.media` when slide omits them
- Default layout to `"media-right"` if undefined
- Default accent to system default if invalid
- Track analytics events if provided

---

# Backward Compatibility

The client normalizer supports:

- `"body"` as an alias for `"description"` at the update level (v1/v2 compat)
- Missing `rules` object — defaults: `showOnce=true`, `dismissible=true`
- Missing `theme` / `analytics` — safely ignored

---

# Extensibility Notes

Schema is designed to support:

- A/B testing
- User segmentation
- Multi-platform rendering
- Remote experimentation
- Localization support (future extension)

---

End of schema documentation.
