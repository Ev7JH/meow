<template>
  <div class="fullscreen-heart">
    <div class="heart-section">
      <div
        class="heart-wrapper"
        @click="startSlideshow"
      >
        <div
          class="cloud-heart"
          :class="{ 'click-animation': isClicked, 'pulse-glow': slideshowActive }"
          @animationend="resetClickAnimation"
        >
          <svg viewBox="0 0 200 180" class="heart-svg">
            <defs>
              <filter id="cloudTexture" x="-20%" y="-20%" width="140%" height="140%">
                <feTurbulence type="fractalNoise" baseFrequency="0.04" numOctaves="4" result="noise" />
                <feDisplacementMap in="SourceGraphic" in2="noise" scale="8" xChannelSelector="R" yChannelSelector="G" result="displaced" />
                <feGaussianBlur in="displaced" stdDeviation="2" result="blurred" />
                <feComposite in="blurred" in2="SourceGraphic" operator="in" />
              </filter>
              <radialGradient id="cloudGradient" cx="45%" cy="40%" r="60%">
                <stop offset="0%" style="stop-color:#ffffff" />
                <stop offset="40%" style="stop-color:#fef5f8" />
                <stop offset="70%" style="stop-color:#fce4ec" />
                <stop offset="100%" style="stop-color:#f8bbd0" />
              </radialGradient>
              <filter id="cloudShadow">
                <feGaussianBlur in="SourceAlpha" stdDeviation="5" />
                <feOffset dx="2" dy="4" />
                <feComponentTransfer>
                  <feFuncA type="linear" slope="0.3" />
                </feComponentTransfer>
                <feMerge>
                  <feMergeNode />
                  <feMergeNode in="SourceGraphic" />
                </feMerge>
              </filter>
              <filter id="softGlow">
                <feGaussianBlur stdDeviation="4" result="blur" />
                <feMerge>
                  <feMergeNode in="blur" />
                  <feMergeNode in="SourceGraphic" />
                </feMerge>
              </filter>
            </defs>
            <path
              d="M 100 165 
                 C 100 165 35 125 20 95 
                 C 5 65 15 35 40 25 
                 C 55 18 75 22 90 35 
                 C 95 40 100 45 100 45 
                 C 100 45 105 40 110 35 
                 C 125 22 145 18 160 25 
                 C 185 35 195 65 180 95 
                 C 165 125 100 165 100 165 Z"
              fill="url(#cloudGradient)"
              filter="url(#cloudTexture)"
              class="heart-cloud"
            />
            <circle cx="55" cy="65" r="12" fill="white" opacity="0.6" filter="url(#softGlow)" />
            <circle cx="145" cy="65" r="12" fill="white" opacity="0.6" filter="url(#softGlow)" />
            <circle cx="100" cy="100" r="10" fill="white" opacity="0.5" filter="url(#softGlow)" />
            <circle cx="70" cy="50" r="8" fill="white" opacity="0.4" />
            <circle cx="130" cy="50" r="8" fill="white" opacity="0.4" />
            <circle cx="85" cy="75" r="6" fill="#ffe0f0" opacity="0.5" />
            <circle cx="115" cy="75" r="6" fill="#ffe0f0" opacity="0.5" />
            <ellipse cx="65" cy="50" rx="15" ry="8" fill="white" opacity="0.7" transform="rotate(-20 65 50)" />
          </svg>
        </div>
      </div>
    </div>

    <!-- Милая инструкция -->
    <transition name="instruction-fade">
      <div v-if="showInstruction" class="instruction-container">
        <div class="instruction-bubble">
          <span class="instruction-icon">🐱</span>
          <span class="instruction-text">нажми на сердечко один раз</span>
          <span class="instruction-icon">🐱</span>
        </div>
        <div class="instruction-subtext">и смотри, как появляются моменты...</div>
        
      </div>
    </transition>

    <!-- Галерея фотографий с FLUX-анимацией для плавного перестроения -->
    <transition-group 
      tag="div" 
      class="photo-gallery"
      name="photo-list"
    >
      <div
        v-for="(photo, index) in displayedPhotos"
        :key="photo.url"
        class="photo-card"
      >
        <img :src="photo.url" :alt="photo.alt" class="gallery-photo" />
        <div class="photo-caption">{{ photo.caption }}</div>
      </div>
    </transition-group>

    <!-- Финальное сообщение -->
    <transition name="message-fade-smooth">
      <div v-if="showFinalMessage" class="final-message-container">
        <div class="final-message">
          <span class="final-text"> верю, что это не мгновение, а целая жизнь </span>
          <div class="floating-hearts">
            <span class="floating-heart">✨</span>
            <span class="floating-heart">✨</span>
            <span class="floating-heart">✨</span>
            <span class="floating-heart">✨</span>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  name: "FullscreenHeart",
  data() {
    return {
      isClicked: false,
      showInstruction: true,
      showFinalMessage: false,
      slideshowActive: false,
      currentPhotoIndex: 0,
      displayedPhotos: [],
      timer: null,
      photos: [
        {
          url: "/images/foto1.jpg",
          alt: "Фото 1",
          caption: "i mrrr you"
        },
        {
          url: "/images/foto2.jpg",
          alt: "Фото 2",
          caption: "meow meow love"
        },
        {
          url: "/images/foto3.jpg",
          alt: "Фото 3",
          caption: "whisker kisses"
        },
        {
          url: "/images/foto4.jpg",
          alt: "Фото 4",
          caption: "cat hugs"
        },
        {
          url: "/images/foto5.jpg",
          alt: "Фото 5",
          caption: "meow"
        }
      ]
    };
  },
  mounted() {
    const heartElement = document.querySelector('.cloud-heart');
    if (heartElement) {
      setInterval(() => {
        if (!this.isClicked) {
          heartElement.classList.add('beat-animation');
          setTimeout(() => {
            heartElement.classList.remove('beat-animation');
          }, 600);
        }
      }, 800);
    }

    setTimeout(() => {
      if (this.showInstruction && !this.slideshowActive) {
        this.showInstruction = false;
      }
    }, 5000);
  },
  beforeUnmount() {
    if (this.timer) clearInterval(this.timer);
  },
  methods: {
    startSlideshow() {
      if (this.slideshowActive) return;
      
      this.isClicked = true;
      this.showInstruction = false;
      this.slideshowActive = true;
      
      this.showNextPhoto();
      
      this.timer = setInterval(() => {
        if (this.currentPhotoIndex < this.photos.length) {
          this.showNextPhoto();
        } else {
          clearInterval(this.timer);
        }
      }, 1500);
    },
    
    showNextPhoto() {
      if (this.currentPhotoIndex < this.photos.length) {
        this.displayedPhotos.push(this.photos[this.currentPhotoIndex]);
        this.currentPhotoIndex++;
        
        if (this.currentPhotoIndex === this.photos.length) {
          this.slideshowActive = false;
          if (this.timer) clearInterval(this.timer);
          
          setTimeout(() => {
            this.showFinalMessage = true;
          }, 300);
        }
      }
    },
    
    resetClickAnimation() {
      this.isClicked = false;
    }
  }
};
</script>

