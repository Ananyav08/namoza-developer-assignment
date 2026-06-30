# Task 01: GTM Event Schema

### Core Interaction Event Schema

| Event Name | Trigger Type | Key Parameters (Min. 3) | GA4 Report / Audience Destination |
| :--- | :--- | :--- | :--- |
| `form_start` | Element Visibility | `form_id`, `form_name`, `clinic_location` | Engagement > Events |
| `booking_step_complete` | Custom Event | `step_number`, `step_name`, `clinic_location`, `specialty` | Funnel Exploration |
| `appointment_booked` | Custom Event | `transaction_id`, `clinic_location`, `specialty`, `lead_status` | Conversions / Audiences |
| `click_to_call` | Just Links | `click_url`, `click_text`, `page_location` | Engagement > Events |
| `whatsapp_widget_click` | Just Links | `click_url`, `page_location`, `widget_type` | Engagement > Events |
| `patient_guide_download` | Custom Event | `file_name`, `file_extension`, `lead_name_hashed` | Engagement > Downloads |
| `clinic_page_view` | Page View | `clinic_city`, `clinic_name`, `page_path` | Engagement > Pages and screens |
| `blog_scroll_depth` | Scroll | `percent_scrolled`, `page_title`, `post_category` | Engagement > Events |

---

### Multi-Step Booking Form Funnel Tracking

#### Step 1: Location & Specialty Selected (Fires when moving to Step 2)
```json
{
  "event": "booking_step_complete",
  "step_number": 1,
  "step_name": "location_specialty_selected",
  "clinic_location": "Indiranagar_Bengaluru",
  "specialty": "Orthopaedics_Knee"
}