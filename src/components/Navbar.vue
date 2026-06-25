<template>
  <nav id="navbar" class="navbar navbar-expand-lg fixed-top custom-nav">
    <div id="navcontainer" class="container align-items-center">
      
      <a href="#" id="logo-link" class="navbar-brand p-0 m-0 d-flex align-items-center" aria-label="Homepage">
        <img src="/images/logo.png" alt="PE Logo" class="nav-logo-img">
      </a>

      <button id="burger" class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
        <span id="navburger" class="navbar-toggler-icon"></span>
      </button>

      <div id="navbarNavAltMarkup" class="collapse navbar-collapse justify-content-center">
        <div class="navbar-nav gap-4 align-items-center text-center mx-auto">
          <a 
            class="nav-link text-light custom-link" 
            :class="{ 'active-link': activeSection === 'landing' }" 
            href="#landing"
          >Home</a>
          <a 
            class="nav-link text-light custom-link" 
            :class="{ 'active-link': activeSection === 'projects' }" 
            href="#projects"
          >My Projects</a>
          <a 
            class="nav-link text-light custom-link" 
            :class="{ 'active-link': activeSection === 'tools' }" 
            href="#tools"
          >Tools</a>
          <a 
            class="nav-link text-light custom-link" 
            :class="{ 'active-link': activeSection === 'contact' }" 
            href="#contact"
          >Contact</a>
        </div>
      </div>

    </div>
  </nav>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

// Track the current section id that is visible on screen
const activeSection = ref('landing');
let observer = null;

onMounted(() => {
  // Config: Trigger when a section takes up a clean area of the viewport
  const options = {
    root: null,
    rootMargin: '-20% 0px -40% 0px', 
    threshold: 0
  };

  observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        activeSection.value = entry.target.id;
      }
    });
  }, options);

  // Target sections to observe across your one-page architecture
  const sections = ['landing', 'projects', 'tools', 'contact'];
  sections.forEach((id) => {
    const el = document.getElementById(id);
    if (el) observer.observe(el);
  });
});

onUnmounted(() => {
  if (observer) observer.disconnect();
});
</script>

<style scoped>
#navbar .nav-link, 
#navbar .custom-link {
  font-family: 'Montserrat', sans-serif !important;
  font-weight: 700; /* Replaces 500 to force true Bold */
  text-transform: uppercase;
  letter-spacing: 1.5px;
}

.custom-nav {
  background-color: rgba(6, 30, 41, 0.5);
  backdrop-filter: blur(12px);
  border-bottom: 1px solid rgba(95, 149, 152, 0.1);
  height: 70px;
}

.nav-logo-img {
  height: 42px;
  width: auto;
  object-fit: contain;
  display: block;
  transition: transform 0.2s ease;
}

.navbar-toggler {
  border-color: rgba(243, 244, 244, 0.25);
}

.navbar-toggler-icon {
  background-image: url("data:image/svg+xml;charset=utf8,%3Csvg viewBox='0 0 30 30' xmlns='http://www.w3.org/2000/svg'%3%3Cpath stroke='rgba(243, 244, 244, 1)' stroke-width='2' stroke-linecap='round' stroke-miterlimit='10' d='M4 7h22M4 15h22M4 23h22'/%3E%3C/svg%3E");
}

/* ==========================================================================
   DYNAMIC UNDERLINE ANIMATION RULES (Active State Only)
   ========================================================================== */
.custom-link {
  position: relative;
  text-decoration: none;
  opacity: 0.65;
  transition: opacity 0.3s ease;
}

/* Base shape of the underline state (invisible track width 0%) */
.custom-link::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: -4px; /* Shifts the line slightly below the link characters */
  width: 0;
  height: 2px;
  background-color: #7E0013; /* Crimson Accent line matching theme */
  transition: width 0.35s cubic-bezier(0.25, 1, 0.5, 1);
}

/* Hover state changes text brightness only, does not reveal the line */
.custom-link:hover {
  opacity: 1;
}

/* Force active layout tracking when section matches viewport target */
.active-link {
  opacity: 1 !important;
  color: #ffffff !important;
}

.active-link::after {
  width: 100% !important; /* Expands the underline line completely out ONLY when active */
}

@media (max-width: 991.98px) {
  .navbar-nav {
    padding: 1rem 0;
  }
  .custom-nav {
    height: auto;
  }
  /* Eliminates underlines completely on tight mobile dropdown toggle stacks */
  .custom-link::after {
    display: none;
  }
}
</style>