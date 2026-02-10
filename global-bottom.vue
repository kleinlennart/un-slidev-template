<template>
  <footer
    v-if="$nav.currentPage > 1"
    class="absolute bottom-4 right-4 text-xs text-black"
  >
    {{ $nav.currentPage }}
  </footer>
</template>
