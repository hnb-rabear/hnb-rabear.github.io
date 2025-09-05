# Analysis of Balancy.co for In-Game Messaging System

This document provides an analysis of how Balancy.co's features meet the specified requirements for an in-game messaging and LiveOps system.

## 1. Functional Requirements

### 1.1. Core Messaging Functionality

*   **Display Messages:** Messages can be displayed as prominent overlays by using **Game Offers** to trigger a UI popup. The message content (text, images) is linked to the offer using the **Attachments** feature. A **Game Event** is used to activate this offer at the desired moment, such as on app open.
*   **Targeting User Segments:**
    *   **Payer Status:** Target users with **Conditions** based on built-in User Properties like `System ▸ Payments ▸ Payments Count` (e.g., equal to 0 for non-payers, greater or equal to 1 for payers). Balancy's automated **RFMM segmentation** can also be used.
    *   **New Users:** Target based on the `System ▸ General Info ▸ Session` property (e.g., session <= 5) or the `IsNewUser` flag.
    *   **Returning Users:** Target using the `System ▸ General Info ▸ Last Login Time` property to identify users who have been inactive for a specific duration.
*   **Call-to-Action (CTA):**
    *   **Internal & External Links:** CTA functionality is implemented by attaching custom data (e.g., a deep link path or an external URL) to a Game Offer. Client-side code reads this data upon offer activation to create buttons with the correct actions.
    *   **Dismiss:** Dismissal is a client-side UI implementation. In Balancy's Visual Scripting, closing a message without action triggers the `Ended` exit port on the `Activate Offer` node, allowing you to handle the dismissal logic.
*   **Scheduling and Expiry:**
    *   **Start/End Date:** Use the **`Dates Range`** condition on a Game Event to set a specific start and end date/time for a message to be active.
    *   **Immediate Display:** A Game Event with no conditions will trigger immediately.
*   **Frequency Capping:** Frequency capping is configured using **Visual Scripting**.
    *   **Per-User / Per-Campaign:** Use a custom User Property as a counter. The script checks this counter before showing a message and increments it afterward.
    *   **Show Once:** Use the `Was Item Purchased` node in a Visual Script. A "marker" item is granted when the message is shown, and the script checks for this marker to prevent showing the message again.
*   **A/B Testing:** Balancy has a built-in A/B testing feature.
    *   **Percentage-Based Delivery:** In a Visual Script, the **`AB Test`** node splits the target audience into different groups based on configured weights (e.g., 20% to Group A, 80% to Group B), allowing you to show different message variants to each group.

### 1.2. Administrative Interface (CMS)

*   **User Interface:** Balancy provides a comprehensive, web-based administrative panel (CMS) for managing all game content and LiveOps.
*   **Message Creation:** The CMS allows for the creation and editing of messages by combining Game Events, Game Offers, and Visual Scripts. This includes setting the message content, targeting rules, scheduling, and frequency capping logic.
*   **Message Management:** The CMS includes a dashboard with an **In-Game Events Calendar** and filterable lists to view all active, scheduled, and past messages.
*   **Reporting Dashboard:** The CMS includes a dashboard to view performance metrics for messages and A/B tests.

---

## 2. Non-Functional Requirements

*   **Performance and Reliability:**
    *   **Minimal Latency:** The SDK is designed for negligible impact on app launch time through asynchronous, non-blocking data loading and efficient updates via a global CDN.
    *   **Offline Support:** The system works offline by using locally cached data. User profiles are saved locally and synced automatically when a connection is restored.
    *   **Scalability:** Balancy uses a global CDN (Cloudflare, Cloudfront) and an efficient data structure to handle a large number of concurrent users.
    *   **Fault Tolerance:** If the server is down, the SDK gracefully falls back to cached data to avoid disrupting the user experience.
*   **Security:**
    *   **API Authentication:** Endpoints are secured via private keys. Server-to-server calls use a robust HMAC-SHA256 signature process to prevent unauthorized access.
    *   **Data Integrity:** The system includes built-in IAP receipt validation for both Google Play and the iOS App Store to protect against fraud.
*   **Platform Compatibility:**
    *   **Cross-Platform:** The core SDK is written in C++ for cross-platform compatibility, with explicit support for both **iOS** and **Android**.

---

## 3. Data and Analytics Requirements

*   **Event Logging:** Balancy provides a system of callbacks (`ISmartObjectsEvents`) that allow you to capture key events and forward them to external analytics platforms.
    *   `message_shown`: Log this event using the `OnNewOfferActivated` callback.
    *   `message_dismissed`: Log this event using the `OnOfferDeactivated` callback, which indicates if the offer was purchased or ignored.
    *   `message_cta_tapped`: This client-side UI interaction should be logged from your game's code when the user taps the button.
*   **Reporting Dashboard (in CMS):** Balancy's **Visual Scripting analytics** display key metrics directly on your logic flows.
    *   **Impressions:** Measured by the user count on the path leading into an `Activate Offer` node.
    *   **Dismissals & Click-Throughs:** Measured by the user counts on the `Ended` (dismissed) and `Purchased` (clicked-through) exit ports of the `Activate Offer` node.
    *   **Click-Through Rate (CTR):** Can be easily calculated from the impression and click-through numbers provided.
*   **User Segmentation Tracking:** The system automatically tracks user metadata like `Total Spend` and `PaymentsCount`, which can be used for targeting. You can also define and send custom user properties.
*   **A/B Test Results:** The results for A/B tests are clearly visible within the CMS, showing a performance breakdown for each variant and allowing you to roll out the winner to all users.

---

## 4. Balancy.co? Build vs. Buy Recommendation

**Recommendation: Implement Balancy.co.**

Building a custom solution with the required level of functionality would be a massive, resource-intensive undertaking. Balancy is a specialized platform that already provides a comprehensive and mature solution for all the specified requirements.

*   **Cost and Time Efficiency:** Balancy offers a ready-to-use solution that would take a dedicated team years and significant financial investment to build from scratch.
*   **Empowerment of Non-Technical Teams:** The platform is designed for game designers and product managers, enabling them to manage content, run events, and analyze results independently. This drastically increases operational speed and agility.
*   **Complete Feature Set:** Balancy directly addresses every requirement, from the user-friendly CMS, powerful segmentation, and Visual Scripting engine to the integrated A/B testing and analytics dashboards.
*   **Reduced Risk and Maintenance:** By leveraging a battle-tested, scalable, and actively maintained platform, you avoid the risks and overhead associated with developing and supporting a custom backend system.

The decision heavily favors "buy" in this scenario, as Balancy.co aligns perfectly with the project's needs, offering a powerful, flexible, and efficient path to achieving your goals.