<style>
/* Глобальные стили */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  margin: 0;
  padding: 0;
  overflow: hidden;
  height: 100%;
  width: 100%;
}

body {
  overflow-y: auto;
  overflow-x: hidden;
}

#app {
  height: 100%;
  width: 100%;
}
</style>

<style scoped>
.fullscreen-heart {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  min-height: 100vh;
  background: linear-gradient(135deg, #e8e0ff 0%, #fce4ec 50%, #fff0f5 100%);
  font-family: "Quicksand", "Comfortaa", "Segoe UI", sans-serif;
  position: relative;
  overflow-y: auto;
  overflow-x: hidden;
  padding: 15px;
}

.fullscreen-heart::-webkit-scrollbar {
  width: 4px;
}

.fullscreen-heart::-webkit-scrollbar-track {
  background: rgba(255, 107, 157, 0.1);
  border-radius: 10px;
}

.fullscreen-heart::-webkit-scrollbar-thumb {
  background: rgba(255, 107, 157, 0.3);
  border-radius: 10px;
}

/* Фоновые облачка */
.fullscreen-heart::before {
  content: "☁️";
  position: absolute;
  font-size: 180px;
  opacity: 0.08;
  top: -50px;
  left: -30px;
  animation: floatBackground 25s infinite ease-in-out;
  pointer-events: none;
}

.fullscreen-heart::after {
  content: "☁️";
  position: absolute;
  font-size: 150px;
  opacity: 0.06;
  bottom: -40px;
  right: -20px;
  animation: floatBackground 20s infinite reverse ease-in-out;
  pointer-events: none;
}

.heart-section {
  z-index: 10;
  margin-bottom: 15px;
  display: flex;
  justify-content: center;
  flex-shrink: 0;
}

.heart-wrapper {
  cursor: pointer;
  display: inline-block;
  position: relative;
  z-index: 10;
  padding: 15px;
  margin: -15px;
}

.cloud-heart {
  position: relative;
  transition: all 0.2s ease;
  filter: drop-shadow(0 10px 20px rgba(255, 105, 180, 0.2));
}

.heart-svg {
  width: 160px;
  height: 144px;
  display: block;
}

.cloud-heart.beat-animation {
  animation: heartbeat 0.6s cubic-bezier(0.45, 1.3, 0.55, 1);
}

.cloud-heart.click-animation {
  animation: heartClick 0.5s cubic-bezier(0.34, 1.3, 0.64, 1);
}

.cloud-heart.pulse-glow {
  filter: drop-shadow(0 0 20px rgba(255, 105, 180, 0.5));
  animation: softPulse 2s ease-in-out infinite;
}

/* Инструкция */
.instruction-container {
  margin: 10px 0 10px;
  z-index: 10;
  position: relative;
  text-align: center;
  animation: bounceSoft 1.5s ease-in-out infinite;
  flex-shrink: 0;
}

.instruction-bubble {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(8px);
  padding: 10px 22px;
  border-radius: 50px;
  display: inline-flex;
  align-items: center;
  gap: 10px;
  box-shadow: 0 8px 20px rgba(255, 105, 180, 0.2);
  border: 1px solid rgba(255, 105, 180, 0.3);
}

.instruction-icon {
  font-size: 1.2rem;
  animation: iconWiggle 1s ease-in-out infinite;
}

.instruction-text {
  font-size: 0.95rem;
  color: #ff6b9d;
  font-weight: 500;
  letter-spacing: 0.5px;
}

.instruction-subtext {
  font-size: 0.8rem;
  color: #c96b9a;
  margin-top: 6px;
  opacity: 0.8;
}

.instruction-arrow {
  font-size: 1.3rem;
  margin-top: 4px;
  color: #ff9ec0;
  animation: arrowBounce 1s ease-in-out infinite;
}

/* ГАЛЕРЕЯ с FLUX анимацией для ПЛАВНОГО перестроения */
.photo-gallery {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 15px;
  margin: 15px 0;
  z-index: 10;
  position: relative;
  max-width: 1000px;
  width: 100%;
  flex: 1 0 auto;
}

/* КЛЮЧЕВОЕ: плавное перемещение элементов при перестроении */
.photo-list-move {
  transition: all 0.6s cubic-bezier(0.2, 0.9, 0.4, 1.1);
}

/* Появление новых элементов */
.photo-list-enter-active {
  transition: all 0.7s cubic-bezier(0.2, 0.9, 0.3, 1.1);
}

.photo-list-enter-from {
  opacity: 0;
  transform: scale(0.85) translateY(30px);
  filter: blur(3px);
}

.photo-list-enter-to {
  opacity: 1;
  transform: scale(1) translateY(0);
  filter: blur(0);
}

/* Исчезновение элементов (если нужно) */
.photo-list-leave-active {
  transition: all 0.4s ease;
  position: absolute;
  width: 100%;
}

.photo-list-leave-to {
  opacity: 0;
  transform: scale(0.9) translateY(-20px);
}

/* Сами карточки фото */
.photo-card {
  background: white;
  border-radius: 18px;
  padding: 10px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
  max-width: 240px;
  width: calc(33% - 15px);
  min-width: 160px;
  flex: 1 1 auto;
  transition: transform 0.4s cubic-bezier(0.2, 0.9, 0.4, 1.1), 
              box-shadow 0.4s cubic-bezier(0.2, 0.9, 0.4, 1.1);
}

.photo-card:hover {
  transform: translateY(-5px) scale(1.02);
  box-shadow: 0 15px 30px rgba(255, 105, 180, 0.25);
}

.gallery-photo {
  width: 100%;
  height: auto;
  border-radius: 14px;
  display: block;
  object-fit: cover;
  aspect-ratio: 4 / 3;
  transition: transform 0.4s cubic-bezier(0.2, 0.9, 0.4, 1.1);
}

.photo-card:hover .gallery-photo {
  transform: scale(1.02);
}

.photo-caption {
  text-align: center;
  margin-top: 8px;
  font-size: 0.85rem;
  color: #ff6b9d;
  font-weight: 500;
  letter-spacing: 0.5px;
  transition: all 0.3s ease;
}

.photo-card:hover .photo-caption {
  color: #ff3388;
  letter-spacing: 1px;
}

/* Финальное сообщение */
.final-message-container {
  margin: 20px 15px;
  z-index: 10;
  position: relative;
  text-align: center;
  flex-shrink: 0;
}

.final-message {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  padding: 15px 30px;
  border-radius: 50px;
  box-shadow: 0 12px 30px rgba(255, 105, 180, 0.2);
  display: inline-block;
  animation: messagePulse 2.5s ease-in-out infinite;
}

.final-text {
  font-size: 1.2rem;
  font-weight: 600;
  background: linear-gradient(125deg, #ff6b9d, #c96b9a, #ff9ec0);
  background-clip: text;
  -webkit-background-clip: text;
  color: transparent;
  letter-spacing: 1px;
  white-space: nowrap;
}

.floating-hearts {
  margin-top: 10px;
  display: flex;
  gap: 10px;
  justify-content: center;
}

.floating-heart {
  font-size: 1.2rem;
  animation: floatHeart 1.5s ease-in-out infinite;
  display: inline-block;
}

.floating-heart:nth-child(1) { animation-delay: 0s; }
.floating-heart:nth-child(2) { animation-delay: 0.3s; }
.floating-heart:nth-child(3) { animation-delay: 0.6s; }
.floating-heart:nth-child(4) { animation-delay: 0.9s; }

/* Анимации */
.instruction-fade-enter-active {
  transition: all 0.7s cubic-bezier(0.2, 0.9, 0.4, 1.1);
}

.instruction-fade-enter-from {
  opacity: 0;
  transform: translateY(20px);
  filter: blur(3px);
}

.instruction-fade-leave-active {
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.instruction-fade-leave-to {
  opacity: 0;
  transform: translateY(-15px);
  filter: blur(2px);
}

.message-fade-smooth-enter-active {
  transition: all 0.9s cubic-bezier(0.2, 0.85, 0.3, 1.05);
}

.message-fade-smooth-enter-from {
  opacity: 0;
  transform: scale(0.85) translateY(35px);
  filter: blur(5px);
}

.message-fade-smooth-enter-to {
  opacity: 1;
  transform: scale(1) translateY(0);
  filter: blur(0);
}

.message-fade-smooth-leave-active {
  transition: all 0.4s ease;
}

.message-fade-smooth-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

@keyframes heartbeat {
  0% { transform: scale(1); }
  25% { transform: scale(1.08); }
  35% { transform: scale(1.05); }
  45% { transform: scale(1.12); }
  55% { transform: scale(1.03); }
  65% { transform: scale(1.08); }
  75% { transform: scale(1.01); }
  100% { transform: scale(1); }
}

@keyframes heartClick {
  0% { transform: scale(1) rotate(0deg); }
  40% { transform: scale(1.15) rotate(-3deg); }
  70% { transform: scale(0.95) rotate(2deg); }
  100% { transform: scale(1) rotate(0deg); }
}

@keyframes softPulse {
  0%, 100% { filter: drop-shadow(0 0 15px rgba(255, 105, 180, 0.4)); }
  50% { filter: drop-shadow(0 0 25px rgba(255, 105, 180, 0.7)); }
}

@keyframes floatBackground {
  0%, 100% { transform: translateX(0) translateY(0); }
  50% { transform: translateX(15px) translateY(-15px); }
}

@keyframes messagePulse {
  0%, 100% {
    transform: scale(1);
    box-shadow: 0 12px 30px rgba(255, 105, 180, 0.2);
  }
  50% {
    transform: scale(1.02);
    box-shadow: 0 18px 40px rgba(255, 105, 180, 0.35);
  }
}

@keyframes floatHeart {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-6px); }
}

@keyframes bounceSoft {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-4px); }
}

