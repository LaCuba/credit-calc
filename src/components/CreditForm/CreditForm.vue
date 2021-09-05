<template>
  <div class="credit-form">
    <h1 class="credit-form__header">Заполните заявку онлайн</h1>
    <div class="credit-form__sliders">
      <div class="slider-sum-credit">
        <div class="slider-sum-credit__container">
          <div class="slider-sum-credit__value">
            {{ creditSummRes }}
          </div>
          <div class="slider-sum-credit__label">Желаемая сумма кредита*</div>
          <input
            class="slider-sum-credit__range"
            type="range"
            :min="300000"
            v-model="creditSumm"
            :max="8000000"
            :step="100000"
          />
        </div>
        <div class="slider-sum-credit__min-value">300 000 ₽</div>
        <div class="slider-sum-credit__max-value">8 000 000 ₽</div>
      </div>
      <div class="slider-period-credit">
        <div class="slider-period-credit__container">
          <div class="slider-period-credit__value">
            {{ creditPeriodRes }}
          </div>
          <label for="inputId" class="slider-period-credit__label">
            Срок кредита
          </label>
          <input
            class="slider-period-credit__range"
            type="range"
            :min="1"
            v-model="creditPeriod"
            :max="240"
            :step="1"
          />
        </div>
        <div class="slider-period-credit__min-value">1 месяц</div>
        <div class="slider-period-credit__max-value">20 лет</div>
      </div>
    </div>
    <div class="credit-form__summ">
      Ваш ежемесячный платеж * - {{ monthlyPayment }} ₽
    </div>
    <div class="credit-form__contacts">
      <div class="credit-form__full-name">
        <input
          type="text"
          id="fullName"
          class="credit-form__input"
          autocomplete="off"
          placeholder=" "
          v-model="fullName"
        />
        <label for="fullName" class="credit-form__label">
          Фамилия Имя Отчество <i>*</i>
        </label>
      </div>
      <div class="credit-form__phone">
        <input
          type="text"
          id="phone"
          class="credit-form__input"
          autocomplete="off"
          placeholder=" "
          v-model="phone"
          @input="acceptNumber"
        />
        <label for="phone" class="credit-form__label"> Телефон <i>*</i> </label>
      </div>
      <div class="credit-form__email">
        <input
          type="text"
          id="email"
          class="credit-form__input"
          autocomplete="off"
          placeholder=" "
          v-model="email"
        />
        <label for="email" class="credit-form__label">
          Электронная почта
        </label>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue"

export default defineComponent({
  name: "CreditForm",
  data() {
    return {
      creditPeriod: "60",
      creditSumm: "3000000",
      fullName: "",
      phone: "",
      email: "",
    }
  },
  computed: {
    creditSummRes(): string {
      return this.creditSumm.replace(/(\d)(?=(\d{3})+([^\d]|$))/g, "$1 ") + " ₽"
    },
    creditPeriodRes(): string {
      if (Number(this.creditPeriod) < 12) {
        const month = this.declOfNum(Number(this.creditPeriod), [
          "месяц",
          "месяца",
          "месяцев",
        ])
        return `${this.creditPeriod} ${month}`
      } else if (Number(this.creditPeriod) % 12 === 0) {
        const year = Number(this.creditPeriod) / 12
        return `${year} ${this.declOfNum(year, ["год", "года", "лет"])}`
      } else {
        const yearVal = Math.floor(Number(this.creditPeriod) / 12)
        const year = this.declOfNum(yearVal, ["год", "года", "лет"])
        const month = this.declOfNum(Number(this.creditPeriod) - yearVal * 12, [
          "месяц",
          "месяца",
          "месяцев",
        ])
        return `${yearVal} ${year} ${
          Number(this.creditPeriod) - yearVal * 12
        } ${month}`
      }
    },
    monthlyPayment(): number {
      const percent = 8.8 / 12
      const res =
        Number(this.creditSumm) *
        ((percent * Math.pow(1 + percent / 100, Number(this.creditPeriod))) /
          100 /
          (Math.pow(1 + percent / 100, Number(this.creditPeriod)) - 1))
      return Math.floor(res)
    },
  },
  methods: {
    declOfNum(num: number, titles: string[]): string {
      const cases = [2, 0, 1, 1, 1, 2]
      return titles[
        num % 100 > 4 && num % 100 < 20 ? 2 : cases[num % 10 < 5 ? num % 10 : 5]
      ]
    },
    acceptNumber() {
      const x = this.phone
        .replace(/\D/g, "")
        .match(/(\d{0,3})(\d{0,3})(\d{0,4})/)
      if (x !== null) {
        this.phone = !x[2]
          ? x[1]
          : "(" + x[1] + ") " + x[2] + (x[3] ? "-" + x[3] : "")
      }
      console.log(this.fullName)
    },
  },
})
</script>

<style lang="scss">
.credit-form__header {
  padding: 40px 0;

  font-family: PT Sans Caption;
  font-style: normal;
  font-weight: bold;
  font-size: 36px;
  line-height: 47px;

  color: #000000;
  &::after {
    margin-top: 40px;
    width: 100%;
    height: 1px;
    background-color: #eeeeee;
    content: "";
    display: block;
  }
}

.credit-form__sliders {
  display: flex;
  gap: 40px;
}

