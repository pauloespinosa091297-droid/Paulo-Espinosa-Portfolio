<template>
  <section id="contact" class="section-contact">
    <div class="container-fluid p-0">
      <div class="row g-0 min-vh-100">
        <div class="col-md-6">
          <div class="ratio ratio-16x9 h-100">
            <iframe 
              src="https://www.google.com/maps/embed?pb=!1m14!1m12!1m3!1d15444.02!2d121.05!3d14.58!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!5e0!3m2!1sen!2sph!4v1713360000000" 
              style="border:0;" allowfullscreen="" loading="lazy">
            </iframe>
          </div>
        </div>

        <div class="col-lg-6 p-4 d-flex align-items-center">
          <div class="w-100" style="max-width: 500px; margin: 0 auto;">
            <h3 class="mb-4">CONTACT US</h3>
            <form @submit.prevent="submitForm">
              <div class="mb-3">
                <label class="form-label">Name</label>
                <input type="text" class="form-control" v-model="name" required>
              </div>
              <div class="mb-3">
                <label class="form-label">Email Address</label>
                <input type="email" class="form-control" v-model="email" required>
              </div>
              <div class="mb-4">
                <label class="form-label">Message</label>
                <textarea class="form-control" rows="5" v-model="message" required></textarea>
              </div>

              <div class="mb-3 d-flex justify-content-center">
                <div ref="recaptchaContainer"></div>
              </div>

              <button type="submit" class="btn btn-primary w-100" :disabled="isLoading">
                {{ isLoading ? "Sending..." : "Send Message" }}
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

// State
const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);

// reCAPTCHA Logic
const SITE_KEY = '6LdaOrwsAAAAANJuzslgfdRy9n7P1bqQZxKtkiHh'; 
const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

const WEB3FORMS_ACCESS_KEY = "b178b093-4dff-4529-a71c-25f184b6ecc3";

const onRecaptchaSuccess = (token) => { recaptchaToken.value = token; };
const onRecaptchaExpired = () => { recaptchaToken.value = ''; };

const renderRecaptcha = () => {
  if (window.grecaptcha && window.grecaptcha.render && recaptchaContainer.value) {
    try {
      recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
        sitekey: SITE_KEY,
        callback: onRecaptchaSuccess,
        'expired-callback': onRecaptchaExpired,
      });
    } catch (e) {
      console.error("reCAPTCHA render error:", e);
    }
  }
};

const submitForm = async () => {
  if (!recaptchaToken.value) {
    notyf.error("Please verify the reCAPTCHA.");
    return;
  }

  isLoading.value = true;
  try {
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: { "Content-Type": "application/json", "Accept": "application/json" },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        name: name.value,
        email: email.value,
        message: message.value,
        recaptcha_response: recaptchaToken.value
      })
    });

    const result = await response.json();
    if (result.success) {
      notyf.success("Message sent successfully!");
      name.value = ""; email.value = ""; message.value = "";
      if (window.grecaptcha) window.grecaptcha.reset(recaptchaWidgetId.value);
      recaptchaToken.value = "";
    } else {
      notyf.error(result.message || "Failed to send.");
    }
  } catch (e) {
    notyf.error("Connection error.");
  } finally {
    isLoading.value = false;
  }
};

onMounted(() => {
  const checkGrecaptcha = setInterval(() => {
    if (window.grecaptcha && window.grecaptcha.render) {
      renderRecaptcha();
      clearInterval(checkGrecaptcha);
    }
  }, 500);

  onBeforeUnmount(() => clearInterval(checkGrecaptcha));
});
</script>