@keyframes arrowBounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(4px); }
}

@keyframes iconWiggle {
  0%, 100% { transform: rotate(0deg); }
  25% { transform: rotate(-8deg); }
  75% { transform: rotate(8deg); }
}

.heart-wrapper:hover .cloud-heart {
  filter: drop-shadow(0 15px 30px rgba(255, 105, 180, 0.35));
  transition: filter 0.3s ease;
}

/* Адаптация */
@media (max-width: 768px) {
  .heart-svg {
    width: 130px;
    height: 117px;
  }

  .photo-card {
    max-width: 210px;
    width: calc(50% - 12px);
    min-width: 140px;
  }

  .final-text {
    font-size: 1rem;
    white-space: normal;
    text-align: center;
  }

  .final-message {
    padding: 12px 20px;
  }

  .floating-heart {
    font-size: 1rem;
  }
}

@media (max-width: 480px) {
  .heart-svg {
    width: 100px;
    height: 90px;
  }

  .photo-card {
    max-width: 100%;
    width: 100%;
    min-width: auto;
  }

  .final-text {
    font-size: 0.85rem;
  }

  .final-message {
    padding: 10px 14px;
  }
}

@media (max-height: 700px) {
  .heart-svg {
    width: 100px;
    height: 90px;
  }
  
  .photo-card {
    padding: 8px;
  }
  
  .photo-caption {
    font-size: 0.75rem;
  }
}
</style>