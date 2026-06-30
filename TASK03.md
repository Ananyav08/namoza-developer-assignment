# Task 03: HubSpot & Martech Integration Architecture

## 1. End-to-End Flow

When a user submits the consultation form, JavaScript sends the form data (name and phone number) to the backend using an API request.

The backend validates the data and checks whether the phone number already exists in HubSpot. If the contact already exists, the record is updated. Otherwise, a new contact is created.

After the contact is successfully saved, a WhatsApp confirmation message is sent through Karix to acknowledge the enquiry.

At the same time, Google Tag Manager records the form submission event, which is then available in Google Analytics for conversion tracking and reporting.

---

## 2. Biggest Failure Point & Solution

One common issue is duplicate contact records if the same patient submits the form more than once.

To prevent this, the backend first checks whether the phone number already exists in HubSpot. If it exists, the existing contact is updated instead of creating a new one.

If HubSpot is temporarily unavailable, the form data can be stored temporarily and processed once the service is available again.

---

## 3. Monitoring WhatsApp Delivery

The system should monitor whether the WhatsApp confirmation message is delivered successfully.

If the message fails due to a temporary network or server issue, it should automatically retry after a short delay.

If the message still cannot be delivered after multiple attempts, the failure should be logged and the development team should be notified so that the issue can be resolved quickly.