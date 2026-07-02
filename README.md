<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>OrthoNow|Book Your Consultation Now</title>

  <style>

    /* Remove default browser spacing */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    /* Page setup */
    body {
      font-family: Arial, sans-serif;
      background: #ffffff;
      color: #1a1a1a;
      padding: 40px 20px;
    }

    /* Keeps everything centered and not too wide */
    .container {
      max-width: 480px;
      margin: 0 auto;
      text-align: center;
    }

    /* 1. HEADLINE */
    h1 {
      font-size: 26px;
      font-weight: bold;
      color: #1a1a1a;
      line-height: 1.4;
      margin-bottom: 12px;
    }

    /* 2. SUBHEADLINE */
    .subheadline {
      font-size: 16px;
      color: #4b5563;
      margin-bottom: 24px;
    }

    /* 3. TRUST ELEMENT */
    .trust {
      background: #f0f7ff;
      border-radius: 8px;
      padding: 12px 16px;
      font-size: 14px;
      color: #1e40af;
      font-weight: 500;
      margin-bottom: 28px;
    }

    /* 4. FORM */
    label {
      display: block;
      font-size: 14px;
      font-weight: 500;
      color: #374151;
      margin-bottom: 6px;
      text-align: left;
    }

    input {
      width: 100%;
      padding: 12px;
      border: 1px solid #d1d5db;
      border-radius: 8px;
      font-size: 15px;
      margin-bottom: 14px;
      outline: none;
    }

    input:focus {
      border-color: #2563EB;
    }

    input.error {
      border-color: #DC2626;
    }

    /* Red error text */
    .error-msg {
      font-size: 12px;
      color: #DC2626;
      margin-top: -10px;
      margin-bottom: 12px;
      text-align: left;
      display: none;
    }

    .error-msg.show {
      display: block;
    }

    /* 5. CTA BUTTON */
    .cta-btn {
      width: 100%;
      padding: 14px;
      background: #2563EB;
      color: #ffffff;
      font-size: 16px;
      font-weight: bold;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }

    .cta-btn:hover {
      background: #1d4ed8;
    }

    /* THANK YOU STATE - hidden by default */
    .thankyou {
      display: none;
      text-align: center;
      padding: 40px 0;
    }

    .thankyou h2 {
      font-size: 22px;
      font-weight: bold;
      margin-bottom: 10px;
    }

    .thankyou p {
      font-size: 15px;
      color: #4b5563;
    }

    /* Mobile */
    @media (max-width: 480px) {
      h1 { font-size: 22px; }
      body { padding: 28px 16px; }
    }

  </style>
</head>

<body>

  <div class="container">

    <!-- HEADLINE  -->
    <h1>Back pain at your desk every day? <br> Get an expert orthopaedic opinion at <span style="color:#2563EB;">OrthoNow</span></h1>

    <!-- SUBHEADLINE -->
    <p class="subheadline">Book a free consultation in 30 seconds Only. <br/> No paperwork. No waiting.</p>

    <!--TRUST ELEMENT-->
    <div class="trust">
      9 Clinics &nbsp;|&nbsp; 3 Cities &nbsp;|&nbsp; Bengaluru, Hyderabad & Chennai
    </div>

    <!-- FORM + CTA BUTTON -->
    <form id="consultForm">

      <!-- Name field -->
      <label for="name">Your Name</label>
      <input type="text" id="name" placeholder="Enter your name" />
      <p class="error-msg" id="nameError">Please enter your name</p>

      <!-- Phone field -->
      <label for="phone">Phone Number</label>
      <input type="tel" id="phone" placeholder="98765 43210" />
      <p class="error-msg" id="phoneError">Please enter a valid 10-digit number</p>

      <!-- CTA Button -->
      <button type="submit" class="cta-btn">Book My Free Consultation</button>

    </form>

    <!-- THANK YOU STATE - hidden until form is submitted -->
    <div class="thankyou" id="thankyou">
      <h2>Yayy! You are all set!</h2>
      <p>Our team will call you within an hour to confirm your consultation slot.</p>
    </div>

  </div>

  <script>

    // Initialize dataLayer array if it doesn't exist yet
    window.dataLayer = window.dataLayer || [];

    // Listen for form submission
    document.getElementById('consultForm').addEventListener('submit', function(e) {

      // Stop page from reloading on submit
      e.preventDefault();

      // Grab values user typed in both fields
      const name = document.getElementById('name').value.trim();
      const phone = document.getElementById('phone').value.trim();

      // Track if form is valid - assume true until we find an error
      let isValid = true;

      // Validate name - must not be empty
      if (name === '') {
        document.getElementById('name').classList.add('error');
        document.getElementById('nameError').classList.add('show');
        isValid = false;
      } else {
        document.getElementById('name').classList.remove('error');
        document.getElementById('nameError').classList.remove('show');
      }

      // Validate phone - must be exactly 10 digits
      if (phone === '' || phone.length !== 10 || isNaN(phone)) {
        document.getElementById('phone').classList.add('error');
        document.getElementById('phoneError').classList.add('show');
        isValid = false;
      } else {
        document.getElementById('phone').classList.remove('error');
        document.getElementById('phoneError').classList.remove('show');
      }

      // If both fields are valid - fire dataLayer and show thank you
      if (isValid) {

        // Fire GTM dataLayer push - this is what Google Ads tracks
        window.dataLayer.push({
          event: 'consultation_form_submitted',
          patient_name: name,
          patient_phone: phone
        });

        // Hide the form
        document.getElementById('consultForm').style.display = 'none';

        // Show thank you message
        document.getElementById('thankyou').style.display = 'block';

      }

    });

  </script>

</body>

</html>
