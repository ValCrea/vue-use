<script setup lang="ts">
import { useMouse, useDark, useToggle } from "@vueuse/core";

const isDark = useDark();
const toggleDark = useToggle(isDark);
const { x, y } = useMouse();
</script>

<template>
  <div :class="{ dark: isDark }" class="all">
    <p @click="toggleDark()">Mouse at: {{ x }} {{ y }}</p>
  </div>
</template>

<style scoped>
.all {
  width: 100%;
  height: 100vh;
}

.dark {
  background-color: black;
  color: white;
}
</style>
