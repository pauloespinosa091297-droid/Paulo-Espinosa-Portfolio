<template>
  <div id="contact" class="py-5 min-vh-100 d-flex align-items-center">
    <div class="container m-auto">
      
      <div class="text-center mb-5">
        <h2 class="text-light font-montserrat mb-2">Get In Touch</h2>
        <div class="accent-line mx-auto"></div>
      </div>

      <div class="row g-5 align-items-stretch">
        <div class="col-lg-6 col-md-12 d-flex flex-column justify-content-between">
          <div class="map-wrapper w-100 h-100 mb-4 mb-lg-0">
            <iframe 
              src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d123652.82583561334!2d120.9168434!3d14.4113337!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3397d26df5767b4f%3A0xe6ec8b488661efc8!2sBacoor%2C%20Cavite!5e0!3m2!1sen!2sph!4v1710000000000!5m2!1sen!2sph" 
              class="w-100 h-100 border-0 rounded-3" 
              allowfullscreen="" 
              loading="lazy" 
              referrerpolicy="no-referrer-when-downgrade"
            ></iframe>
          </div>
        </div>

        <div class="col-lg-6 col-md-12">
          <form @submit.prevent="submitForm" class="contact-form d-flex flex-column gap-4">
            
            <div class="form-group">
              <label for="name" class="text-light small text-uppercase tracking-wider mb-2 d-block">Name</label>
              <input 
                type="text" 
                id="name" 
                v-model="name" 
                class="form-control custom-input" 
                placeholder="Your Name" 
                required
              >
            </div>

            <div class="form-group">
              <label for="email" class="text-light small text-uppercase tracking-wider mb-2 d-block">Email Address</label>
              <input 
                type="email" 
                id="email" 
                v-model="email" 
                class="form-control custom-input" 
                placeholder="your.email@example.com" 
                required
              >
            </div>

            <div class="form-group">
              <label for="message" class="text-light small text-uppercase tracking-wider mb-2 d-block">Message</label>
              <textarea 
                id="message" 
                v-model="message" 
                rows="5" 
                class="form-control custom-input" 
                placeholder="Type your message here..." 
                required
              ></textarea>
            </div>

            <div class="recaptcha-wrapper my-2">
              <div ref="recaptchaContainer"></div>
            </div>

            <button 
              type="submit" 
              class="btn submit-cta-btn" 
              :disabled="isLoading"
            >
              {{ isLoading ? 'Sending...' : 'Send Message' }}
            </button>
          </form>

          <div class="social-links-footer text-center mt-5">
            <div class="d-flex justify-content-center gap-3">
              <a href="https://github.com/pauloespinosa091297-droid" target="_blank" rel="noopener noreferrer" class="social-icon-circle" aria-label="GitHub">
                <i class="fab fa-github"></i>
              </a>
              <a href="#" class="social-icon-circle" aria-label="Facebook">
                <i class="fab fa-facebook-f"></i>
              </a>
              <a href="#" class="social-icon-circle" aria-label="Instagram">
                <i class="fab fa-instagram"></i>
              </a>
              <a href="#" class="social-icon-circle" aria-label="LinkedIn">
                <i class="fab fa-linkedin-in"></i>
              </a>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
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