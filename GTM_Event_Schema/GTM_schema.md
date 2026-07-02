# Orthonow- GTM_Event_Schema
## Prepare by: Aditya Nayan

## About This Document
This GTM Event Schema defines all user interactions to be tracked on the OrthoNow website.
It covers *event names, trigger types, key parameters, GA4 report/audience it feeds*.

# The Event Schema
| Event Name | Trigger Type | Key Parameters | GA4 Report |
|------------|--------------|----------------|------------|
| call_now_clicked | Click Trigger (Click URL contains tel:) | button_location, clinic_name, device_type | Events Report → Conversions |
| whatsapp_widget_clicked | Click Trigger (Click URL contains wa.me) | button_location, whatsapp_number, device_type | Events Report → Audience Builder |
| patient_guide_downloaded | Custom Event Trigger (event: patient_guide_downloaded) | form_name, document_title, clinic_location | Events Report → Lead Generation |
| clinic_page_viewed | Page View Trigger (Page Path contains /clinics/) | clinic_name, clinic_city, page_path | Pages Report → Location Performance |
| blog_article_read | Scroll Depth Trigger (75% vertical) | article_title, article_category, scroll_depth | Engagement Report → Scroll Depth |
| booking_step_complete | Custom Event Trigger (event: booking_step_complete) | step_number, step_name, clinic_location, specialty | Funnel Exploration → Booking Funnel |
| booking_complete | Custom Event Trigger (event: booking_complete) | clinic_location, specialty, preferred_date, transaction_id | Conversions Report → Goal Completions |
 
---
 
## Booking Form Funnel Drop-off Tracking
 
The 3-step booking form never reloads the page between steps. GTM cannot detect step transitions automatically. The front-end developer must write a `dataLayer.push()` at each step completion.
 
### How GTM Trigger Works at Each Step
 
At each step, a Custom Event Trigger in GTM listens for `booking_step_complete`. When the front-end fires the push, GTM catches it and sends the data to GA4.
 
### Step 1 — User selects clinic location + specialty
 
```json
{
  "event": "booking_step_complete",
  "step_number": 1,
  "step_name": "location_specialty_selected",
  "clinic_location": "koramangala",
  "specialty": "Knee Pain"
}
```
**GTM Trigger:** Custom Event — Event name: `booking_step_complete`
**Where dev places this code:** Inside the click handler of the Step 1 "Next" button
 
---
 
### Step 2 — User enters name, phone, preferred date
 
```json
{
  "event": "booking_step_complete",
  "step_number": 2,
  "step_name": "patient_details_entered",
  "clinic_location": "Koramangala",
  "specialty": "Knee Pain",
  "preferred_date": "2026-07-03"
}
```
 
**GTM Trigger:** Custom Event — Event Name equals `booking_step_complete`
**Where dev places this code:** Inside the click handler of the Step 2 "Next" button
 
---
 
### Step 3 — User confirms booking
 
```json
{
  "event": "booking_complete",
  "step_number": 3,
  "step_name": "booking_confirmed",
  "clinic_location": "Koramangala",
  "specialty": "Knee Pain",
  "preferred_date": "2026-07-10",
  "transaction_id": "ON-1231223-001"
}
```
 
**GTM Trigger:** Custom Event — Event Name equals `booking_complete`
**Where dev places this code:** Inside the click handler of the Step 3 "Confirm Booking" button
 
---
 
### How to Surface Drop-off in GA4 Funnel Exploration
 
1. Open GA4 → Explore → Funnel Exploration
2. Add 3 steps:
   - Step 1: Event = `booking_step_complete` where `step_number` = 1
   - Step 2: Event = `booking_step_complete` where `step_number` = 2
   - Step 3: Event = `booking_complete` where `step_number` = 3
3. GA4 will show how many users completed each step and where they dropped off
---
 
## Task 1C — Google Ads Conversion Action
 
**Chosen event:** `booking_complete`
 
**Why this one over others:**
 
`booking_complete` is the highest-intent action a user can take on the OrthoNow website. It means the user selected a clinic, entered their details, and confirmed an appointment. This directly maps to revenue for the client.
 
Other events like `call_now_clicked` or `whatsapp_widget_clicked` are micro-conversions — they indicate intent but not confirmed action. Importing `booking_complete` into Google Ads allows the campaign to optimise toward users most likely to complete a full booking, giving the performance marketing team the most valuable signal for Smart Bidding.