.slider-sum-credit {
  max-width: 520px;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: 70px 20px;
  grid-template-areas:
    "slider slider"
    "minValue maxValue";
  gap: 11px;
}

.slider-sum-credit__container {
  grid-area: slider;
  position: relative;
  width: 520px;
  height: 70px;
  background-color: #eeeeee;
  border-radius: 5px;
}

.slider-sum-credit__value {
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

.slider-sum-credit__label {
  position: absolute;
  left: 15px;
  top: 15px;
  color: #999999;
  cursor: text;
  // transition: top 200ms ease-in, left 200ms ease-in, font-size 200ms ease-in;

  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 12px;
  line-height: 14px;
}

.slider-sum-credit__range {
  overflow: hidden;
  position: absolute;
  margin: 0;
  left: 0;
  top: 60px;
  width: 520px;
  height: 10px;
  border-radius: 5px;
  -webkit-appearance: none;
  outline: none;
  background-color: #dddddd;
}

.slider-sum-credit__range::-webkit-slider-thumb {
  border: none;
  width: 10px;
  -webkit-appearance: none;
  height: 10px;
  cursor: ew-resize;
  background: #4052a5;
  box-shadow: -520px 0 0 515px #4052a5;
}

.slider-sum-credit__range::-moz-range-thumb {
  border: none;
  width: 10px;
  -webkit-appearance: none;
  height: 10px;
  cursor: ew-resize;
  background: #4052a5;
  box-shadow: -520px 0 0 515px #4052a5;
}
.slider-sum-credit__min-value {
  grid-area: minValue;
}
.slider-sum-credit__max-value {
  grid-area: maxValue;
  justify-self: end;
}
.slider-sum-credit__min-value,
.slider-sum-credit__max-value {
  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 19px;
  color: #999999;
}

.slider-period-credit {
  max-width: 520px;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: 70px 20px;
  grid-template-areas:
    "slider slider"
    "minValue maxValue";
  gap: 11px;
}

.slider-period-credit__container {
  grid-area: slider;
  position: relative;
  width: 520px;
  height: 70px;
  background-color: #eeeeee;
  border-radius: 5px;
}

.slider-period-credit__value {
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

.slider-period-credit__label {
  position: absolute;
  left: 15px;
  top: 15px;
  color: #999999;
  cursor: text;
  // transition: top 200ms ease-in, left 200ms ease-in, font-size 200ms ease-in;

  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 12px;
  line-height: 14px;
}

.slider-period-credit__range {
  overflow: hidden;
  position: absolute;
  margin: 0;
  left: 0;
  top: 60px;
  width: 520px;
  height: 10px;
  border-radius: 5px;
  -webkit-appearance: none;
  outline: none;
  background-color: #dddddd;
}

.slider-period-credit__range::-webkit-slider-thumb {
  border: none;
  width: 10px;
  -webkit-appearance: none;
  height: 10px;
  cursor: ew-resize;
  background: #4052a5;
  box-shadow: -520px 0 0 515px #4052a5;
}

.slider-period-credit__range::-moz-range-thumb {
  border: none;
  width: 10px;
  -webkit-appearance: none;
  height: 10px;
  cursor: ew-resize;
  background: #4052a5;
  box-shadow: -520px 0 0 515px #4052a5;
}
.slider-period-credit__min-value {
  grid-area: minValue;
}
.slider-period-credit__max-value {
  grid-area: maxValue;
  justify-self: end;
}
.slider-period-credit__min-value,
.slider-period-credit__max-value {
  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 19px;
  color: #999999;
}

.credit-form__summ {
  padding-top: 40px;
  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 24px;
  line-height: 28px;
  text-align: center;

  color: #000000;
}

.credit-form__contacts {
  margin-top: 40px;
  display: grid;
  grid-template-columns: repeat(3, 333px);
  gap: 40.333px;
}

.credit-form__full-name {
  position: relative;
  width: 333px;
  height: 60px;
}
.credit-form__phone {
  position: relative;
  width: 333px;
  height: 60px;
}
.credit-form__email {
  position: relative;
  width: 333px;
  height: 60px;
}
.credit-form__input {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: 2px solid #e1e5ee;
  border-radius: 5px;
  color: #333333;
  outline: none;
  padding: 25px 15px 10px 15px;
  background: #eeeeee;

  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 19px;

  &:hover {
    background: #ffffff;
    border: 2px solid #3c4ea3;
  }

  &:focus {
    background: #ffffff;
    border: 2px solid #3c4ea3;
  }
}

.credit-form__label {
  position: absolute;
  left: 15px;
  top: 20px;
  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 19px;
  color: #333333;
  cursor: text;
  transition: top 200ms ease-in, left 200ms ease-in, font-size 200ms ease-in;
  background-color: #eeeeee;

  i {
    color: red;
  }
}

.credit-form__input:hover ~ .credit-form__label {
  background: #ffffff;
  color: #5168d1;
}
.credit-form__input:focus ~ .credit-form__label {
  background: #ffffff;
  color: #5168d1;
}

.credit-form__input:focus ~ .credit-form__label,
.credit-form__input:not(:placeholder-shown).credit-form__input:not(:focus)
  ~ .credit-form__label {
  top: 10px;
  font-size: 12px;
  left: 15px;
}
</style>
