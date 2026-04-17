<template>
  <section id="contact" class="section-contact">
    <div class="container-fluid p-0">
      <div class="row g-0 min-vh-100">
        <div class="col-lg-6 shadow-md">
          <div class="border rounded shadow-sm p-4 h-100">
            <h3 id="contact-title">CONTACT US</h3>
            <div class="contact-form-wrapper mx-auto">
              <form @submit.prevent="submitForm">
                
                <input type="checkbox" name="botcheck" class="hidden" style="display: none;">

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

                <button type="submit" class="btn btn-primary" :disabled="isLoading">
                  {{ isLoading ? "Sending..." : "Submit" }}
                </button>
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
        <div class="modal-body">Your message has been sent successfully!</div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">OK</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, nextTick } from 'vue';
import { Notyf } from 'notyf';
import 'notyf/notyf.min.css';

const notyf = new Notyf();
const WEB3FORMS_ACCESS_KEY = "f646aa6d-e4ba-4130-a027-5dc2f00c9c8b";

const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);
const successModalRef = ref(null);

const submitForm = async () => {
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
      })
    });

    const result = await response.json();

    if (result.success) {
      notyf.success("Message Sent!");
      name.value = ""; email.value = ""; message.value = "";
      
      await nextTick();
      if (window.bootstrap) {
        const modal = new window.bootstrap.Modal(successModalRef.value);
        modal.show();
      }
    } else {
      notyf.error(result.message || "Failed to send.");
    }
  } catch (e) {
    notyf.error("Connection error.");
  } finally {
    isLoading.value = false;
  }
};
</script>