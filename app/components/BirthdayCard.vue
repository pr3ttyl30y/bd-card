<template>
  <div class="relative w-full mx-auto overflow-hidden bg-[#F9F7F2] rounded-lg aspect-[3/4] p-8">
    <!-- Background Texture -->
    <img src="/background-texture.svg" alt="" class="absolute inset-0 w-full h-full object-cover opacity-50 pointer-events-none" />

    <!-- Balloons -->
    <img src="/blue-balloon-1.svg" class="absolute top-4 left-4 w-24 animate-float-slow transform -rotate-12 z-0" alt="" />
    <img src="/yellow-balloon-2.svg" class="absolute top-24 right-8 w-28 animate-float-medium transform rotate-6 z-20" alt="" /> <!-- Overlap Top Right -->
    <img src="/yellow-balloon-1.svg" class="absolute bottom-48 left-12 w-24 animate-float-fast transform -rotate-6 z-20" alt="" /> <!-- Overlap Bottom Left -->
    <img src="/blue-balloon-2.svg" class="absolute bottom-12 right-6 w-28 animate-float-slow transform rotate-12 z-0" alt="" />
    
    <!-- Props -->
    <img :src="cakeSrc" class="absolute bottom-[20%] left-12 w-32 animate-float-medium z-30" alt="Birthday Cake" />

    <!-- Title -->
    <h1 v-if="showTitle" class="font-['Caveat',sans-serif] text-gray-500 text-xl absolute top-4 left-4 z-10 w-full">{{ title }}</h1>

    <!-- Image Container -->
    <div class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 w-3/4 aspect-square z-10">
       <div class="w-full h-full border-4 border-white shadow-md overflow-hidden relative">
            <!-- Placeholder for 'person running/blurred' -->
            <img :src="imageSrc" 
                 alt="Birthday Person" 
                 class="w-full h-full object-cover" />
       </div>
    </div>

    <!-- Message -->
    <div class="absolute bottom-16 left-0 right-0 px-12 text-center z-10">
      <p class="font-sans text-sm font-medium text-gray-800 leading-relaxed tracking-wide">
        Happy birthday, {{ recipientName }}! {{ message }}
      </p>
    </div>

    <!-- Signature -->
    <div class="absolute bottom-8 right-12 z-10">
        <p class="font-['Caveat',cursive] text-2xl text-gray-800">{{ senderName }}</p>
    </div>


    <!-- Celebration Effects -->
    <div class="absolute inset-0 pointer-events-none z-20 overflow-hidden">
      <!-- Sparkles -->
      <svg class="absolute top-12 left-1/4 w-6 h-6 text-yellow-400 animate-twinkle" viewBox="0 0 24 24" fill="currentColor">
        <path d="M12 0L14.59 9.41L24 12L14.59 14.59L12 24L9.41 14.59L0 12L9.41 9.41L12 0Z" />
      </svg>
      <svg class="absolute bottom-32 right-1/4 w-4 h-4 text-yellow-500 animate-twinkle animation-delay-1000" viewBox="0 0 24 24" fill="currentColor">
        <path d="M12 0L14.59 9.41L24 12L14.59 14.59L12 24L9.41 14.59L0 12L9.41 9.41L12 0Z" />
      </svg>
      <svg class="absolute top-1/3 right-12 w-5 h-5 text-yellow-300 animate-twinkle animation-delay-500" viewBox="0 0 24 24" fill="currentColor">
        <path d="M12 0L14.59 9.41L24 12L14.59 14.59L12 24L9.41 14.59L0 12L9.41 9.41L12 0Z" />
      </svg>

      <!-- Confetti / Fireworks bits -->
      <div class="absolute top-20 left-10 w-2 h-2 rounded-full bg-red-400 animate-pop"></div>
      <div class="absolute bottom-40 right-20 w-3 h-3 rounded-full bg-blue-400 animate-pop animation-delay-700"></div>
      <div class="absolute top-1/2 left-8 w-2 h-2 rounded-full bg-green-400 animate-pop animation-delay-300"></div>
      <div class="absolute top-10 right-1/3 w-1.5 h-1.5 rounded-full bg-purple-400 animate-pop animation-delay-1500"></div>
    </div>

  </div>

<Teleport to="body">
  <div class="fixed inset-0 pointer-events-none z-[99999] overflow-hidden" v-if="showConfetti">
    <div 
      v-for="particle in particles" 
      :key="particle.id"
      class="absolute rounded-full"
      :style="{
        left: particle.x + 'px',
        top: particle.y + 'px',
        width: particle.size + 'px',
        height: particle.size + 'px',
        backgroundColor: particle.color,
        transform: `rotate(${particle.rotation}deg)`,
        opacity: particle.opacity
      }"
    ></div>
  </div>
