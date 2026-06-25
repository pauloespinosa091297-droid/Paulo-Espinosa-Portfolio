<template>
  <section id="tools" class="container py-5">
    <h2 class="text-center mb-5 section-title">Tools & Technologies</h2>

    <div class="row g-5 justify-content-center">
      
      <div class="col-lg-4 col-md-6">
        <div class="tools-category-card text-center p-4">
          <h5 class="mb-4 group-title">Frontend & Basics</h5>
          
          <div class="scene-3d">
            <div class="carousel-3d">
              
              <div class="logo-3d-wrapper rot-0">
                <img src="https://i.imgur.com/a0aQ1YZ.png" class="img-fluid tool-icon" alt="HTML 5">
                <span class="tool-label font-montserrat">HTML 5</span>
              </div>
              
              <div class="logo-3d-wrapper rot-90">
                <img src="https://i.imgur.com/IqKJ2b3.png" class="img-fluid tool-icon" alt="CSS 3">
                <span class="tool-label font-montserrat">CSS 3</span>
              </div>
              
              <div class="logo-3d-wrapper rot-180">
                <img src="https://i.imgur.com/oYWXBMO.png" class="img-fluid tool-icon" alt="Bootstrap 5">
                <span class="tool-label font-montserrat">Bootstrap 5</span>
              </div>
              
              <div class="logo-3d-wrapper rot-270">
                <img src="https://i.imgur.com/2ut8vqZ.png" class="img-fluid tool-icon" alt="JavaScript">
                <span class="tool-label font-montserrat">JavaScript</span>
              </div>

            </div>
          </div>
          
        </div>
      </div>

      <div class="col-lg-4 col-md-6">
        <div class="tools-category-card text-center p-4">
          <h5 class="mb-4 group-title">Backend & Database</h5>
          
          <div class="scene-3d">
            <div class="carousel-3d">
              
              <div class="logo-3d-wrapper rot-0">
                <img src="https://i.imgur.com/I9W3QrG.png" class="img-fluid tool-icon" alt="MongoDB">
                <span class="tool-label font-montserrat">MongoDB</span>
              </div>
              
              <div class="logo-3d-wrapper rot-90">
                <img src="https://i.imgur.com/asc8koI.png" class="img-fluid tool-icon" alt="Express.js">
                <span class="tool-label font-montserrat">Express.js</span>
              </div>  
              
              <div class="logo-3d-wrapper rot-180">
                <img src="https://i.imgur.com/2rERJyj.png" class="img-fluid tool-icon" alt="React.js">
                <span class="tool-label font-montserrat">React.js</span>
              </div>
              
              <div class="logo-3d-wrapper rot-270">
                <img src="https://i.imgur.com/0J58HCq.png" class="img-fluid tool-icon" alt="Node.js">
                <span class="tool-label font-montserrat">Node.js</span>
              </div>

            </div>
          </div>
          
        </div>
      </div>

      <div class="col-lg-4 col-md-6">
        <div class="tools-category-card text-center p-4">
          <h5 class="mb-4 group-title">Environment & Tools</h5>
          
          <div class="scene-3d">
            <div class="carousel-3d">
              
              <div class="logo-3d-wrapper rot-0">
                <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/vscode/vscode-original.svg" class="img-fluid tool-icon" alt="VS Code">
                <span class="tool-label font-montserrat">VS Code</span>
              </div>
              
              <div class="logo-3d-wrapper rot-90">
                <img src="https://i.imgur.com/aLq73Jz.png" class="img-fluid tool-icon" alt="Git">
                <span class="tool-label font-montserrat">Git</span>
              </div>
              
              <div class="logo-3d-wrapper rot-180">
                <img src="https://i.imgur.com/Tz0QsIs.png" class="img-fluid tool-icon" alt="Postman">
                <span class="tool-label font-montserrat">Postman</span>
              </div>
              
              <div class="logo-3d-wrapper rot-270">
                <img src="https://i.imgur.com/W9OXo8J.png" class="img-fluid tool-icon" alt="Sublime Text">
                <span class="tool-label font-montserrat">Sublime</span>
              </div>

            </div>
          </div>
          
        </div>
      </div>

    </div>
  </section>
</template>

<style scoped>
.section-title {
  position: relative;
  color: #fff;
}
.section-title::after {
  content: '';
  display: block;
  width: 50px;
  height: 3px;
  background-color: var(--brand-secondary);
  margin: 15px auto 0 auto;
}
.tools-category-card {
  background-color: rgba(29, 84, 109, 0.1);
  border: 1px solid rgba(95, 149, 152, 0.15);
  border-radius: 16px;
  overflow: hidden;
}
.group-title {
  color: var(--brand-secondary);
  font-size: 1.1rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 1px;
}


.scene-3d {
  width: 100%;
  height: 240px; 
  perspective: 800px; 
  display: flex;
  justify-content: center;
  align-items: center;
}

.carousel-3d {
  width: 110px; 
  height: 110px;
  position: relative;
  transform-style: preserve-3d;
  animation: spinLogos 14s infinite linear;
}

.tools-category-card:hover .carousel-3d {
  animation-play-state: paused;
}


.logo-3d-wrapper {
  position: absolute;
  width: 110px;
  height: 130px; 
  left: 0;
  top: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  backface-visibility: visible;
}


.rot-0   { transform: rotateY(0deg) translateZ(110px); }
.rot-90  { transform: rotateY(90deg) translateZ(110px); }
.rot-180 { transform: rotateY(180deg) translateZ(110px); }
.rot-270 { transform: rotateY(270deg) translateZ(110px); }


.tool-icon {
  max-height: 70px; /* FIXED: Bumped from 55px to 70px for maximum impact */
  width: auto;
  object-fit: contain;
  filter: grayscale(15%) drop-shadow(0 4px 6px rgba(0,0,0,0.25));
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.tool-icon:hover {
  filter: grayscale(0%) scale(1.1) drop-shadow(0 4px 15px rgba(95,149,152,0.6));
}

.tool-label {
  color: #8da4ae;
  font-size: 0.8rem;
  font-weight: 500;
  letter-spacing: 0.5px;
  margin-top: 10px;
  white-space: nowrap;
  transition: color 0.3s ease;
}


.logo-3d-wrapper:hover .tool-label {
  color: #ffffff;
}

.font-montserrat {
  font-family: 'Montserrat', sans-serif !important;
}

/* 3D CAROUSEL TIMELINE ROTATION CYCLE */
@keyframes spinLogos {
  from {
    transform: rotateX(-8deg) rotateY(0deg); 
  }
  to {
    transform: rotateX(-8deg) rotateY(360deg);
  }
}
</style>