<template>
  <div id="landing" class="row align-items-center min-vh-100 mx-auto">
    <div id="profile" class="profile col-md-8 text-center text-light py-5 my-5 mx-auto">
      
      <div class="avatar-circle-wrapper mx-auto mb-4">
        <img src="/images/myself.jpg" alt="Paulo Espinosa" class="profile-img">
      </div>

      <h1 id="myname" class="display-4 fw-bold mb-2">Paulo Espinosa</h1>
      <h5 class="title text-uppercase mb-4 tracking-wide text-accent">Full Stack Developer</h5>
      <p id="description" class="lead mx-auto mb-5 text-muted-custom">
        I build beautiful, functional web applications and bring ideas to life through code. 
        Passionate about creating seamless user experiences and solving complex problems.
      </p>

      <div class="cta-triangle-container">
        <div class="cta-top-row">
          <a href="#projects" id="base1" class="btn landing-cta-btn">View my works</a>
          <a href="#contact" id="base2" class="btn landing-cta-btn">Get in touch</a>
        </div>
        
        <div class="cta-bottom-row">
          <a 
            href="#" 
            role="button" 
            @click.prevent="showMenuModal = true" 
            class="btn landing-cta-btn"
          >
            More Options
          </a>
        </div>
      </div>

    </div>
  </div>

  <div v-if="showMenuModal" class="custom-modal-overlay" @click.self="showMenuModal = false">
    <div class="custom-modal-content text-center">
      <button class="close-modal-btn" @click="showMenuModal = false">&times;</button>
      <h4 class="mb-4 text-uppercase tracking-wider font-montserrat">Credentials</h4>
      
      <div class="d-flex flex-column gap-3">
        <a 
          href="https://drive.google.com/file/d/1Nyk3S5com68OL7xiGPlNzmvsaL0nor_r/view?usp=sharing" 
          target="_blank" 
          rel="noopener noreferrer" 
          class="btn modal-action-btn"
          @click="showMenuModal = false"
        >
          View Resume
        </a>
        
        <button class="btn modal-action-btn-outline" @click="openCertificates">
          View Certificates
        </button>
      </div>
    </div>
  </div>

  <div v-if="showCertModal" class="custom-modal-overlay" @click.self="showCertModal = false">
    <div class="custom-modal-content modal-large">
      <button class="close-modal-btn" @click="showCertModal = false">&times;</button>
      <h4 class="text-center mb-4 text-uppercase tracking-wider font-montserrat">My Certificates</h4>
      
      <div class="cert-grid-container py-3">
        <div class="row g-4">
          
          <div 
            v-for="(cert, index) in certificatesList" 
            :key="index" 
            class="col-md-6"
          >
            <div class="cert-card" @click="launchLightBox(index)">
              <div class="cert-img-wrapper">
                <img :src="cert.image" :alt="cert.title" class="cert-img">
                <div class="cert-hover-overlay">
                  <span>Click to View Fullscreen</span>
                </div>
              </div>
              <div class="cert-details p-3">
                <h6 class="mb-1 text-light">{{ cert.title }}</h6>
                <small class="text-muted">{{ cert.description }}</small>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>

  <div v-if="showLightBox" class="lightbox-overlay" @click.self="showLightBox = false">
    <button class="lightbox-close" @click="showLightBox = false">&times;</button>
    
    <button class="lightbox-nav nav-left" @click="prevSlide" aria-label="Previous image">
      &#10094;
    </button>

    <div class="lightbox-stage">
      <div class="lightbox-image-container">
        <img 
          :src="certificatesList[activeCertIndex].image" 
          :alt="certificatesList[activeCertIndex].title" 
          class="lightbox-main-img"
        >
      </div>
      <div class="lightbox-caption text-center p-3">
        <h5 class="text-light mb-1">{{ certificatesList[activeCertIndex].title }}</h5>
        <p class="text-muted-custom mb-0 small">{{ certificatesList[activeCertIndex].description }}</p>
        <span class="badge bg-secondary mt-2">{{ activeCertIndex + 1 }} / {{ certificatesList.length }}</span>
      </div>
    </div>

    <button class="lightbox-nav nav-right" @click="nextSlide" aria-label="Next image">
      &#10095;
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const showMenuModal = ref(false);
const showCertModal = ref(false);

// Lightbox Layout Tracking States
const showLightBox = ref(false);
const activeCertIndex = ref(0);

// FIXED: Reverted directory references to reflect Capital 'Certificates'
const certificatesList = [
  {
    title: "Frontend Course",
    description: "Git, HTML, CSS, Bootstrap 4",
    image: "/images/Certificates/Frontend.jpeg"
  },
  {
    title: "Backend Course",
    description: "Javascript, MongoDB, Node.js, Postman, and Express.js",
    image: "/images/Certificates/Backend.jpeg"
  },
  {
    title: "Full Stack Course",
    description: "MERN Tech Stack (MongoDB, Express.js, React.js, Node.js)",
    image: "/images/Certificates/Fullstack.jpeg"
  },
  {
    title: "Azure Fundamentals (AZ-900)",
    description: "Microsoft Azure Cloud Services",
    image: "/images/Certificates/azure.png"
  }
];

