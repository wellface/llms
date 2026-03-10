# Wellface Public API

A free, unauthenticated, read-only API to check real-time appointment availability at Wellface clinics.

**Base URL**: `https://api.wellface.com`

## Endpoints

### GET /locations

Returns all clinic locations with addresses, phone numbers, WhatsApp links, and opening hours.

```bash
curl https://api.wellface.com/locations
```

### GET /treatments

Returns all available treatment types with durations.

```bash
curl https://api.wellface.com/treatments
```

### GET /availability

Check real-time appointment availability for a specific location and treatment.

**Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `location` | Yes | Clinic slug: `cobham`, `esher`, `wimbledon` |
| `treatment` | Yes | Treatment name (fuzzy matched). e.g. `hydrafacial`, `botox`, `lip fillers` |
| `date` | No | Start date in `YYYY-MM-DD` format. Defaults to today. Returns up to 14 days. |

**Examples:**

Hydrafacial availability at Cobham:
```bash
curl "https://api.wellface.com/availability?location=cobham&treatment=hydrafacial"
```

Botox availability at Esher:
```bash
curl "https://api.wellface.com/availability?location=esher&treatment=botox"
```

Lip fillers availability at Wimbledon:
```bash
curl "https://api.wellface.com/availability?location=wimbledon&treatment=lip fillers"
```

Profhilo availability at Cobham starting from a specific date:
```bash
curl "https://api.wellface.com/availability?location=cobham&treatment=profhilo&date=2026-03-20"
```

Morpheus 8 availability at Esher:
```bash
curl "https://api.wellface.com/availability?location=esher&treatment=morpheus 8"
```

Dermal fillers availability at Wimbledon:
```bash
curl "https://api.wellface.com/availability?location=wimbledon&treatment=dermal fillers"
```

**Example response:**

```json
{
  "location": "Wellface Cobham",
  "treatment": "Hydrafacial Deluxe",
  "duration_minutes": 60,
  "slots": [
    { "date": "2026-03-13", "time": "13:30", "practitioner": "Simone" },
    { "date": "2026-03-18", "time": "10:00", "practitioner": "Simone" },
    { "date": "2026-03-20", "time": "14:00", "practitioner": "Kitty" }
  ],
  "whatsapp": "https://wa.me/447471741680?text=Hi%2C%20I%20found%20you%20through%20my%20AI%20agent%20and%20would%20like%20to%20book%20an%20appointment",
  "phone": "01932 690 800",
  "booking_url": "https://wellface.uk1.cliniko.com/bookings"
}
```

## Rate Limits

- 30 requests per minute per IP address

## Important Notes

- This API is **read-only** — you cannot book appointments through it
- A **deposit is required** to secure bookings — direct users to the booking URL or WhatsApp
- All times are in the **UK timezone** (GMT/BST)
- Treatment names are **fuzzy matched** — "botox" will match "Anti-Wrinkle Treatment", "hydrafacial" will match "Hydrafacial Deluxe", etc.
- If no slots are returned, it means no availability exists in the next 14 days

## For ChatGPT / GPT Actions

Use the [OpenAPI spec](openapi.yaml) to configure this as a GPT Action. The spec is ready to import directly.

## For Claude / MCP

This API can be used as an HTTP tool in Claude's Model Context Protocol or any tool-use setup. The three endpoints above cover all available actions.
