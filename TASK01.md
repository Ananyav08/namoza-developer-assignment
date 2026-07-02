# Task 01: GTM Event Schema

## Core Interaction Event Schema

| Event Name | GTM Trigger Type | Key Parameters | GA4 Report / Audience |
|------------|------------------|----------------|-----------------------|
| form_start | Custom Event (Input Focus) | form_name, clinic_location, page_location | Engagement → Events |
| booking_step_complete | Custom Event | step_number, step_name, clinic_location, specialty | Funnel Exploration |
| appointment_booked | Custom Event | booking_id, clinic_location, specialty, booking_status | Conversions, Audiences |
| click_to_call | Just Links | phone_number, clinic_location, page_location | Engagement → Events |
| whatsapp_widget_click | Just Links | page_location, clinic_location, button_position | Engagement → Events |
| patient_guide_download | Custom Event | guide_name, phone_hash, clinic_location | Engagement → Downloads |
| clinic_page_view | Page View | clinic_name, clinic_city, page_path | Pages & Screens Report |
| blog_scroll_depth | Scroll Depth Trigger | percent_scrolled, page_title, post_category | Engagement Report |

---

# Multi-Step Booking Funnel Tracking

## Step 1 – Select Clinic & Specialty

### GTM Trigger
Custom Event Trigger

Event Name

booking_step_complete

### dataLayer Push

```json
{
  "event": "booking_step_complete",
  "step_number": 1,
  "step_name": "location_specialty_selected",
  "clinic_location": "Indiranagar",
  "specialty": "Orthopaedics"
}
```

---

## Step 2 – Patient Details Entered

### GTM Trigger
Custom Event Trigger

Event Name

booking_step_complete

### dataLayer Push

```json
{
  "event": "booking_step_complete",
  "step_number": 2,
  "step_name": "patient_details_entered",
  "clinic_location": "Indiranagar",
  "patient_name": "John Doe",
  "preferred_date": "2026-07-15"
}
```

---

## Step 3 – Booking Confirmed

### GTM Trigger
Custom Event Trigger

Event Name

appointment_booked

### dataLayer Push

```json
{
  "event": "appointment_booked",
  "booking_id": "BK10254",
  "step_number": 3,
  "clinic_location": "Indiranagar",
  "specialty": "Orthopaedics",
  "booking_status": "Confirmed"
}
```

---

# Funnel Drop-Off Tracking in GA4

GA4 Funnel Exploration will be configured using the following sequence:

```
Booking Started
        ↓
booking_step_complete
(step_number = 1)

        ↓
booking_step_complete
(step_number = 2)

        ↓
appointment_booked
```

Example Funnel

```
100 Users Started

↓

78 Completed Step 1

↓

56 Completed Step 2

↓

31 Appointment Booked
```

This allows the marketing team to identify where users abandon the booking process and optimize the highest drop-off stage.

---

# GTM Trigger Configuration

| Step | Trigger Type | Trigger Name | Fires When |
|------|--------------|--------------|------------|
| Step 1 | Custom Event | booking_step_complete | User selects clinic and specialty |
| Step 2 | Custom Event | booking_step_complete | User enters name, phone and preferred date |
| Step 3 | Custom Event | appointment_booked | Booking confirmation is successful |

---

# Google Ads Conversion

### Conversion Event

```
appointment_booked
```

### Why this conversion?

I would import **appointment_booked** into Google Ads because it represents a completed appointment, which is the primary business goal. Events such as **form_start**, **click_to_call**, or **WhatsApp click** indicate user engagement but do not guarantee a successful booking. Optimizing campaigns around completed appointments provides higher-quality conversion data and better bidding performance.

---

# Frontend & GTM Responsibility

The frontend developer is responsible for firing the appropriate `window.dataLayer.push()` events whenever a booking step is completed.

Google Tag Manager listens for these custom events using Custom Event Triggers and forwards the data to GA4. GA4 then stores the events and makes them available for reporting, Funnel Exploration, audience creation, and Google Ads conversion tracking.

Flow Chart

```text
                                                   Frontend
                                                       ↓
                                             window.dataLayer.push()
                                                       ↓
                                               Google Tag Manager
                                                       ↓
                                                      GA4
                                                       ↓
                                      Reports / Funnel Exploration / Google Ads
```
