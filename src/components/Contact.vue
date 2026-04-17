<template>
  <section id="contact" class="section-contact">
    <div class="container-fluid p-0">
      <div class="row g-0 min-vh-100">
        <div id="image" class="col-md-6 mb-4 mb-md-0">
          <div class="ratio ratio-16x9 h-100">
            <iframe 
              src="https://www.google.com/maps/embed?pb=!1m14!1m12!1m3!1d15444.4!2d121.0!3d14.5!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!5e0!3m2!1sen!2sph!4v1710000000000!5m2!1sen!2sph" 
              style="border:0;" 
              allowfullscreen="" 
              loading="lazy">
            </iframe>
          </div>
        </div>

        <div class="col-lg-6 shadow-md">
          <div class="border rounded shadow-sm p-4 h-100">
            <h3 id="contact-title">CONTACT US</h3>

            <div class="contact-form-wrapper mx-auto">
              <form @submit.prevent="submitForm" id="contactForm">
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

                <button
                  type="submit"
                  class="btn btn-primary"
                  :disabled="isLoading">
                  {{ isLoading ? "Sending..." : "Submit" }}
                </button>

                <div class="d-flex justify-content-end mt-2">
                  <div ref="recaptchaContainer"></div>
                </div>
              </form>
            </div> 
          </div> 
        </div> 
      </div> 
    </div> 
  </section>

  <div class="modal fade" id="successModal" tabindex="-1" ref="successModalRef">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Message Sent</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
        </div>
        <div class="modal-body">Your message has been successfully sent!</div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">OK</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick } from 'vue';
import { Notyf } from 'notyf';
import 'notyf/notyf.min.css';

const notyf = new Notyf();


const WEB3FORMS_ACCESS_KEY = "f646aa6d-e4ba-4130-a027-5dc2f00c9c8b";
const SITE_KEY = '6LdaOrwsAAAAANJuzslgfdRy9n7P1bqQZxKtkiHh'; 


const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);


const recaptchaContainer = ref(null);
const successModalRef = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');


const onRecaptchaSuccess = (token) => { recaptchaToken.value = token; };
const onRecaptchaExpired = () => { recaptchaToken.value = ''; };

const resetRecaptcha = () => {
  if (recaptchaWidgetId.value !== null && window.grecaptcha) {
    window.grecaptcha.reset(recaptchaWidgetId.value);
    recaptchaToken.value = '';
  }
};

const renderRecaptcha = () => {
  if (window.grecaptcha && window.grecaptcha.render) {
    recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
      sitekey: SITE_KEY,
      callback: onRecaptchaSuccess,
      'expired-callback': onRecaptchaExpired,
    });
  }
};


const submitForm = async () => {
  if (!recaptchaToken.value) {
    notyf.error('Please verify the reCAPTCHA.');
    return;
  }

  isLoading.value = true;

  try {
    const payload = {
      access_key: WEB3FORMS_ACCESS_KEY,
      name: name.value,
      email: email.value,
      message: message.value,
      recaptcha_response: recaptchaToken.value, // Exact field required by Web3Forms
    };

    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: { "Content-Type": "application/json", "Accept": "application/json" },
      body: JSON.stringify(payload)
    });

    const result = await response.json();

    if (result.success) {
      notyf.success("Message Sent!");
      
      // Clear Form
      name.value = ""; email.value = ""; message.value = "";
      resetRecaptcha();

      // Show Modal
      await nextTick();
      if (window.bootstrap) {
        const modal = new window.bootstrap.Modal(successModalRef.value);
        modal.show();
      }
    } else {
      console.error("Web3Forms Error:", result);
      notyf.error(result.message || "Submission failed.");
    }
  } catch (e) {
    console.error("Fetch Error:", e);
    notyf.error("Connection error. Please try again.");
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