const openCertificates = () => {
  showMenuModal.value = false;
  showCertModal.value = true;
};

// Lightbox Action Methods
const launchLightBox = (index) => {
  activeCertIndex.value = index;
  showLightBox.value = true;
};

const nextSlide = () => {
  if (activeCertIndex.value < certificatesList.length - 1) {
    activeCertIndex.value++;
  } else {
    activeCertIndex.value = 0;
  }
};

const prevSlide = () => {
  if (activeCertIndex.value > 0) {
    activeCertIndex.value--;
  } else {
    activeCertIndex.value = certificatesList.length - 1;
  }
};

const handleKeyDown = (e) => {
  if (!showLightBox.value) return;
  if (e.key === 'ArrowRight') nextSlide();
  if (e.key === 'ArrowLeft') prevSlide();
  if (e.key === 'Escape') showLightBox.value = false;
};

onMounted(() => {
  window.addEventListener('keydown', handleKeyDown);
});

onBeforeUnmount(() => {
  window.removeEventListener('keydown', handleKeyDown);
});
</script>

<style scoped>
/* ==========================================================================
   1. PROFILE AVATAR & BASE TEXT TYPOGRAPHY
   ========================================================================== */
.avatar-circle-wrapper {
  width: 180px;
  height: 180px;
  border-radius: 50%;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 3px solid var(--brand-secondary);
  box-shadow: 0 0 25px rgba(95, 149, 152, 0.3);
  background-color: #7E000D; 
}

.profile-img {
  width: 100%;
  height: 100%;
  object-fit: cover; 
  object-position: center top; 
  transform: scale(1.02);
}

.text-accent {
  color: #7E0013;
  letter-spacing: 2px;
}

.text-muted-custom {
  color: #cdd4d7;
  max-width: 600px;
}

/* ==========================================================================
   2. UNIFIED CTA TRIANGLE BUTTON ENGINE (Identical Size & Effects)
   ========================================================================== */
.cta-triangle-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 500px;
  margin: 0 auto;
}

.cta-top-row {
  display: flex !important;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  width: 100%;
  gap: 20px !important;
  margin-bottom: 20px;
}

.cta-bottom-row {
  display: flex;
  justify-content: center;
  width: 100%;
}

.landing-cta-btn {
  font-family: 'Montserrat', sans-serif !important;
  font-size: 0.85rem !important;
  font-weight: 500 !important;
  letter-spacing: 1px !important;
  text-transform: uppercase !important;
  color: #ffffff !important;
  text-decoration: none !important;
  background-color: rgba(95, 149, 152, 0.12) !important;
  border: 1px solid var(--brand-secondary) !important;
  border-radius: 4px !important;
  width: 210px !important;
  padding: 0.7rem 0 !important;
  text-align: center !important;
  display: inline-block !important;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1) !important;
}

.landing-cta-btn:hover {
  background-color: var(--brand-secondary) !important;
  color: var(--bg-dark) !important;
  box-shadow: 0 8px 20px rgba(95, 149, 152, 0.35) !important;
  transform: translateY(-3px) !important;
  text-decoration: none !important;
}

/* ==========================================================================
   3. CUSTOM MODAL LAYOUT OVERLAYS & ACTIONS
   ========================================================================== */
.custom-modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(6, 30, 41, 0.85);
  backdrop-filter: blur(8px);
  z-index: 1050;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1rem;
}

.custom-modal-content {
  background-color: #0c2b39;
  border: 1px solid rgba(95, 149, 152, 0.2);
  border-radius: 8px;
  padding: 2.5rem 2rem;
  position: relative;
  width: 100%;
  max-width: 400px;
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.5);
  animation: fadeInModal 0.3s cubic-bezier(0.16, 1, 0.3, 1);
}

.modal-large {
  max-width: 800px;
  max-height: 85vh;
  display: flex;
  flex-direction: column;
}

.close-modal-btn {
  position: absolute;
  top: 15px;
  right: 20px;
  background: none;
  border: none;
  color: var(--text-light);
  font-size: 1.8rem;
  line-height: 1;
  cursor: pointer;
  opacity: 0.5;
  transition: opacity 0.2s ease;
}

.close-modal-btn:hover {
  opacity: 1;
}

.custom-modal-content .d-flex.flex-column {
  display: flex !important;
  flex-direction: column !important;
  gap: 16px !important; 
}

.modal-action-btn {
  background-color: var(--brand-primary);
  color: var(--text-light) !important;
  font-family: 'Montserrat', sans-serif;
  font-weight: 500;
  padding: 0.75rem;
  border-radius: 4px;
  transition: background-color 0.2s ease;
  text-decoration: none;
  display: block;
}

