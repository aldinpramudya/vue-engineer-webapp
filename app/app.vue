<script setup>
useHead({
  titleTemplate: "Web Engineer App"
})
</script>

<template>
  <div>
    <!-- Nuxt Component Default Testing -->
    <NuxtRouteAnnouncer />
    <!-- Nuxt Component Default End -->
    <AppNavbar />
    <main class="px-40 py-10">
      <NuxtPage />
    </main>
    <AppFooter />
  </div>
</template>
