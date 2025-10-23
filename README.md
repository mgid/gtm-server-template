## Guide: MGID Server-Side Pixel

This guide outlines the implementation of the MGID Server-Side Pixel using **two separate tags** to manage the `clickId` for proper conversion tracking.

### 1\. Step 1: Capture and Save `clickId` (Page View Tag)

The primary goal of the first tag is to save the MGID `clickId` to a cookie when a user lands on the website. This cookie is then used later for conversion reporting.

#### A. Create the MGID Page View Tag

1. **Go to Tags** in your GTM Server-Side container and click **New**.
2. **Tag Configuration:**
   * **Choose Tag Type:** Select the **MGID Server Side Pixel** template.
   * **Event Type:** Choose **page_view** from the dropdown. This is crucial for initial click ID capture.
3. **Triggering:**
   * **Choose a Trigger:** Select the trigger that fires on all page views or on the specific pages where you expect the MGID traffic to land and the `clickId` to be present.
     * *Common Triggers:* The built-in **`All Pages`** trigger or a custom trigger for the **`page_view`** event.
4. **Save** the tag (e.g., *MGID - Page View - ClickID Capture*).

**Context:** When this tag fires, it looks for the MGID `clickId` (e.g. `adclid`) and uses the template's built-in functionality to **set a first-party cookie** on the user's browser, storing that value in `MgidSensorClid`.

---

### 2\. Step 2: Send Conversion Postback (Conversion Tag)

The second tag is triggered when a specific action (conversion) occurs. It reads the stored `clickId` from the cookie and sends the  postback data to the MGID server(https://a.mgid.com/postback).

#### A. Ensure Conversion Data is Available

Before creating the tag, ensure your client-side GTM container is sending the appropriate conversion data to the server container.

* This usually means sending an event (e.g., `purchase`, `signup`, `form_submit`) to your Server Container.

#### B. Create the MGID Conversion Tag

1. **Go to Tags** in your GTM Server-Side container and click **New**.
2. **Tag Configuration:**
   * **Choose Tag Type:** Select the **MGID Server Side Pixel** template.
   * **Event Type:** Choose **conversion** from the dropdown. This signals to the template that a postback should be sent.
   * **Event Name:** Map this field to the corresponding variable from your incoming event data (e.g., using a **`Event Data`** variable type in GTM).
   * **Conversion Value:** Add the revenue cost for this conversion.
3. **Triggering:**
   * **Choose a Trigger:** Select the specific custom trigger that corresponds to your conversion event name (e.g., *Custom Event - purchase*).
4. **Save** the tag (e.g., *MGID - Conversion - Purchase*).

**Context:** When this tag fires, the template automatically **extracts the `clickId` from the cookie** (saved in Step 1) and includes it in the postback request sent to MGID, thus attributing the conversion correctly.
