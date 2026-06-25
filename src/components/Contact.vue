<template>
  <section id="contact" class="py-5">
    <div class="container">
      <h2 class="text-center mb-5 section-title">Get In Touch</h2>
      
      <div class="social-links-container d-flex justify-content-center align-items-center mb-5">
        <a href="https://github.com/pauloespinosa" target="_blank" class="social-link" title="GitHub">
          <img src="/images/github.png" class="social-icon" alt="GitHub">
        </a> 
        <a href="https://www.facebook.com/paulo.espinosa.12" target="_blank" class="social-link" title="Facebook">
          <img src="/images/facebook.png" class="social-icon" alt="Facebook">
        </a> 
        <a href="https://www.instagram.com/plspns/" target="_blank" class="social-link" title="Instagram">
          <img src="/images/instagram.png" class="social-icon" alt="Instagram">
        </a> 
        <a href="https://www.linkedin.com/in/paulo-espinosa-1232ab24a/" target="_blank" class="social-link" title="LinkedIn">
          <img src="/images/linkedin.png" class="social-icon" alt="LinkedIn">
        </a>
      </div>

      <div class="row g-4 align-items-stretch">
        
        <div id="image" class="col-md-6">
          <div class="map-wrapper h-100 rounded overflow-hidden">
            <iframe 
              src="https://maps.google.com/maps?q=Manila,Philippines&t=m&z=12&output=embed&iwloc=near" 
              width="100%" 
              height="100%" 
              style="border:0; filter: invert(90%) hue-rotate(180deg) brightness(85%) contrast(95%);" 
              allowfullscreen="" 
              loading="lazy" 
              referrerpolicy="no-referrer-when-downgrade">
            </iframe>
          </div>
        </div>

        <div class="col-md-6">
          <div class="form-card-wrapper p-4 h-100">
            <h4 id="contact-title" class="mb-3 tracking-wide text-light">CONTACT ME</h4>

            <form @submit.prevent="submitForm">
              <div class="mb-3">
                <label class="form-label text-light opacity-75">Name</label>
                <input type="text" class="form-control input-custom" placeholder="Full name" v-model="name" required>
              </div>

              <div class="mb-3">
                <label class="form-label text-light opacity-75">Employer / Company Name</label>
                <input type="text" class="form-control input-custom" placeholder="Company or Organization name" v-model="companyName">
              </div>

              <div class="mb-3">
                <label class="form-label text-light opacity-75">Email Address</label>
                <input type="email" class="form-control input-custom" placeholder="Email" v-model="email" required>
              </div>

              <div class="mb-4">
                <label class="form-label text-light opacity-75">Message</label>
                <textarea class="form-control input-custom" rows="4" placeholder="Your message here..." v-model="message" required></textarea>
              </div>

              <div class="d-flex justify-content-center mb-4">
                <div ref="recaptchaContainer"></div>
              </div>

              <button type="submit" class="btn custom-btn w-100 py-2.5" :disabled="isLoading">
                {{ isLoading ? "Verifying & Sending..." : "Submit Message" }}
              </button>
            </form>
          </div> 
        </div> 

      </div> 
    </div> 
  </section>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { Notyf } from 'notyf';
import 'notyf/notyf.min.css';

const notyf = new Notyf({
  duration: 4000,
  position: { x: 'right', y: 'bottom' },
  types: [
    { type: 'success', background: '#1D546D' },
    { type: 'error', background: '#9e2a2b' }
  ]
});

const WEB3FORMS_ACCESS_KEY = "5ba601de-6da4-4b8b-81cd-7865d1fd1006";
const ABSTRACT_API_KEY = "f166fd3d11f54160a5dcaf6d52b80320"; 
const subject = "New message from Portfolio Contact Form";

const name = ref("");
const companyName = ref(""); 
const email = ref("");
const message = ref("");
const isLoading = ref(false);

const SITE_KEY = '6LckWLwsAAAAAOQJrVKsT7vfZmLnww9JzR9xk2q9';
const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

let intervalId = null;

// NEW: Real Email Address Verification Engine
const verifyEmailAddress = async (targetEmail) => {
  try {
    const response = await fetch(`https://emailvalidation.abstractapi.com/v1/?api_key=${ABSTRACT_API_KEY}&email=${targetEmail}`);
    const data = await response.json();
    
    // Check if the domain has a valid MX record and isn't flagged as completely deliverable: false
    if (data.deliverability === "UNDELIVERABLE" || data.is_valid_format.value === false) {
      if (data.autocorrect) {
        notyf.error(`Email invalid. Did you mean ${data.autocorrect}?`);
      } else {
        notyf.error("The email address you entered does not appear to exist.");
      }
      return false;
    }
    return true;
  } catch (error) {
    console.error("Verification API fallback triggered:", error);
    return true; // Fallback to let the form send if the verification API hit an accidental rate limit
  }
};

