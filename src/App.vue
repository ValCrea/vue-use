<script setup lang="ts">
import { ref, type Ref } from "vue";
import {
  useMouse,
  useDark,
  useToggle,
  useStorage,
  useWindowSize,
  useWindowScroll,
  useEventListener,
  onKeyStroke,
  onLongPress,
  onStartTyping,
  useBattery,
  useDevicePixelRatio,
  useDevicesList,
  useNavigatorLanguage,
  useOnline,
  useTextSelection,
  useNow,
  useDateFormat,
  useDebounceFn,
} from "@vueuse/core";

type RefMaybeStr = Ref<string | null>;

const { x: mouseX, y: mouseY } = useMouse();

const isDark = useDark();
const toggleDark = useToggle(isDark);
const toggleDarkMode = () => {
  toggleDark();
  document.documentElement.classList.toggle("dark");
};

const state = useStorage("my-store", { name: "", pass: "" }, localStorage);

const { width, height } = useWindowSize();

const { x: scrollX, y: scrollY } = useWindowScroll();

const clickLocation: RefMaybeStr = ref(null);
useEventListener(document, "click", (event: MouseEvent) => {
  clickLocation.value = `${event.clientX} ${event.clientY}`;
});

const arrowDownTime: RefMaybeStr = ref(null);
onKeyStroke("ArrowDown", () => {
  arrowDownTime.value = new Date().toLocaleString("en-UK");
});

const htmlRefHook = ref<HTMLElement | null>(null);
const longPressedHook = ref(false);
const onLongPressCallbackHook = () => {
  longPressedHook.value = true;
};
const resetHook = () => {
  longPressedHook.value = false;
};
onLongPress(htmlRefHook, onLongPressCallbackHook, {
  modifiers: { prevent: true },
});

const lastKey: RefMaybeStr = ref(null);
onStartTyping((event: KeyboardEvent) => {
  lastKey.value = event.key;
});

const { isSupported, charging } = useBattery();

const { pixelRatio } = useDevicePixelRatio();

const { devices } = useDevicesList();

const { language } = useNavigatorLanguage();

const online = useOnline();

const selected = useTextSelection();

const formattedTime = useDateFormat(useNow(), "YYYY-MM-DD HH:mm:ss");

const registeredClicks = ref(0);
const registeredEvents = ref(0);
const debouncedFn = useDebounceFn(() => {
  registeredEvents.value++;
}, 1000);
</script>

<template>
  <div class="showcase">
    <p>Mouse: {{ mouseX }} {{ mouseY }}</p>
    <button @click="toggleDarkMode()" class="btn">Toggle dark mode</button>
    <div class="showcase__element">
      <input v-model="state.name" type="text" />
      <input v-model="state.pass" type="text" />
    </div>
    <p>Width: {{ width }}, Height: {{ height }}</p>
    <p>Scroll: {{ scrollX }} {{ scrollY }}</p>
    <p v-if="clickLocation">Last click location: {{ clickLocation }}</p>
    <p v-if="arrowDownTime">Last arrow down time: {{ arrowDownTime }}</p>
    <div class="showcase__element">
      <button ref="htmlRefHook" class="btn">Long press</button>
      Pressed: {{ longPressedHook }}
      <button @click="resetHook" class="btn">Reset</button>
    </div>
    <p v-if="lastKey">Last key pressed: {{ lastKey }}</p>
    <p>
      Battery charging:
      <span v-if="isSupported">{{ charging }}</span>
      <span v-else>Api not supported</span>
    </p>
    <p>Pixel ratio: {{ pixelRatio }}</p>
    <p>Devices: {{ devices.map((device) => device.kind) }}</p>
    <p>Language: {{ language }}</p>
    <p>{{ online ? "You have Internet" : "You have no Internet" }}</p>
    <p v-if="selected.text.value != ''">Selected text: {{ selected.text }}</p>
    <p>Time: {{ formattedTime }}</p>
    <button
      @click="
        registeredClicks++;
        debouncedFn();
      "
      class="btn"
    >
      Clicks: {{ registeredClicks }} &nbsp; Events: {{ registeredEvents }}
    </button>
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

  border: solid 1px var(--border-primary);
  border-radius: 0.25rem;

  &:hover {
    background-color: var(--border-primary);
    border-color: var(--border-secondary);
  }
}
</style>
