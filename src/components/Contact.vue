<template>
  <section id="contact" class="section-contact">
    <div class="container-fluid p-0">
      <div class="row g-0 min-vh-100">
        
        <div id="image" class="col-md-6 mb-4 mb-md-0">
          <div class="ratio ratio-16x9 h-100">
            <iframe 
              src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3861.1234!2d120.9!3d14.5!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x0%3A0x0!2zMTTCsDMwJzAwLjAiTiAxMjDCsDU0JzAwLjAiRQ!5e0!3m2!1sen!2sph!4v1700000000000!5m2!1sen!2sph" 
              style="border:0;" 
              allowfullscreen="" 
              loading="lazy" 
              referrerpolicy="no-referrer-when-downgrade">
            </iframe>
          </div>
        </div>

        <div class="col-lg-6 d-flex align-items-center p-4">
          <div class="w-100" style="max-width: 500px; margin: 0 auto;">
            <h3 class="mb-4">CONTACT US</h3>
            
            <form @submit.prevent="submitForm">
              <input type="checkbox" name="botcheck" class="hidden" style="display: none;">

              <div class="mb-3">
                <label class="form-label fw-bold">Name</label>
                <input type="text" class="form-control" placeholder="Juan Dela Cruz" v-model="name" required>
              </div>

              <div class="mb-3">
                <label class="form-label fw-bold">Email Address</label>
                <input type="email" class="form-control" placeholder="juan.delacruz@mail.com" v-model="email" required>
              </div>

              <div class="mb-4">
                <label class="form-label fw-bold">Message</label>
                <textarea class="form-control" rows="5" placeholder="Leave a message here" v-model="message" required></textarea>
              </div>

              <button
                type="submit"
                class="btn btn-primary w-100 py-2"
                :disabled="isLoading">
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
import { ref } from 'vue';
import { Notyf } from 'notyf';
import 'notyf/notyf.min.css';

const notyf = new Notyf({
  duration: 4000,
  position: { x: 'right', y: 'top' },
});

// Form Data
const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);

// Config
const WEB3FORMS_ACCESS_KEY = "b178b093-4dff-4529-a71c-25f184b6ecc3";

const submitForm = async () => {
  isLoading.value = true;

  try {
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: { 
        "Content-Type": "application/json", 
        "Accept": "application/json" 
      },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        name: name.value,
        email: email.value,
        message: message.value,
        subject: "New Portfolio Contact Message"
      })
    });

    const result = await response.json();

    if (result.success) {
      notyf.success("Thank you! Your message has been sent.");
      // Clear inputs
      name.value = "";
      email.value = "";
      message.value = "";
    } else {
      // If result fails, it will show the specific error from Web3Forms
      notyf.error(result.message || "Something went wrong.");
    }
  } catch (e) {
    notyf.error("Connection error. Please try again.");
    console.error("Submission Error:", e);
  } finally {
    isLoading.value = false;
  }
};
</script>

<style scoped>
.section-contact {
  background-color: #ffffff;
}

iframe {
  width: 100%;
  height: 100%;
  min-height: 450px;
}
</style>