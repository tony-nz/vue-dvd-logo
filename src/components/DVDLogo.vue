<template>
  <div
    class="absolute w-[350px] h-[310px]"
    :style="{ 'background-color': tvBackground }"
  />
  <div
    class="relative"
    :style="{
      backgroundImage: 'url(assets/tv.png)',
      backgroundSize: 'contain',
      backgroundRepeat: 'no-repeat',
      backgroundPosition: 'center',
      width: '400px',
      height: '400px',
    }"
  >
    <div
      ref="screenRef"
      class="absolute inset-2 bg-transparent overflow-hidden"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="48"
        height="48"
        viewBox="0 0 48 48"
        class="absolute flex items-center justify-center"
        :style="{
          width: `${logoSize}px`,
          height: `${logoSize}px`,
          left: `${logoPosition.x}px`,
          top: `${logoPosition.y}px`,
        }"
      >
        <path
          :fill="logoFillColor"
          d="M24.002 27c-12.154 0-22 1.343-22 3.006c0 1.653 9.845 2.994 22 2.994c12.156 0 22-1.341 22-2.994c0-1.663-9.844-3.006-22-3.006m0 3.972c-2.863 0-5.191-.494-5.191-1.104c0-.609 2.329-1.104 5.191-1.104s5.193.495 5.193 1.104s-2.331 1.104-5.193 1.104"
        />
        <path
          :fill="logoFillColor"
          d="m21.29 15l2.371 6.43L29.25 15h9.486c4.647 0 7.906 2.148 7.158 4.904c-.745 2.756-5.178 4.904-9.803 4.904h-6.295s.141-.043.172-.126c.246-.944 1.707-6.264 1.725-6.347c.02-.102-.105-.133-.105-.133h4.572s-.088-.006-.125.133a758 758 0 0 0-1.145 4.176c-.023.094-.162.139-.162.139h1.094c2.594 0 5.047-.828 5.563-2.748c.473-1.752-1.244-2.746-4.039-2.746h-1.014l-4.375.004l-10.043 9.932l-3.845-9.754s-.036-.066-.065-.147c-.014-.026-.108-.106-.206-.063c-.065.036-.074.117-.066.146c.036.066.042.08.048.111c.569.93.467 2.009.33 2.52c-.774 2.75-5.186 4.904-9.812 4.904H2.002s.149-.043.172-.126c.254-.946 1.717-6.294 1.726-6.347c.018-.09-.099-.133-.099-.133h4.604s-.132.037-.158.131c-.024.078-.954 3.432-1.151 4.178c-.023.094-.178.139-.178.139h1.118c2.597 0 5.032-.828 5.547-2.748c.472-1.752-1.23-2.746-4.021-2.746H4.089s.125-.059.147-.139c.123-.443.497-1.834.515-1.899c.02-.072-.105-.119-.105-.119z"
        />
      </svg>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, onUnmounted, ref } from "vue";

export default defineComponent({
  name: "DVDLogo",
  setup() {
    const animationFrame = ref(0);
    const logoDirection = ref({ dx: 2, dy: 2 });
    const logoFillColor = ref("#42a5f5"); // Initial fill color
    const logoPosition = ref({ x: 50, y: 50 });
    const logoSize = 100; // Logo size in pixels
    const screenRef = ref<HTMLDivElement | null>(null);
    const speed = ref(0.5);
    const tvBackground = ref("#000");

    // Define padding offsets for the TV cut-out
    const topOffset = 25;
    const bottomOffset = 70;
    const leftOffset = 30;
    const rightOffset = 30;

    /**
     * Generate a random color
     * @returns {string} Random color in hex format
     */
    const getRandomColor = () => {
      const letters = "0123456789ABCDEF";
      let color = "#";
      for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    };

    /**
     * Move the logo within the TV cut-out area
     * @returns {void}
     */
    const moveLogo = () => {
      const screen = screenRef.value;
      if (!screen) return;

      const { offsetWidth, offsetHeight } = screen;

      // Calculate the next position of the logo
      const nextX = logoPosition.value.x + logoDirection.value.dx * speed.value;
      const nextY = logoPosition.value.y + logoDirection.value.dy * speed.value;

      // Define boundaries considering the padding offsets
      const minX = leftOffset;
      const minY = topOffset;
      const maxX = offsetWidth - logoSize - rightOffset;
      const maxY = offsetHeight - logoSize - bottomOffset;

      // Check for boundary collisions and change color on collision
      let hitCorner = false;

      if (nextX <= minX || nextX >= maxX) {
        logoDirection.value.dx *= -1; // Reverse direction horizontally
        logoPosition.value.x = Math.min(Math.max(nextX, minX), maxX);
        hitCorner = true;
      }
      if (nextY <= minY || nextY >= maxY) {
        logoDirection.value.dy *= -1; // Reverse direction vertically
        logoPosition.value.y = Math.min(Math.max(nextY, minY), maxY);
        hitCorner = true;
      }

      // Change color if it hits a corner
      if (hitCorner) {
        logoFillColor.value = getRandomColor();
      }

      // Update position
      logoPosition.value.x += logoDirection.value.dx * speed.value;
      logoPosition.value.y += logoDirection.value.dy * speed.value;
      // Request the next animation frame
      animationFrame.value = requestAnimationFrame(moveLogo);
    };

    onMounted(() => {
      // Initialize the position at the center of the cut-out area
      const screen = screenRef.value;
      if (screen) {
        const { offsetWidth, offsetHeight } = screen;
        logoPosition.value.x = (offsetWidth - logoSize) / 2;
        logoPosition.value.y = (offsetHeight - logoSize) / 2;
      }

      animationFrame.value = requestAnimationFrame(moveLogo);
    });

    onUnmounted(() => {
      cancelAnimationFrame(animationFrame.value);
    });

    return {
      logoFillColor,
      logoPosition,
      logoSize,
      screenRef,
      tvBackground,
    };
  },
});
</script>
