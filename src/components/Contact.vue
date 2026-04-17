<script setup>
  import { ref } from 'vue';
  import { Notyf } from 'notyf';
  import 'notyf/notyf.min.css';

  const notyf = new Notyf();
  const WEB3FORMS_ACCESS_KEY = "5ba601de-6da4-4b8b-81cd-7865d1fd1006";
  const subject = "New message from Portfolio Contact Form";

  const name = ref("");
  const email = ref("");
  const message = ref("");
  const isLoading = ref(false);

  const submitForm = async() => {
    // REMOVED the recaptchaToken check because you are on the Free Tier
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
          // Add this for free spam protection
          botcheck: "" 
        })
      });

      const result = await response.json();

      if(result.success) {
        notyf.success("Message Sent!");
        // Clear form
        name.value = ""; email.value = ""; message.value = "";
      } else {
        notyf.error(result.message);
      }
    } catch(e) {
      notyf.error("Failed to send message.");
    } finally {
      isLoading.value = false;
    }
  }
</script>