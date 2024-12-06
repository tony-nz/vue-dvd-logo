<template>
  <div class="p-2 flex justify-end bg-black">
    <a
      href="https://github.com/tony-nz/vue-dvd-logo"
      target="_blank"
      class="text-xs text-gray-400"
    >
      <span class="mr-2"
        >Created by tony-nz
        <span class="text-xs text-gray-300">(Tony Myers)</span></span
      >
      <img
        src="/assets/github.svg"
        alt="github"
        class="w-4 h-4 inline-block mr-2"
      />
    </a>
  </div>
  <div
    class="relative w-full h-screen flex items-center justify-center bg-slate-900"
  >
    <DVDLogo />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import DVDLogo from "./components/DVDLogo.vue";

export default defineComponent({
  name: "App",
  components: {
    DVDLogo,
  },
  setup() {
    return {
      //
    };
  },
});
</script>
