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
        class="btn custom-btn"
        data-bs-dismiss="modal"
        onclick="openThankYouModal()"
        >
        OK
      </button>
    </div>
  </div>
</div>
</div>

<div class="modal fade" id="thankYouModal" tabindex="-1">
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content text-center p-4">
      <div class="modal-body">
        <h4 class="mb-3">Thank you for reaching out!</h4>
        <p class="text-muted">
          I’ll get back to you as soon as possible.
        </p>
        <button
        type="button"
        class="btn custom-btn mt-3"
        data-bs-dismiss="modal"
        >
        OK
      </button>
    </div>
  </div>
</div>
</div>
</template>

<script setup>
  import { ref, onMounted } from 'vue';
  import { Notyf } from 'notyf';
  import 'notyf/notyf.min.css';

  const notyf = new Notyf();

  const WEB3FORMS_ACCESS_KEY = "5ba601de-6da4-4b8b-81cd-7865d1fd1006";
  const SITE_KEY = "6LckWLwsAAAAAOQJrVKsT7vfZmLnww9JzR9xk2q9";

  const name = ref("");
  const email = ref("");
  const message = ref("");
  const isLoading = ref(false);

  const recaptchaContainer = ref(null);
  const recaptchaToken = ref("");
  let widgetId = null;

  // CAPTCHA success
  function onCaptchaSuccess(token) {
    recaptchaToken.value = token;
  }

  // render CAPTCHA
  function renderCaptcha() {
    if (!window.grecaptcha) return;

    widgetId = window.grecaptcha.render(recaptchaContainer.value, {
      sitekey: SITE_KEY,
      callback: onCaptchaSuccess,
    });
  }

  // reset CAPTCHA
  function resetCaptcha() {
    if (widgetId !== null) {
      window.grecaptcha.reset(widgetId);
      recaptchaToken.value = "";
    }
  }

  // SUBMIT FORM
  const submitForm = async () => {
    if (!recaptchaToken.value) {
      notyf.error("Please complete the CAPTCHA");
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
          name: name.value,
          email: email.value,
          message: message.value,
          subject: "New message from Portfolio",
          "g-recaptcha-response": recaptchaToken.value
        })
      });

      const result = await response.json();

      if (result.success) {
        notyf.success("Message Sent!");

        name.value = "";
        email.value = "";
        message.value = "";
        resetCaptcha();
      } else {
        console.log(result);
        notyf.error(result.message || "Error sending message");
      }

    } catch (error) {
      console.error(error);
      notyf.error("Network error");
    } finally {
      isLoading.value = false;
    }
  };

  // MOUNT
  onMounted(() => {
    const interval = setInterval(() => {
      if (window.grecaptcha && recaptchaContainer.value) {
        renderCaptcha();
        clearInterval(interval);
      }
    }, 100);
  });
</script>