.modal-action-btn:hover {
  background-color: #276d8d;
}

.modal-action-btn-outline {
  background-color: transparent;
  color: var(--brand-secondary) !important;
  border: 1px solid var(--brand-secondary);
  font-family: 'Montserrat', sans-serif;
  font-weight: 500;
  padding: 0.75rem;
  border-radius: 4px;
  transition: all 0.2s ease;
  display: block;
}

.modal-action-btn-outline:hover {
  background-color: var(--brand-secondary);
  color: var(--bg-dark) !important;
}

/* ==========================================================================
   4. SCROLLABLE GALLERY BOXES FOR SYSTEM CERTIFICATES
   ========================================================================== */
.cert-grid-container {
  overflow-y: auto;
  padding-right: 10px;
}

.cert-card {
  background-color: #061E29;
  border: 1px solid rgba(95, 149, 152, 0.15);
  border-radius: 6px;
  overflow: hidden;
  height: 100%;
  cursor: pointer;
  position: relative;
  transition: transform 0.2s ease, border-color 0.2s ease;
}

.cert-card:hover {
  transform: translateY(-3px);
  border-color: var(--brand-secondary);
}

.cert-img-wrapper {
  width: 100%;
  aspect-ratio: 4 / 3;
  overflow: hidden;
  position: relative;
  background-color: #030f14;
}

.cert-img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.cert-hover-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(6, 30, 41, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: opacity 0.25s ease;
}

.cert-hover-overlay span {
  color: var(--brand-secondary);
  font-family: 'Montserrat', sans-serif;
  font-weight: 500;
  font-size: 0.9rem;
  border: 1px solid var(--brand-secondary);
  padding: 6px 14px;
  border-radius: 20px;
}

.cert-card:hover .cert-hover-overlay {
  opacity: 1;
}

.font-montserrat {
  font-family: 'Montserrat', sans-serif !important;
}

/* ==========================================================================
   5. FACEBOOK STYLE IMMERSIVE LIGHTBOX
   ========================================================================== */
.lightbox-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(3, 15, 20, 0.96);
  z-index: 2000; 
  display: flex;
  align-items: center;
  justify-content: center;
}

.lightbox-stage {
  width: 85%;
  max-width: 900px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.lightbox-image-container {
  width: 100%;
  height: 60vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.lightbox-main-img {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
  animation: slideFade 0.25s ease-out;
  box-shadow: 0 10px 40px rgba(0,0,0,0.6);
}

.lightbox-caption {
  margin-top: 1.5rem;
  background-color: rgba(12, 43, 57, 0.6);
  border: 1px solid rgba(95, 149, 152, 0.15);
  border-radius: 8px;
  width: 100%;
  max-width: 600px;
}

.lightbox-close {
  position: absolute;
  top: 25px;
  right: 35px;
  background: none;
  border: none;
  color: #ffffff;
  font-size: 2.5rem;
  cursor: pointer;
  opacity: 0.7;
  transition: opacity 0.2s ease;
}

.lightbox-close:hover {
  opacity: 1;
}

.lightbox-nav {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background-color: rgba(6, 30, 41, 0.5);
  border: 1px solid rgba(95, 149, 152, 0.2);
  color: #ffffff;
  font-size: 2rem;
  padding: 10px 18px;
  border-radius: 50%;
  cursor: pointer;
  transition: all 0.2s ease;
  z-index: 2010;
  user-select: none;
}

.lightbox-nav:hover {
  background-color: var(--brand-secondary);
  color: var(--bg-dark);
}

.nav-left { left: 40px; }
.nav-right { right: 40px; }

@keyframes fadeInModal {
  from { opacity: 0; transform: scale(0.96); }
  to { opacity: 1; transform: scale(1); }
}

@keyframes slideFade {
  from { opacity: 0; transform: scale(0.98); }
  to { opacity: 1; transform: scale(1); }
}

/* Certificate Panel Scrollbars */
.cert-grid-container::-webkit-scrollbar {
  width: 6px;
}
.cert-grid-container::-webkit-scrollbar-track {
  background: rgba(6, 30, 41, 0.5);
}
.cert-grid-container::-webkit-scrollbar-thumb {
  background: var(--brand-secondary);
  border-radius: 10px;
}

/* ==========================================================================
   6. RESPONSIVE MOBILE MEDIA QUERIES
   ========================================================================== */
@media (max-width: 768px) {
  .lightbox-nav {
    padding: 6px 12px;
    font-size: 1.5rem;
  }
  .nav-left { left: 15px; }
  .nav-right { right: 15px; }
  .lightbox-stage { width: 95%; }
  .lightbox-image-container { height: 45vh; }
}

@media (max-width: 575.98px) {
  .cta-top-row {
    flex-direction: column;
    gap: 12px !important;
  }
  .landing-cta-btn {
    width: 100% !important; 
  }
}
</style>