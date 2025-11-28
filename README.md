
# MGID Server-Side Pixel for Google Tag Manager

[![GTM Server-Side](https://img.shields.io/badge/GTM-Server--Side-blue.svg)](https://developers.google.com/tag-platform/tag-manager/server-side)
[![License](https://img.shields.io/badge/License-Apache%202.0-green.svg)](LICENSE)
[![MGID](https://img.shields.io/badge/Platform-MGID-red)](https://www.mgid.com)

This repository contains the official **Server-Side Google Tag Manager (sGTM) Template** for MGID. It allows advertisers to send conversion data securely from their server directly to MGID endpoints, bypassing client-side limitations and ensuring accurate attribution.

## 🚀 Key Features

* **First-Party Cookie Support:** Sets HTTP-only/Secure cookies (`MgidSensorClid`) to persist attribution data despite ITP/ETP browser restrictions.
* **Ad Blocker Resistance:** Server-to-server communication is less likely to be blocked than client-side scripts.
* **Secure Data Transmission:** Hides sensitive logic and API endpoints from the client browser.
* **Dual Mode:** Handles both click storage (Landing Page) and conversion reporting (Thank You Page).

---

## 📋 Prerequisites

Before installing, ensure you have the following:

1.  **Google Tag Manager Server Container:** [Official Setup Guide](https://developers.google.com/tag-platform/tag-manager/server-side).
2.  **Custom Domain Configuration (CRITICAL):**
    * Your Server Container **must** run on a subdomain of your main website (e.g., website: `example.com`, sGTM: `metrics.example.com`).
    * *Why?* If the domains do not match, browsers will block the attribution cookie as "Third-Party," breaking tracking.
    * [Official Domain Mapping Guide](https://developers.google.com/tag-platform/tag-manager/server-side/custom-domain).

---

## 📦 Installation

### Option 1: Community Gallery (Recommended)
Search for **"MGID Server-Side Pixel"** in the GTM Template Gallery and add it to your workspace.

### Option 2: Manual Import
1.  Download the [`template.tpl`](./template.tpl) file from this repository.
2.  Open your GTM Server Container.
3.  Navigate to **Templates** > **New**.
4.  Click the **⋮** (Menu) icon in the top right > **Import**.
5.  Select the `template.tpl` file and **Save**.

---

## 🔧 Configuration & Usage

This template functions in two modes. You will typically need to create **two separate tags**.

### 1. Landing Page Tag (Store the Click)
*Captures the user when they arrive from an ad.*

* **Tag Type:** MGID Server-Side Pixel
* **Event Type:** `PageView`
* **Trigger:** Fire on **All Page Views** (or specifically where your Client, e.g., GA4, claims the request).

> **Behavior:** The tag scans the URL for `adclid`, the Referer header, or existing cookies. If a click ID is found, it is saved to the `MgidSensorClid` cookie (1-year expiry).

### 2. Conversion Tag (Send the Data)
*Captures the user when they convert.*

* **Tag Type:** MGID Server-Side Pixel
* **Event Type:** `Conversion`
* **Event Name:** Your goal name (e.g., `purchase`, `lead`).
* **Conversion Value:** (Optional) Static value or variable (e.g., `{{Event Data - Value}}`).
* **Trigger:** Fire on your conversion event (e.g., Event Name = `purchase`).

> **Behavior:** The tag retrieves the Click ID from the `MgidSensorClid` cookie and sends a server-to-server postback to `https://a.mgid.com/postback`.

---

## 🧪 Debugging

The template includes built-in logging that is only active during **GTM Preview Mode**.

1.  Click **Preview** in your Server Container.
2.  Trigger an event on your website.
3.  Select the request in the Preview Debugger.
4.  Check the **Console** tab for logs prefixed with `=== MGID Server Side GTM ===`.

| Log Message | Meaning |
| :--- | :--- |
| `Click ID found in page_location` | Success. ID extracted from URL. |
| `Storing Click ID in cookie` | Success. Cookie set/updated. |
| `Postback sent successfully` | Success. API returned 200 OK. |
| `ERROR: Click ID is required...` | Failure. No ID found in Cookie or URL. |

---

## 🛡️ Permissions

This template requires specific permissions to function sandbox-safe:
* `access_response` (to set cookies).
* `read_event_data` (to parse `page_location`).
* `read_request` (to read `referer`).
* `send_http` (Allowed URL: `https://a.mgid.com/*`).

## 📄 License

Distributed under the Apache 2.0 License. See `LICENSE` for more information.
