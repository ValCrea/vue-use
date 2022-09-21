<script setup lang="ts">
import { useMouse, useDark, useToggle, useStorage } from "@vueuse/core";

const isDark = useDark();
const toggleDark = useToggle(isDark);
const { x, y } = useMouse();

const toggleDarkMode = () => {
  toggleDark();
  document.documentElement.classList.toggle("dark");
};

const state = useStorage("my-store", { name: "", pass: "" }, localStorage);
</script>

<template>
  <div class="showcase">
    <p>Mouse at: {{ x }} {{ y }}</p>
    <button @click="toggleDarkMode()" class="btn">Toggle dark mode</button>
    <div class="showcase__element">
      <input v-model="state.name" type="text" />
      <input v-model="state.pass" type="text" />
    </div>
  </div>
</template>

<style scoped lang="scss">
.showcase {
  margin-block: 5rem;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1rem;

  &__element {
    display: flex;
    flex-direction: row;
    align-items: baseline;
    justify-content: space-evenly;
    gap: 1rem;
  }
}
.btn {
  padding: 0.2rem;

  color: var(--text-secondary);
  background-color: var(--background-secondary);

  border: solid 1px var(--border-color);
  border-radius: 0.25rem;
}
</style>