const submitForm = async () => {
  if (!recaptchaToken.value) {
    notyf.error('Please verify that you are not a robot.');
    return;
  }

  isLoading.value = true;

  // STEP 1: Intercept submission to check real mail status
  const isEmailReal = await verifyEmailAddress(email.value);
  if (!isEmailReal) {
    isLoading.value = false;
    return; // Kill submission if it's a fake email domain
  }

  // STEP 2: Proceed with standard submission pipeline if true
  try {
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        subject: subject,
        name: name.value,
        company: companyName.value, 
        email: email.value,
        message: message.value,
        "g-recaptcha-response": recaptchaToken.value
      })
    });

    const result = await response.json();

    if (result.success) {
      notyf.success("Message Sent Successfully!");
      name.value = "";
      companyName.value = ""; 
      email.value = "";
      message.value = "";
      resetRecaptcha();
    }
  } catch (e) {
    console.error(e);
    notyf.error("Failed to send message. Please try again.");
  } finally {
    isLoading.value = false;
  }
};

function onRecaptchaSuccess(token) { recaptchaToken.value = token; }
defineExpose({ onRecaptchaSuccess });
function onRecaptchaExpired() { recaptchaToken.value = ''; }

function renderRecaptcha() {
  if (!window.grecaptcha) return;
  recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
    sitekey: SITE_KEY,
    theme: 'dark',
    callback: onRecaptchaSuccess,
    'expired-callback': onRecaptchaExpired,
  });
}

function resetRecaptcha() {
  if (recaptchaWidgetId.value !== null) {
    window.grecaptcha.reset(recaptchaWidgetId.value);
    recaptchaToken.value = '';
  }
}

onMounted(() => {
  intervalId = setInterval(() => {
    if (window.grecaptcha && window.grecaptcha.render) {
      renderRecaptcha();
      clearInterval(intervalId);
    }
  }, 100);
});

onBeforeUnmount(() => { 
  if (intervalId) clearInterval(intervalId); 
});
</script>

<style scoped>
.section-title {
  position: relative;
}
.section-title::after {
  content: '';
  display: block;
  width: 50px;
  height: 3px;
  background-color: var(--brand-secondary);
  margin: 15px auto 0 auto;
}

.social-links-container {
  position: relative;
  z-index: 5;
}

.social-link {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 54px;  
  height: 54px; 
  background-color: rgba(6, 30, 41, 0.4);
  border: 1px solid rgba(95, 149, 152, 0.15);
  border-radius: 50%; 
  text-decoration: none;
  margin: 0 16px; 
  transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275), border-color 0.3s ease, background-color 0.3s ease;
}

.social-link:hover {
  transform: scale(1.1) translateY(-3px);
  border-color: var(--brand-secondary);
  background-color: rgba(29, 84, 109, 0.15);
}

.social-icon {
  width: 24px;   
  height: 24px;
  object-fit: contain;
  filter: brightness(0.9) grayscale(20%);
  transition: filter 0.3s ease;
}

.social-icon[src*="github"],
.social-icon[src*="facebook"] {
  width: 32px;
  height: 32px;
}

.social-link:hover .social-icon {
  filter: brightness(1) grayscale(0%) drop-shadow(0 0 8px var(--brand-secondary));
}

.map-wrapper {
  border: 1px solid rgba(95, 149, 152, 0.2);
  min-height: 400px; 
}
.form-card-wrapper {
  background-color: rgba(29, 84, 109, 0.15);
  border: 1px solid rgba(95, 149, 152, 0.2);
  border-radius: 12px;
}
.input-custom {
  background-color: rgba(6, 30, 41, 0.5);
  border: 1px solid rgba(95, 149, 152, 0.3);
  color: var(--text-light);
}
.input-custom:focus {
  background-color: rgba(6, 30, 41, 0.8);
  border-color: var(--brand-secondary);
  color: var(--text-light);
  box-shadow: 0 0 0 0.25rem rgba(95, 149, 152, 0.25);
}

@media (max-width: 480px) {
  .social-link {
    width: 46px;
    height: 46px;
    margin: 0 8px;
  }
  .social-icon {
    width: 20px;
    height: 20px;
  }
  .social-icon[src*="github"],
  .social-icon[src*="facebook"] {
    width: 26px;
    height: 26px;
  }
}
</style>