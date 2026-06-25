<template>
  <div id="contact" class="py-5 min-vh-50 d-flex align-items-center">
    <div class="container m-auto" style="max-width: 600px;">
      
      <div class="text-center mb-5">
        <h2 class="text-light font-montserrat mb-2">Get In Touch</h2>
        <div class="accent-line mx-auto"></div>
      </div>
      
      <form @submit.prevent="handleSubmit" class="contact-form d-flex flex-column gap-4">
        
        <div class="form-group">
          <label for="name" class="text-light small text-uppercase tracking-wider mb-2 d-block">Name</label>
          <input 
            type="text" 
            id="name" 
            v-model="formFields.name" 
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
            v-model="formFields.email" 
            class="form-control custom-input" 
            placeholder="your.email@example.com" 
            required
          >
        </div>

        <div class="form-group">
          <label for="message" class="text-light small text-uppercase tracking-wider mb-2 d-block">Message</label>
          <textarea 
            id="message" 
            v-model="formFields.message" 
            rows="5" 
            class="form-control custom-input" 
            placeholder="Type your message here..." 
            required
          ></textarea>
        </div>

        <button 
          type="submit" 
          class="btn submit-cta-btn mt-2" 
          :disabled="sendingState"
        >
          {{ sendingState ? 'Sending...' : 'Send Message' }}
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
</template>

<script setup>
import { ref } from 'vue';

// Unified Object Model preventing single item reading crashes
const formFields = ref({
  name: '',
  email: '',
  message: ''
});

const sendingState = ref(false);

const handleSubmit = async () => {
  sendingState.value = true;
  
  try {
    const response = await fetch('https://api.web3forms.com/submit', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
      },
      body: JSON.stringify({
        // Put your valid active Web3Forms Access Key right here
        access_key: "5ba601de-6da4-4b8b-81cd-7865d1fd1006",
        name: formFields.value.name,
        email: formFields.value.email,
        message: formFields.value.message
      })
    });

    const data = await response.json();

    if (response.status === 200 || data.success) {
      alert('Message sent successfully!');
    } else {
      console.error('Server Side Return Error Payload:', data);
      alert(`Submission failed: ${data.message || 'Check your access key token verification'}`);
    }

  } catch (error) {
    console.error('Client side compilation execution failure:', error);
    alert('A connection runtime error occurred. Please check your network connection.');
  } finally {
    // CRITICAL FIX: Executing layout cleanup inside finally blocks guarantees
    // the text inputs reset even if the server returns a 400 or 500 error!
    formFields.value = {
      name: '',
      email: '',
      message: ''
    };
    sendingState.value = false;
  }
};
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
   2. INPUT SHELLS STYLE CONTROLLERS
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
   3. SUBMIT ACTION BUTTON ENGINE
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
  color: #030f14 !important; /* Matches --bg-dark */
  box-shadow: 0 6px 18px rgba(95, 149, 152, 0.3) !important;
  transform: translateY(-2px);
}

.submit-cta-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* ==========================================================================
   4. ICON NAVIGATION ELEMENT WRAPPERS
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