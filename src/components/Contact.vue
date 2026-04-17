<template>
  <section id="contact" class="section-contact">
    <div class="container-fluid p-0">
      <div class="row g-0 min-vh-100">
        <div id="image" class="col-md-6 mb-4 mb-md-0">
          <div class="ratio ratio-16x9 mb-3">
            <iframe 
              src="https://www.google.com/maps/embed?pb=!1m14!1m12!1m3!1d15442.13!2d121.0!3d14.5!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!5e0!3m2!1sen!2sph!4v1" 
              width="600" 
              height="450" 
              style="border:0;" 
              allowfullscreen="" 
              loading="lazy" 
              referrerpolicy="no-referrer-when-downgrade">
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
                  <input type="text" class="form-control" placeholder="Juan Dela Cruz" v-model="name" required>
                </div>

                <div class="mb-3">
                  <label class="form-label">Email Address</label>
                  <input type="email" class="form-control" placeholder="juan.delacruz@mail.com" v-model="email" required>
                </div>

                <div class="mb-4">
                  <label class="form-label">Message</label>
                  <textarea class="form-control" rows="5" placeholder="Leave a message here" v-model="message" required></textarea>
                </div>

                <button
                  type="submit"
                  id="submitBtn"
                  class="btn custom-btn"
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

  <div class="modal fade" id="successModal" tabindex="-1" aria-hidden="true" ref="successModalRef">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Message Sent</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
        </div>
        <div class="modal-body">
          Your message has been successfully sent.
        </div>
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

const subject = "New message from Portfolio Contact Form";
const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);

const recaptchaContainer = ref(null);
const successModalRef = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

function onRecaptchaSuccess(token) {
  recaptchaToken.value = token;
}

function onRecaptchaExpired() {
  recaptchaToken.value = '';
}

function resetRecaptcha() {
  if (recaptchaWidgetId.value !== null && window.grecaptcha) {
    window.grecaptcha.reset(recaptchaWidgetId.value);
    recaptchaToken.value = '';
  }
}

function renderRecaptcha() {
  if (window.grecaptcha && window.grecaptcha.render) {
    recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
      sitekey: SITE_KEY,
      callback: onRecaptchaSuccess,
      'expired-callback': onRecaptchaExpired,
    });
  }
}

const submitForm = async () => {
  if (!recaptchaToken.value) {
    notyf.error('Please verify that you are not a robot.');
    return;
  }

  isLoading.value = true;

  try {
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        subject: subject,
        name: name.value,
        email: email.value,
        message: message.value,
        "g-recaptcha-response": recaptchaToken.value
      })
    });

    const result = await response.json();

    if (result.success) {
      notyf.success("Message Sent!");
      
      // Reset data
      name.value = "";
      email.value = "";
      message.value = "";
      resetRecaptcha();

     
      await nextTick();

      if (window.bootstrap) {
        const modal = new window.bootstrap.Modal(successModalRef.value);
        modal.show();
      } else {
     
        alert("Success! Your message was sent.");
      }
    } else {
      notyf.error("Form submission failed.");
    }
  } catch (e) {
    notyf.error("An error occurred. Please try again.");
  } finally {
    isLoading.value = false;
  }
};

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