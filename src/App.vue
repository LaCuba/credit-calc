<template>
  <div id="app" class="app">
    <credit-page />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue"
import CreditPage from "./components/CreditPage.vue"

export default defineComponent({
  name: "App",
  components: {
    CreditPage,
  },
})
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");
/* Reset css */

* {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  *,
  *::before,
  *::after {
    box-sizing: border-box;
  }
}

.app {
  width: 1440px;
  margin: 60px auto;
  padding: 0 60px;
}
</style>