</Teleport>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const emit = defineEmits(['animation-start']);

const props = defineProps({
  recipientName: {
    type: String,
    default: 'Sponge Bob'
  },
  message: {
    type: String,
    default: 'Wishing you a beautiful year ahead, full of inspiration, cozy moments, and people who get you.'
  },
  senderName: {
    type: String,
    default: 'HRMS 3.0'
  },
  imageSrc: {
    type: String,
    default: '/sponge-bob-at-office.jpg'
  },
  cakeSrc: {
    type: String,
    default: '/birthday-cake.svg'
  },
  title: {
    type: String,
    default: 'Whimsical balloon birthday card'
  },
  showTitle: {
    type: Boolean,
    default: false
  }
});

const showConfetti = ref(true);
const particles = ref([]);
let animationId = null;

const colors = ['#EF4444', '#3B82F6', '#10B981', '#F59E0B', '#8B5CF6', '#EC4899'];

const createParticle = (x, y, angle, velocity) => {
  const radian = angle * (Math.PI / 180);
  return {
    id: Math.random(),
    x,
    y,
    vx: Math.cos(radian) * velocity * (Math.random() * 0.5 + 0.8), // slight variation
    vy: Math.sin(radian) * velocity * (Math.random() * 0.5 + 0.8),
    size: Math.random() * 6 + 4,
    color: colors[Math.floor(Math.random() * colors.length)],
    rotation: Math.random() * 360,
    rotationSpeed: (Math.random() - 0.5) * 10,
    opacity: 1,
    life: 1
  };
};

const triggerBurst = () => {
    const width = window.innerWidth;
    const height = window.innerHeight;
    
    for (let i = 0; i < 100; i++) {
        // Left corner
        particles.value.push(createParticle(0, height, -60 + (Math.random() - 0.5) * 60, Math.random() * 25 + 15));
        // Right corner
        particles.value.push(createParticle(width, height, -120 + (Math.random() - 0.5) * 60, Math.random() * 25 + 15));
    }
};

const updateParticles = () => {
  particles.value.forEach(p => {
    p.x += p.vx;
    p.y += p.vy;
    p.vy += 0.12; // Reduced gravity for slower fall
    p.vx *= 0.96; // Increased drag/air resistance
    p.rotation += p.rotationSpeed;
    p.life -= 0.003; // Longer life
    p.opacity = Math.min(1, p.life * 2);
  });

  particles.value = particles.value.filter(p => p.life > 0 && p.y < window.innerHeight + 100);
  
  if (particles.value.length > 0 || showConfetti.value) {
      animationId = requestAnimationFrame(updateParticles);
  }
};


onMounted(() => {
    emit('animation-start');
    updateParticles();

    // Burst 1: Immediate
    triggerBurst();
    
    // Burst 2: After 1.5s
    setTimeout(() => {
        triggerBurst();
    }, 1500);

    // Burst 3: After 3s
    setTimeout(() => {
        triggerBurst();
    }, 3000);
    
    // Cleanup container after enough time for all bursts to finish
    setTimeout(() => {
        showConfetti.value = false; 
    }, 12000); 
});

onUnmounted(() => {
  if (animationId) cancelAnimationFrame(animationId);
});
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Caveat:wght@400;700&display=swap');

.animate-float-slow {
  animation: float 6s ease-in-out infinite;
}
.animate-float-medium {
  animation: float 5s ease-in-out infinite;
}
.animate-float-fast {
  animation: float 4s ease-in-out infinite;
}

.animate-twinkle {
  animation: twinkle 2s ease-in-out infinite;
}
.animate-pop {
  animation: pop 3s ease-in-out infinite;
}

/* Utility for animation delays if not using Tailwind plugin */
.animation-delay-300 { animation-delay: 300ms; }
.animation-delay-500 { animation-delay: 500ms; }
.animation-delay-700 { animation-delay: 700ms; }
.animation-delay-1000 { animation-delay: 1000ms; }
.animation-delay-1500 { animation-delay: 1500ms; }

@keyframes float {
  0% { transform: translate(0, 0); }
  33% { transform: translate(10px, -25px); }
  66% { transform: translate(-10px, -15px); }
  100% { transform: translate(0, 0); }
}

@keyframes twinkle {
  0%, 100% { opacity: 0.3; transform: scale(0.8); }
  50% { opacity: 1; transform: scale(1.2); }
}

@keyframes pop {
  0% { transform: scale(0); opacity: 0; }
  50% { transform: scale(1.2); opacity: 1; }
  100% { transform: scale(1); opacity: 0; }
}
</style>
