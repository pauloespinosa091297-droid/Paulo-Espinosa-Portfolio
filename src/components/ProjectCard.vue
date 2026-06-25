<template>
  <div class="project-card-wrapper d-flex flex-column w-100 h-100">
    <div class="project-img-container">
      <img :src="project.image" :alt="project.title" class="project-card-img">
    </div>

    <div class="project-info-body p-4 text-center d-flex flex-column flex-grow-1">
      <h4 class="project-card-title text-light font-montserrat mb-2">{{ project.title }}</h4>
      <p class="project-card-desc text-muted-custom mb-4">{{ project.description }}</p>

      <div class="project-actions-container mt-auto">
        <a 
          v-if="project.repository"
          :href="project.repository" 
          target="_blank" 
          rel="noopener noreferrer" 
          class="btn project-cta-btn"
        >
          View Repository
        </a>

        <a 
          v-if="project.documentation" 
          :href="project.documentation" 
          target="_blank" 
          rel="noopener noreferrer" 
          class="btn project-cta-btn"
        >
          Postman Docs
        </a>

        <a 
          v-if="project.demo" 
          :href="project.demo" 
          target="_blank" 
          rel="noopener noreferrer" 
          class="btn project-cta-btn"
        >
          Live Demo
        </a>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  project: {
    type: Object,
    required: true
  }
});
</script>

<style scoped>
/* ==========================================================================
   CARD STRUCTURAL LAYOUT
   ========================================================================== */
.project-card-wrapper {
  background-color: #061E29;
  border: 1px solid rgba(95, 149, 152, 0.15);
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
  transition: transform 0.3s ease, border-color 0.3s ease;
  
  /* Forces the component to stay stretched to 100% height of the grid col */
  height: 100% !important;
}

.project-card-wrapper:hover {
  transform: translateY(-5px);
  border-color: var(--brand-secondary);
}

.project-img-container {
  width: 100%;
  aspect-ratio: 16 / 10;
  overflow: hidden;
  background-color: #030f14;
}

.project-card-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.project-card-wrapper:hover .project-card-img {
  transform: scale(1.04);
}

.project-card-title {
  font-size: 1.25rem;
  font-weight: 700;
  letter-spacing: 1px;
}

.project-card-desc {
  font-size: 0.9rem;
  line-height: 1.6;
  color: #cdd4d7;
}

/* ==========================================================================
   UNIFIED PROJECTS BUTTON ENGINE
   ========================================================================== */
.project-actions-container {
  display: flex !important;
  flex-direction: row !important;
  justify-content: center !important;
  align-items: center !important;
  gap: 12px !important;
  width: 100% !important;
  
  /* Pushes buttons to the baseline floor of the flexible card height tracking */
  margin-top: auto !important;
}

.project-cta-btn {
  font-family: 'Montserrat', sans-serif !important;
  font-size: 0.75rem !important; 
  font-weight: 500 !important;
  letter-spacing: 0.5px !important;
  text-transform: uppercase !important;
  color: #ffffff !important;
  text-decoration: none !important;
  
  background-color: rgba(95, 149, 152, 0.12) !important;
  border: 1px solid var(--brand-secondary) !important;
  border-radius: 4px !important;
  
  flex: 1 !important; 
  max-width: 165px !important;
  padding: 0.65rem 0 !important;
  text-align: center !important;
  display: inline-block !important;
  white-space: nowrap !important;
  
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1) !important;
}

.project-cta-btn:hover {
  background-color: var(--brand-secondary) !important;
  color: var(--bg-dark) !important;
  box-shadow: 0 6px 15px rgba(95, 149, 152, 0.25) !important;
  transform: translateY(-2px) !important;
}

.font-montserrat {
  font-family: 'Montserrat', sans-serif !important;
}

@media (max-width: 420px) {
  .project-actions-container {
    flex-direction: column !important;
    gap: 10px !important;
  }
  .project-cta-btn {
    max-width: 100% !important;
    width: 100% !important;
  }
}
</style>