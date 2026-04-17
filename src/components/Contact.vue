<template>
  <section id="contact" class="section-contact">
    <div class="container-fluid p-0"> 
      <div class="row g-0 min-vh-100">

        <div id="image" class="col-md-6 mb-4 mb-md-0">
          <div class="ratio ratio-16x9 mb-3">
            <iframe 
            src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d61775.9165503935!2d120.9382832050179!3d14.599372899314908!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3397ca03571ec38b%3A0x69d1d5751069c11f!2sManila%2C%20Metro%20Manila!5e0!3m2!1sen!2sph!4v1768479887127!5m2!1sen!2sph" 
            width="600" 
            height="450" 
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

          <p id="content" class="mb-4 text-muted"></p>

          <div class="contact-form-wrapper mx-auto">
           <form @submit.prevent="submitForm"> <div class="mb-3">
             <label class="form-label">Name</label>
             <input type="text" class="form-control" placeholder="Full name" v-model="name" required>
           </div>

           <div class="mb-3">
             <label class="form-label">Email Address</label>
             <input type="email" class="form-control" placeholder="Email" v-model="email" required>
           </div>

           <div class="mb-4">
             <label class="form-label">Message</label>
             <textarea class="form-control" rows="5" placeholder="Message" v-model="message" required></textarea>
           </div>

           <button
           type="submit" 
           class="btn btn-primary w-100"
           :disabled="isLoading">
           {{ isLoading ? "Sending..." : "Submit" }}
         </button>
         <div class="d-flex justify-content-end mt-3">
           <div ref="recaptchaContainer"></div>
         </div>
       </form>
     </div> 
   </div> 
 </div> 
</div> 
</div> 
</section>

<div class="modal fade" id="successModal" tabindex="-1">
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
          <button 
            type="button" 
            class="btn btn-primary" 
            data-bs-dismiss="modal">
            OK
          </button>
        </div>

  </div>
</div>
</div>

</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { Notyf } from 'notyf';
import 'notyf/notyf.min.css';
import * as bootstrap from 'bootstrap';

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

            name.value = "";
            email.value = "";
            message.value = "";
      
            resetRecaptcha();

          
            isLoading.value = false;

       
            const successModal = new bootstrap.Modal(document.getElementById('successModal'));
            successModal.show();
        }

    } catch (e) {
        console.log(e);
        isLoading.value = false;
        notyf.error("Failed to send a message. Please try again.");
    }
};


// =======================
// RECAPTCHA
// =======================

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