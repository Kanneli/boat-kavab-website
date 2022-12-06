<script setup>
import zayanImg from '@/assets/Zayan.png'
</script>

<template>
  <main>
    <div>
      <div class="hidden md:flex md:flex-row md:justify-between">
        <div class="flex">
        </div>
        <div class="flex items-center h-screen">
          <h1 class="mr-20 ml-6">
            Kavab coming soon!
          </h1>
        </div>
      </div>
      <div class="md:hidden mt-6">
        <h1>
          Kavab coming soon!
        </h1>
      </div>
      <div class="fixed bottom-0 h-screen w-screen bg-no-repeat bg-left-bottom bg-contain" :style="`background-image: url('${zayanImg}')` "></div>
    </div>
  </main>
</template>
