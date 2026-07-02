# Task 03: HubSpot & Martech Integration Architecture

## 1. End-to-End Flow

1. User fills out the consultation form (Name, Phone, Clinic Preference) and clicks **Submit**.
2. The frontend sends the form data to the backend using an **HTTPS POST API**.
3. The backend validates the submitted data.
4. The backend searches the **HubSpot CRM API** using the patient's phone number.
5. If the phone number already exists, the existing contact is **updated**.
6. If the phone number does not exist, a **new contact** is created with:
   - Name
   - Phone
   - Clinic Preference
   - Source = **Google Ads - Consultation Landing Page**
   - Lead Status = **New Enquiry**
7. After the contact is saved successfully, the backend calls the **Karix WhatsApp Business API** to send a confirmation message.
8. At the same time, the frontend fires:

```javascript
window.dataLayer.push({
  event: "consultation_form_submitted"
});
```

9. **Google Tag Manager (GTM)** captures the event and sends it to **GA4** and **Google Ads** for conversion tracking.

---

## 2. Biggest Failure Point & Fallback

### Biggest Failure Point

- HubSpot does not automatically deduplicate contacts using **phone numbers**.
- Duplicate contacts may be created if the same patient submits the form multiple times.

### Solution

- Search HubSpot using the phone number before creating a contact.
- If the contact exists, update it.
- Otherwise, create a new contact.
- If HubSpot is temporarily unavailable, store the lead in a temporary queue/database and retry later so no enquiry is lost.

---

## 3. Monitoring WhatsApp Delivery

### Possible Issues

- Network problems
- Karix API downtime
- Server errors
- Slow API response

### Monitoring & Solution

- Log every WhatsApp API request and response.
- Automatically retry failed requests.
- Monitor message delivery time.
- Trigger an alert if the confirmation message is not delivered within **2 minutes**.

---

## Integration Flow Diagram

```text
User
   │
   ▼
Landing Page
   │
   ▼
Backend API
   │
   ├──────────────► HubSpot CRM API
   │                  │
   │                  ├── Contact Exists → Update
   │                  └── New Contact → Create
   │
   ├──────────────► Karix WhatsApp API
   │                  │
   │                  └── Confirmation Message
   │
   └──────────────► window.dataLayer.push()
                          │
                          ▼
                   Google Tag Manager
                          │
                          ▼
                         GA4
                          │
                          ▼
                     Google Ads
```