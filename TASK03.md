# Task 03: HubSpot & Martech Integration Architecture

### 1. End-to-End Architectural Flow
The integration uses a Direct API Webhook Framework via an Automation Engine (Make/Zapier).

* **Payload Capture:** The vanilla JavaScript form intercepts submission, executes the local GTM dataLayer push, and forwards the raw payload (name/phone) via a native fetch() POST request to a webhook handler.
* **Data Transformation:** The handler standardizes the phone number into E.164 format (e.g., adding +91).
* **HubSpot Upsert Execution:** The router executes a lookup step inside HubSpot via the HubSpot Contacts Search API using the phone number property.
* **Conditional Routing:** If the contact exists, the workflow updates the existing record via a PATCH request (`Lead Status = 'New Enquiry'`). If not, it fires a POST request to create a fresh contact record.
* **Downstream Actions:** Concurrently, the workflow triggers the WhatsApp transactional template execution on Karix.

---

### 2. The Single Biggest Failure Point & Fallback Strategy
* **The Core Failure Point:** HubSpot Duplicate Contamination[cite: 1]. HubSpot natively deduplicates entries using the Email Address property[cite: 1]. Because this landing page skips email collection to maximize conversions, standard integrations will blindly spawn a duplicate record if a patient submits again[cite: 1].
* **The Architectural Fallback:** We eliminate this completely by enforcing an explicit API Search-and-Upsert Logic Gate before pushing data to HubSpot[cite: 1]. The script queries `/crm/v3/objects/contacts/search` matching against the phone property to find the absolute internal recordId[cite: 1]. A secondary Google Sheet or local database log captures the raw webhook stream as a backup queue[cite: 1].

---

### 3. Protecting the 2-Minute WhatsApp SLA & Monitoring
* **SLA Breakers:** Karix delivery pipeline failure due to carrier network congestion, upstream API token expirations, or rate-limiting overages[cite: 1].
* **Monitoring & Resolution Protocol:** We separate form receipt from the messaging engine[cite: 1]. The webhook stores the request and queues the message[cite: 1]. If Karix throws a 429 or 503 error, the queue uses an automatic exponential backoff retry mechanism (retrying at 15s, 45s, and 90s)[cite: 1]. If it fails beyond 90 seconds, it routes to a Dead-Letter Queue to trigger an instant high-priority Slack/SMS alert to the dev team[cite: 1].