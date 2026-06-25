<template>
  <section id="projects" class="py-5 min-vh-100 d-flex align-items-center">
    <div class="container">
      
      <div class="row mb-5">
        <div class="col-12 text-center text-light">
          <h2 class="display-5 fw-bold text-uppercase tracking-wider font-montserrat">My Projects</h2>
          <div class="header-line mx-auto mt-2"></div>
        </div>
      </div>

      <div class="row justify-content-center">
        <div 
          v-for="project in projectsData" 
          :key="project.id" 
          class="col-12 col-md-6 col-lg-4 d-flex custom-project-col"
        >
          <ProjectCard :project="project" />
        </div>
      </div>

    </div>
  </section>
</template>

<script setup>
import ProjectCard from './ProjectCard.vue';
import projectsData from '../data/projects.json';
</script>

<style scoped>
#projects {
  background-color: transparent !important;
  position: relative;
}


.custom-project-col {
  padding-top: 16px !important;
  padding-bottom: 16px !important;
}

.header-line {
  width: 60px;
  height: 3px;
  background-color: #7E0013;
}

.font-montserrat {
  font-family: 'Montserrat', sans-serif !important;
}
</style>