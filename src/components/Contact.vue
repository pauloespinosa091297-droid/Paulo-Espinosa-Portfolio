<template>
  <section id="contact" class="section-contact">
    <div class="container-fluid p-0"> 
      <div class="row g-0 min-vh-100">

        <!-- MAP -->
        <div id="image" class="col-md-6 mb-4 mb-md-0">
          <div class="ratio ratio-16x9 mb-3">
            <iframe 
              src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d61775.9165503935!2d120.9382832050179!3d14.599372899314908!2m3!1f0!2f0!3f0!2m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3397ca03571ec38b%3A0x69d1d5751069c11f!2sManila%2C%20Metro%20Manila!5e0!3m2!1sen!2sph"
              width="600"
              height="450"
              allowfullscreen
              loading="lazy">
            </iframe>
          </div>
        </div>

        <!-- FORM -->
        <div class="col-lg-6 shadow-md">
          <div class="border rounded shadow-sm p-4 h-100">
            <h3>CONTACT US</h3>

            <form @submit.prevent="submitForm">

              <div class="mb-3">
                <label>Name</label>
                <input type="text" class="form-control" v-model="name" required>
              </div>

              <div class="mb-3">
                <label>Email</label>
                <input type="email" class="form-control" v-model="email" required>
              </div>

              <div class="mb-4">
                <label>Message</label>
                <textarea class="form-control" rows="5" v-model="message" required></textarea>
              </div>

              <button type="submit" class="btn custom-btn" :disabled="isLoading">
                {{ isLoading ? "Sending..." : "Submit" }}
              </button>

            </form>
          </div> 
        </div> 

      </div> 
    </div> 
  </section>

  <!-- SUCCESS MODAL -->
  <div class="modal fade" id="successModal" tabindex="-1">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content text-center p-4">
        <div class="modal-body">
          <h4>Message Sent</h4>
          <p>Your message has been successfully sent.</p>
          <button class="btn custom-btn mt-3" data-bs-dismiss="modal">
            OK
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { Notyf } from 'notyf';
import 'notyf/notyf.min.css';

const notyf = new Notyf();

const WEB3FORMS_ACCESS_KEY = "5ba601de-6da4-4b8b-81cd-7865d1fd1006";

const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);

const submitForm = async () => {
  isLoading.value = true;

  try {
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        subject: "New message from Portfolio",
        name: name.value,
        email: email.value,
        message: message.value
      })
    });

    const result = await response.json();

    if (result.success) {
      notyf.success("Message Sent!");

      // SHOW MODAL
      const modal = new bootstrap.Modal(document.getElementById('successModal'));
      modal.show();

      // RESET FORM
      name.value = "";
      email.value = "";
      message.value = "";
    } else {
      console.log(result);
      notyf.error(result.message || "Something went wrong.");
    }

  } catch (error) {
    console.error(error);
    notyf.error("Failed to send message.");
  } finally {
    isLoading.value = false;
  }
};
</script>