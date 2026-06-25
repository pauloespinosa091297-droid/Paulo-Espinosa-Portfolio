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

const notyf = new Notyf();

const WEB3FORMS_ACCESS_KEY = "5ba601de-6da4-4b8b-81cd-7865d1fd1006";
const subject = "New message from Portfolio Contact Form";

const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);

const submitForm = async () => {
    if (!recaptchaToken.value) {
        notyf.error('Please verify that you are not a robot.');
        return;
    }

    isLoading.value = true;

    try {
        const response = await fetch("https://api.web3forms.com/submit", {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                access_key: WEB3FORMS_ACCESS_KEY,
                subject: subject,
                name: name.value,
                email: email.value,
                message: message.value
            })
        });

        const result = await response.json();

        if (result.success) {
            notyf.success("Message Sent!");

            // Successfully clear inputs out on strict confirmation
            name.value = "";
            email.value = "";
            message.value = "";
            
            resetRecaptcha();
        } else {
            // FIXED: Gracefully handles Web3Forms payload rejections without breaking script logic
            console.error("Web3Forms validation rejection payload:", result);
            notyf.error(`Submission failed: ${result.message || 'Check your inputs.'}`);
        }

    } catch (e) {
        console.error("Network interface runtime error:", e);
        notyf.error("Failed to send a message. Please try again.");
    } finally {
        // FIXED: This blocks unlocks your CTA spinner state no matter what the API returns!
        isLoading.value = false;
    }
};

// ==========================================
// GOOGLE RECAPTCHA ROUTINES
// ==========================================
const SITE_KEY = '6LckWLwsAAAAAOQJrVKsT7vfZmLnww9JzR9xk2q9';

const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

function onRecaptchaSuccess(token) {
    recaptchaToken.value = token;
}

function onRecaptchaExpired() {
    recaptchaToken.value = '';
}

function renderRecaptcha() {
    if (!window.grecaptcha) {
        console.error('reCAPTCHA not loaded');
        return;
    }

    recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
        sitekey: SITE_KEY,
        size: 'normal',
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
    const interval = setInterval(() => {
        if (window.grecaptcha && window.grecaptcha.render) {
            renderRecaptcha();
            clearInterval(interval);
        }
    }, 100);

    onBeforeUnmount(() => {
        clearInterval(interval);
    });
});
</script>

<style scoped>
/* ==========================================================================
   1. VISUAL SUB-HEADERS & DECORATIONS
   ========================================================================== */
.font-montserrat {
  font-family: 'Montserrat', sans-serif !important;
}

.accent-line {
  width: 50px;
  height: 2px;
  background-color: var(--brand-secondary, #5f9598);
}

.tracking-wider {
  letter-spacing: 1px;
}

/* ==========================================================================
   2. MAP WRAPPER ELEMENT CONFIGURATIONS
   ========================================================================== */
.map-wrapper {
  min-height: 400px;
  border: 1px solid rgba(95, 149, 152, 0.15);
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

.map-wrapper iframe {
  filter: grayscale(100%) invert(92%) contrast(83%); /* Optional dark theme match styling */
}

/* ==========================================================================
   3. INPUT SHELLS STYLE CONTROLLERS
   ========================================================================== */
.custom-input {
  background-color: #061E29 !important;
  border: 1px solid rgba(95, 149, 152, 0.15) !important;
  color: #ffffff !important;
  border-radius: 4px !important;
  padding: 0.75rem 1rem !important;
  transition: all 0.2s ease-in-out !important;
}

.custom-input:focus {
  border-color: var(--brand-secondary, #5f9598) !important;
  box-shadow: 0 0 12px rgba(95, 149, 152, 0.25) !important;
  outline: none !important;
}

.custom-input::placeholder {
  color: rgba(205, 212, 215, 0.4);
}

/* ==========================================================================
   4. SUBMIT ACTION BUTTON ENGINE
   ========================================================================== */
.submit-cta-btn {
  font-family: 'Montserrat', sans-serif !important;
  text-transform: uppercase !important;
  font-size: 0.8rem !important;
  font-weight: 500 !important;
  letter-spacing: 1px !important;
  color: #ffffff !important;
  background-color: rgba(95, 149, 152, 0.12) !important;
  border: 1px solid var(--brand-secondary, #5f9598) !important;
  padding: 0.8rem 0 !important;
  border-radius: 4px !important;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1) !important;
  width: 100%;
}

.submit-cta-btn:hover:not(:disabled) {
  background-color: var(--brand-secondary, #5f9598) !important;
  color: #030f14 !important;
  box-shadow: 0 6px 18px rgba(95, 149, 152, 0.3) !important;
  transform: translateY(-2px);
}

.submit-cta-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* ==========================================================================
   5. ICON NAVIGATION ELEMENT WRAPPERS
   ========================================================================== */
.social-icon-circle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 42px;
  height: 42px;
  border-radius: 50%;
  border: 1px solid rgba(95, 149, 152, 0.2);
  background-color: #061E29;
  color: #cdd4d7;
  text-decoration: none;
  transition: all 0.25s ease;
}

.social-icon-circle:hover {
  border-color: var(--brand-secondary, #5f9598);
  color: var(--brand-secondary, #5f9598);
  box-shadow: 0 0 10px rgba(95, 149, 152, 0.2);
  transform: translateY(-2px);
}
</style>