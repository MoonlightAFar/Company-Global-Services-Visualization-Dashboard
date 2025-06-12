<template>
  <div class="github-link">
    <a href="https://github.com/MoonlightAFar" target="_blank" rel="noopener noreferrer">
      <span class="icon">🐱</span> Github
    </a>
  </div>
</template>

<style scoped>
.github-link {
  position: fixed;
  right: 20px;
  top: 0;
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(4px);
  border: 1px solid #ccc;
  border-bottom-left-radius: 8px;
  border-bottom-right-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  padding: 6px 12px;
  font-size: 14px;
  z-index: 1000;
  transition: transform 0.2s ease, background 0.2s ease;
}

.github-link:hover {
  transform: translateY(2px);
  background: rgba(255, 255, 255, 0.95);
}

.github-link a {
  color: #333;
  text-decoration: none;
  font-weight: 500;
  display: flex;
  align-items: center;
  gap: 4px;
}

.github-link .icon {
  font-size: 16px;
}
</style>
