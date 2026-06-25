<template>
  <footer id="footer" class="footer py-4 text-center">
    <div class="container">
      <p class="m-0 text-muted-custom">© 2026 PAULO. All Rights Reserved.</p>
    </div>
  </footer>
</template>

<style scoped>
#pre-footer {
  border-top: 1px solid rgba(95, 149, 152, 0.15);
  background-color: rgba(6, 30, 41, 0.4);
}

.social-link {
  display: inline-block;
  transition: transform 0.3s ease;
}

.social-link:hover {
  transform: scale(1.1);
}

.social-icon {
  max-width: 40px;
  height: auto;
  filter: brightness(0.9) grayscale(20%);
  transition: filter 0.3s ease;
}

.social-link:hover .social-icon {
  filter: brightness(1) grayscale(0%) drop-shadow(0 0 8px var(--brand-secondary));
}

.footer {
  background-color: #04141c;
  border-top: 1px solid rgba(6, 30, 41, 0.8);
}

.text-muted-custom {
  color: #8da4ae;
  font-size: 0.85rem;
  letter-spacing: 1px;
}


</style>