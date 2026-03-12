# illumin Universal Pixel — Google Tag Manager Template

This template lets you deploy the [illumin](https://illumin.com/) Universal Pixel through Google Tag Manager without writing any code. It loads the pixel script once and fires tracking events with the parameters you configure in the GTM UI.

## Quick Start

1. In Google Tag Manager, go to **Templates → Tag Templates → Search Gallery** and add the **illumin Universal Pixel** template.
2. Create a new tag using the template.
3. Enter your **Pixel Key** (required).
4. Optionally fill in the event parameters below.
5. Choose a trigger (e.g. *All Pages*, or a specific event) and publish.

## Parameters

| Parameter | GTM Field | Required | Type | Description |
|-----------|-----------|----------|------|-------------|
| `pixelKey` | Pixel Key | **Yes** | Text | Unique pixel identifier assigned by illumin when creating a Universal Pixel (e.g. `"9180448253778427401"`). Found in the illumin platform under your pixel settings. |
| `pg` | Page Group ID | No | Number | Numeric identifier for the page event mapping created in illumin (e.g. `49135`). Each mapping corresponds to specific on-site actions you want to track. |
| `prodid` | Product ID | No | Text | Advertiser Product ID used for dynamic cart tracking. Supports multiple IDs — when an array is provided the pixel joins them with a `~` separator. |
| `ordid` | Order ID | No | Text | Advertiser Order ID used for conversion and order tracking. |
| `crev` | Cart Revenue | No | Text | Cart revenue value associated with the conversion event. |
| `delay` | Delay (ms) | No | Number | Delay in milliseconds before the pixel fires. Useful for ensuring page data is available before the event is sent. |

## How It Works

### Script Loading

The template mirrors the standard illumin integration snippet:

1. **First tag fire** — creates a command queue (`window.aap` → `window.acuityAdsEventQueue`), queues the event, sets up the piggyback callback, and injects `https://origin.acuityplatform.com/event/v2/pixel.js`. Once the script loads it drains the queue and replaces `window.aap` with the real fire function.
2. **Subsequent tag fires** — detects that `window.aap` already exists (either from a previous tag instance or a hard-coded snippet on the page) and calls it directly without re-injecting the script.

This means you can safely place multiple instances of the tag on the same page (e.g. one on *All Pages* with just a Pixel Key, and another on a purchase confirmation with cart parameters).

### Event Data

Only parameters that you fill in are included in the event object sent to the pixel. An empty optional field is simply omitted — the pixel never receives `undefined` or blank values.

## Examples

### Basic Pageview Tracking

| Field | Value |
|-------|-------|
| Pixel Key | `9180448253778427401` |

Trigger: *All Pages*

### Page Event Mapping

| Field | Value |
|-------|-------|
| Pixel Key | `3552746322991042929` |
| Page Group ID | `49135` |

Trigger: A custom event or specific page trigger.

### Conversion with Cart Data

| Field | Value |
|-------|-------|
| Pixel Key | `9180448253778427401` |
| Page Group ID | `49135` |
| Product ID | `SKU-123` (or use a GTM Variable that returns an array for multiple products) |
| Order ID | `ORD-456` |
| Cart Revenue | `29.99` |

Trigger: Purchase / Thank You page.

### Delayed Pixel Fire

| Field | Value |
|-------|-------|
| Pixel Key | `9180448253778427401` |
| Delay (ms) | `1000` |

Trigger: Any — the pixel will wait 1 second before firing.

## Using GTM Variables for Dynamic Values

Any field can accept a GTM Variable (e.g. a Data Layer Variable or Custom JavaScript Variable) instead of a static value. This is the recommended approach for dynamic parameters like Product ID, Order ID, and Cart Revenue.

For example, to pull `ordid` from the data layer:

1. Create a **Data Layer Variable** named `transaction.orderId`.
2. In the tag configuration, set the **Order ID** field to `{{transaction.orderId}}`.

## Permissions

This template requires the following GTM sandbox permissions:

- **Injects Scripts** — loads `https://origin.acuityplatform.com/event/v2/pixel.js`
- **Accesses Global Variables** — reads/writes `aap`, `acuityAdsEventQueue`, `acuityPiggybackCallback`, and `acuityParseResponse`

## Support

For issues with the template itself, please open an issue on the [GitHub repository](https://github.com/illumindev/illumin-universal-pixel-gtm-template).

For illumin platform questions, visit [illumin.com](https://illumin.com/).
