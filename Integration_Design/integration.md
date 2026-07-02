# Task 3 — Integration Architecture
## OrthoNow Consultation Form - HubSpot - WhatsApp - Google Ads

---

## End-to-End Architecture

When a patient submits the consultation form on the OrthoNow landing page, the following sequence executes automatically:

**Step 1 — Form Submission**
The landing page fires a *consultation_form_submitted* dataLayer event and simultaneously sends the form data (Name, Phone) via a direct POST request to a Make.com webhook URL embedded in the form's JavaScript.

**Step 2 — Make.com as middleware**
Make.com receives the webhook and orchestrates the rest of the flow. I chose Make.com over Zapier because it offers better error handling, retry logic, and webhook support on the free tier. I chose it over a direct API call because it requires no backend server — the landing page is a static HTML file with no server-side code.

**Step 3 — HubSpot Contact Creation**
Make.com calls the HubSpot Contacts API ('POST /crm/v3/objects/contacts') to create or update a contact with:
- Name, Phone
- Clinic Preference (from form)
- Source = 'Google Ads - Consultation Landing Page'
- Lead Status = 'New Enquiry'

**Important — Phone Deduplication Issue:** HubSpot's default deduplication is based on email address, not phone number. Since our form does not collect email, two patients submitting with the same phone number but different names will create duplicate contacts. To fix this, Make.com first searches HubSpot for an existing contact with that phone number using **GET /crm/v3/objects/contacts/search** before creating a new one. If found, it updates the existing record instead of creating a duplicate.

**Step 4 — WhatsApp Message via Karix**
Make.com immediately calls the Karix WhatsApp Business API with the patient's phone number and a pre-approved message template confirming their enquiry. This runs in the same Make.com scenario as Step 3, keeping the total execution time under 30 seconds.

**Step 5 — Google Ads Conversion**
The *consultation_form_submitted* dataLayer event on the landing page is picked up by GTM, which fires the Google Ads conversion tag. This happens client-side immediately on form submit — independent of the Make.com flow.

---

## Biggest Failure Point

The single biggest failure point is the **Make.com webhook timing out or failing silently**. If Make.com is down or the webhook call fails, the patient receives no WhatsApp confirmation and HubSpot receives no lead.

**Fallback:** Make.com has a built-in retry mechanism — configure it to retry failed steps 3 times with a 2-minute interval. Additionally, store form submissions in a Google Sheet as a backup via a parallel Make.com step. If HubSpot or Karix fails, the Google Sheet still captures the lead for manual follow-up.

---

## WhatsApp 2-Minute SLA

The following could break the 2-minute SLA:

1. **Make.com queue delay** — if many forms submit simultaneously, Make.com queues scenarios. On the free plan, execution can be delayed by up to 15 minutes. Fix: upgrade to a paid Make.com plan with priority execution.

2. **Karix API timeout** — if Karix is slow to respond, the WhatsApp message delays. Fix: set a 10-second timeout on the Karix API call in Make.com and trigger a retry immediately.

3. **Phone number format mismatch** — Karix requires numbers in international format (+91XXXXXXXXXX). If the patient enters a 10-digit number without country code, the API call fails. Fix: Make.com formats the number automatically by prepending +91 before calling Karix.

**Monitoring:** Use Make.com's execution history dashboard to track scenario run times. Set up a Make.com alert to notify via email if any execution exceeds 90 seconds or fails. This gives the team visibility before the SLA is breached.