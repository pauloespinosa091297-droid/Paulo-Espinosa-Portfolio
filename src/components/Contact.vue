<template>
  <section id="contact" class="section-contact">
    <div class="container-fluid p-0">
      <div class="row g-0 min-vh-100">
        <div id="image" class="col-md-6 mb-4 mb-md-0">
          <div class="ratio ratio-16x9 mb-3">
            <iframe 
              src="https://www.google.com/maps/embed?pb=!1m14!1m12!1m3!1d15444.110531816503!2d121.0494449!3d14.6119444!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!5e0!3m2!1sen!2sph!4v1715600000000!5m2!1sen!2sph" 
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

            <p id="thankYouMessage" class="text-success fw-semibold mt-3 d-none">
              Thank you for reaching out!
            </p>

            <div class="contact-form-wrapper mx-auto">
              <form @submit.prevent="submitForm" id="contactForm">
                <div class="mb-3">
                  <label class="form-label">Name</label>
                  <input id="inputname" type="text" class="form-control" placeholder="Juan Dela Cruz" v-model="name" required>
                </div>

                <div class="mb-3">
                  <label class="form-label">Email Address</label>
                  <input id="inputemail" type="email" class="form-control" placeholder="juan.delacruz@mail.com" v-model="email" required>
                </div>

                <div class="mb-4">
                  <label class="form-label">Message</label>
                  <textarea id="inputmessage" class="form-control" rows="5" placeholder="Leave a message here" v-model="message" required></textarea>
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

  <div class="modal fade" id="successModal" tabindex="-1" aria-hidden="true">
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
          <button type="button" class="btn custom-btn" data-bs-dismiss="modal">OK</button>
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

const WEB3FORMS_ACCESS_KEY = "f646aa6d-e4ba-4130-a027-5dc2f00c9c8b";
const SITE_KEY = '6LdaOrwsAAAAANJuzslgfdRy9n7P1bqQZxKtkiHh'; 

const subject = "New message from Portfolio Contact Form";
const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);

const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

// reCAPTCHA Callbacks
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
  // 1. Validation
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
      
      // Clear Form
      name.value = "";
      email.value = "";
      message.value = "";
      resetRecaptcha();


      const modalElement = document.getElementById('successModal');
      if (window.bootstrap) {
        const modalInstance = new bootstrap.Modal(modalElement);
        modalInstance.show();
      } else {
        console.warn("Bootstrap JS not found. Check your scripts.");
      }
    } else {
      notyf.error("Something went wrong. Please try again.");
    }

  } catch (e) {
    console.error(e);
    notyf.error("Failed to send message.");
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