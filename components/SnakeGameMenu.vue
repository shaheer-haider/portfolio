<template>
  <div id="console-menu" class="h-full flex flex-col items-end justify-between">
    <div>
      <div id="instructions" class="font-fira_retina text-sm text-white">
        <p>// use your keyboard</p>
        <p>// arrows to play</p>

        <div id="buttons" class="w-full flex flex-col items-center gap-1 pt-2">
          <button id="console-button" class="button-up" @click="emit('move', 'up')">
            <img src="/icons/console/arrow-button.svg" alt="" />
          </button>

          <div class="grid grid-cols-3 gap-1">
            <button id="console-button" class="button-left" @click="emit('move', 'left')">
              <img src="/icons/console/arrow-button.svg" alt="" class="-rotate-90" />
            </button>

            <button id="console-button" class="button-down" @click="emit('move', 'down')">
              <img src="/icons/console/arrow-button.svg" alt="" class="rotate-180" />
            </button>

            <button
              id="console-button"
              class="button-right"
              @click="emit('move', 'right')"
            >

              <img src="/icons/console/arrow-button.svg" alt="" class="rotate-90" />
            </button>
          </div>
        </div>
      </div>

      <!-- score board -->
      <div id="score-board" class="w-full flex flex-col pl-5">
        <p class="font-fira_retina text-white pt-5">// food left</p>

        <div id="score" class="grid grid-cols-5 gap-5 justify-items-center pt-5 w-fit">
          <div class="food"></div>
          <div class="food"></div>
          <div class="food"></div>
          <div class="food"></div>
          <div class="food"></div>
          <div class="food"></div>
          <div class="food"></div>
          <div class="food"></div>
          <div class="food"></div>
          <div class="food"></div>
        </div>
      </div>
    </div>
    <div class="w-full space-y-1 mb-1">
      <div class="flex w-full justify-between items-center text-sm">
        <label>Snake Color </label>
        <input
          :value="props.snakeColor"
          type="color"
          @change="(e)=>emit('updateSnakeColor', e.target.value)"
        />
      </div>
      <div class="flex w-full justify-between items-center text-sm">
        <label>Food Color </label>
        <input
          :value="props.foodColor"
          type="color"
          @change="(e)=>emit('updateFoodColor', e.target.value)"

        />
      </div>
    </div>
    <!-- skip -->
    <NuxtLink
      id="skip-btn"
      to="/about-me"
      class="font-fira_retina flex hover:bg-white/20"
    >
      skip
    </NuxtLink>
  </div>
</template>

<script setup>
const emit = defineEmits(["move", "updateSnakeColor", "updateFoodColor"]);
const props = defineProps(["snakeColor", "foodColor"]);
</script>

<style>
input[type="color"] {
  border-radius: 5px;
  padding: 0;
  border: 0PX;
  height: 14px;
  width: 28px;
}

input[type="color"]::-moz-color-swatch {
  border: none;
}

input[type="color"]::-webkit-color-swatch-wrapper {
  padding: 0;
  border-radius: 0;
}

input[type="color"]::-webkit-color-swatch {
  border: none;
}
</style>
