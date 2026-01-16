<template>
  <UApp>
    <div class="w-full min-h-screen flex items-center justify-center bg-gray-100">
      <UContainer>
        <div class="text-center">
          <h1 class="text-3xl font-bold mb-8 text-gray-800">Birthday Card Preview</h1>
          <p class="mb-8 text-gray-600">Click below to open the card</p>
          
        <UModal :close="false" :prevent-close="isLocked" :dismissible="!isLocked" :ui="{ body: 'p-0 sm:p-0' }">
            <UButton 
              label="Open Birthday Card" 
              size="xl"
              color="primary"
              variant="solid"
            />

            <template #body>
              <BirthdayCard 
                recipient-name="Sponge Bob"
                message="Wishing you a beautiful year ahead, full of inspiration, cozy moments, and people who get you."
                sender-name="HRMS 3.0"
                image-src="/sponge-bob-at-office.jpg"
                cake-src="/birthday-cake.svg"
                @animation-start="startLockTimer" 
              />
            </template>
          </UModal>
        </div>
      </UContainer>
    </div>
  </UApp>
</template>

<script setup>
import { ref } from 'vue';

const isLocked = ref(false);

const startLockTimer = () => {
    isLocked.value = true;
    setTimeout(() => {
        isLocked.value = false;
    }, 5000);
};
</script>
