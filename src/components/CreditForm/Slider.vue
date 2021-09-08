<template>
  <div class="slider__container">
    <div class="slider__value">
      {{ displayRes }}
    </div>
    <div class="slider__label">{{ label }}</div>
    <div class="slider__tracker" ref="slider" @mousedown="startDrag($event)">
      <div
        class="slider__thumb"
        :style="{
          width: `${progress}%`,
        }"
      ></div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue"

const clamp = (value: number, min: number, max: number) => {
  if (value < min) {
    return min
  }
  if (value > max) {
    return max
  }
  return value
}

export default defineComponent({
  name: "Slider",
  props: {
    min: String,
    max: String,
    step: String,
    beginValue: String,
    displayRes: String,
    label: String,
  },
  emits: ["progress"],
  data() {
    return {
      drugStep: 100,
      progress:
        (Number(this.beginValue) - Number(this.min)) /
        ((Number(this.max) - Number(this.min)) / 100),
    }
  },
  methods: {
    startDrag(event: MouseEvent) {
      this.moveAt(event)
      document.addEventListener("mousemove", this.moveAt)
      document.addEventListener("mouseup", this.dragStop)
    },
    moveAt(event: MouseEvent) {
      this.drugStep = (Number(this.max) - Number(this.min)) / Number(this.step)
      const bounds = this.$refs.slider.getBoundingClientRect()
      const realX = clamp(event.pageX - bounds.x, 1, bounds.width)
      const progress = (realX / bounds.width) * 100
      this.progress =
        progress === 100
          ? progress
          : progress - (progress % (100 / this.$data.drugStep))
      const value =
        Math.round(
          ((Number(this.max) - Number(this.min)) / 100) * this.progress
        ) + Number(this.min)
      this.$emit("progress", value)
    },
    dragStop() {
      document.removeEventListener("mousemove", this.moveAt)
      document.removeEventListener("mouseup", this.dragStop)
    },
  },
  beforeUnmount() {
    this.dragStop()
  },
})
</script>

<style lang="scss">
.slider__tracker {
  position: absolute;
  left: 0;
  top: 60px;
  cursor: pointer;
  position: relative;
  border-radius: 5px;
  width: 520px;
  height: 10px;
  overflow: hidden;
  background: #dddddd;

  .slider__thumb {
    position: absolute;
    left: 0px;
    border-radius: 5px;
    width: 10%;
    height: 100%;
    background: linear-gradient(91.4deg, #4052a5 0%, #242f62 100%);
  }
}

.slider__container {
  position: relative;
  width: 520px;
  height: 70px;
  background-color: #eeeeee;
  border-radius: 5px;
}

.slider__value {
  position: absolute;
  padding: 3px 0;
  top: 30px;
  left: 15px;
  width: 490px;
  height: 25px;

  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 19px;
  color: #333333;
}

.slider__label {
  position: absolute;
  left: 15px;
  top: 15px;
  color: #999999;
  cursor: text;

  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 12px;
  line-height: 14px;
}
</style>
