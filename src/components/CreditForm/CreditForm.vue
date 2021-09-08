<template>
  <form class="credit-form" @submit.prevent="onSubmitCreditForm">
    <h1 class="credit-form__header">Заполните заявку онлайн</h1>
    <div class="credit-form__sliders">
      <div class="slider-sum-credit">
        <div class="slider-sum-credit__slider-container">
          <Slider
            min="300000"
            max="8000000"
            beginValue="3000000"
            step="100000"
            @progress="updaterCreditSumm"
            :displayRes="creditSummRes"
            label="Желаемая сумма кредита*"
          />
        </div>
        <div class="slider-sum-credit__min-value">300 000 ₽</div>
        <div class="slider-sum-credit__max-value">8 000 000 ₽</div>
      </div>
      <div class="slider-period-credit">
        <div class="slider-period-credit__slider-container">
          <Slider
            min="1"
            max="240"
            beginValue="60"
            step="1"
            @progress="updaterCreditPeriod"
            :displayRes="creditPeriodRes"
            label="Срок кредита"
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
          required
        />
        <span class="credit-form__input-border"> </span>
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
          @click="installMask"
          @keydown="acceptNumber"
          required
        />
        <span class="credit-form__input-border"> </span>
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
        <span class="credit-form__input-border"> </span>
        <label for="email" class="credit-form__label">
          Электронная почта
        </label>
      </div>
    </div>
    <div class="credit-form__submit__container">
      <div class="credit-form__submit">
        <div class="credit-form__submit__conditions">
          <div class="credit-form__submit__checkbox-container">
            <input
              type="checkbox"
              class="credit-form__submit__checkbox"
              id="conditionsCheckbox"
              v-model="conditions"
              required
            />
            <span class="credit-form__submit__border"></span>
            <span class="credit-form__submit__vector"></span>
          </div>
          <label class="credit-form__submit__label" for="conditionsCheckbox">
            Я принимаю условия передачи информации</label
          >
        </div>
        <button @click="submit" class="credit-form__submit__btn">
          ОТПРАВИТЬ
        </button>
      </div>
    </div>
  </form>
</template>

<script lang="ts">
import { defineComponent } from "vue"
import Slider from "./Slider.vue"

export default defineComponent({
  components: { Slider },
  name: "CreditForm",
  data() {
    return {
      creditPeriod: "60",
      creditSumm: "3000000",
      fullName: "",
      phone: "",
      email: "",
      conditions: false,
    }
  },
  computed: {
    monthlyPayment(): number {
      const percent = 8.8 / 12
      const res =
        Number(this.creditSumm) *
        ((percent * Math.pow(1 + percent / 100, Number(this.creditPeriod))) /
          100 /
          (Math.pow(1 + percent / 100, Number(this.creditPeriod)) - 1))
      return Math.floor(res)
    },
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
  },
  methods: {
    updaterCreditSumm(value: number) {
      this.creditSumm = value.toString()
    },
    updaterCreditPeriod(value: number) {
      this.creditPeriod = value.toString()
    },
    declOfNum(num: number, titles: string[]): string {
      const cases = [2, 0, 1, 1, 1, 2]
      return titles[
        num % 100 > 4 && num % 100 < 20 ? 2 : cases[num % 10 < 5 ? num % 10 : 5]
      ]
    },
    installMask(event: Event) {
      let element = event.target as HTMLInputElement
      if (element.value === "") {
        element.value = "+7 "
      }
    },
    acceptNumber(event: KeyboardEvent) {
      let element = event.target as HTMLInputElement
      let oldValue = element.value
      let caretPosition = element.selectionStart
      let isCaretInEndPosition =
        caretPosition && oldValue.substring(caretPosition).search(/\d/) === -1
      let caretMinPosition = 4 // минимально допустимая позиция каретки (не заходить на '+7 (' )

      if (caretPosition !== null) {
        if (event.type === "keydown") {
          let key = event.key
          if (
            key === "Backspace" &&
            oldValue
              .substring(caretPosition - 1, caretPosition)
              .search(/[\s)-]/) !== -1
          ) {
            let shift = 1 // на сколько сдвинуть каретку
            if (oldValue.substring(caretPosition - 2, caretPosition) === ") ")
              shift = 2
            element.setSelectionRange(
              caretPosition - shift,
              caretPosition - shift
            )
          }
          if (
            key === "Delete" &&
            oldValue
              .substring(caretPosition, caretPosition + 1)
              .search(/[\s)-]/) !== -1
          ) {
            let shift = 1
            if (oldValue.substring(caretPosition, caretPosition + 2) === ") ")
              shift = 2
            element.setSelectionRange(
              caretPosition + shift,
              caretPosition + shift
            )
          }

          if (key === "ArrowLeft" && caretPosition === caretMinPosition)
            event.preventDefault()
          if (key === "ArrowRight" && isCaretInEndPosition)
            event.preventDefault()

          if (key === "ArrowUp") key = "Home"
          if (key === "ArrowDown") key = "End"

          if (key === "Home") {
            element.setSelectionRange(caretMinPosition, caretMinPosition)
            event.preventDefault()
          }

          if (key === "End") {
            let caretMaxPosition = oldValue.indexOf("_")
            if (caretMaxPosition !== -1) {
              element.setSelectionRange(caretMaxPosition, caretMaxPosition)
              event.preventDefault()
            }
          }

          return
        }

        // вычисляем значение value элемента
        let newValue = oldValue // при событии focus & click значение value не меняется
        if (event.type === "input") {
          let matrix = "+7 (___) ___-__-__",
            i = 0,
            def = matrix.replace(/\D/g, ""),
            val = oldValue.replace(/\D/g, "")
          newValue = matrix.replace(/[_\d]/g, function (match) {
            return i < val.length ? val.charAt(i++) || def.charAt(i) : match
          })
          element.value = newValue
        }

        // определяем положение каретки
        let caretMaxPosition = newValue.indexOf("_")
        if (caretMaxPosition === -1) {
          caretMaxPosition = newValue.length
        }
        if (isCaretInEndPosition) {
          caretPosition = caretMaxPosition
        } else if (caretPosition < caretMinPosition) {
          caretPosition = caretMinPosition
        } else if (caretPosition > caretMaxPosition) {
          caretPosition = caretMaxPosition
        }
        element.setSelectionRange(caretPosition, caretPosition)
      }
    },
    onSubmitCreditForm() {
      const creditform = {
        fullName: this.fullName,
        phone: this.phone,
        email: this.email,
        conditions: this.conditions,
        creditPeriod: this.creditPeriod,
        creditSumm: this.creditSumm,
        monthlyPayment: this.monthlyPayment.toString(),
      }
      console.log(creditform)
      alert("Форма успешно отправлена!")
      this.creditPeriod = "60"
      this.creditSumm = "3000000"
      this.fullName = ""
      this.phone = ""
      this.email = ""
      this.conditions = false
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
.slider-sum-credit__slider-container {
  grid-area: slider;
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

.slider-period-credit__slider-container {
  grid-area: slider;
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
  margin-bottom: 40px;
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

.credit-form__input-border {
  position: absolute;
  left: 0;
  top: 0;
  width: 333px;
  height: 60px;
  padding: 15px;
  background: #eeeeee;
  border-radius: 5px;
  z-index: 1;
}

.credit-form__input {
  z-index: 2;
  position: absolute;
  top: 2px;
  left: 2px;
  width: 329px;
  height: 56px;
  border: none;
  border-radius: 4px;
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
  }

  &:hover ~ .credit-form__input-border {
    background: linear-gradient(91.4deg, #4052a5 0%, #242f62 100%);
  }

  &:focus {
    background: #ffffff;
  }
  &:focus ~ .credit-form__input-border {
    background: linear-gradient(91.4deg, #4052a5 0%, #242f62 100%);
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
  z-index: 2;

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

.credit-form__submit__container {
  &::before {
    margin-bottom: 40px;
    display: block;
    width: 100%;
    height: 1px;
    background-color: #eeeeee;
    content: "";
  }
}

.credit-form__submit {
  display: grid;
  grid-template-columns: repeat(2, 1fr);

  position: relative;
}

.credit-form__submit__conditions {
  justify-self: start;
  display: inline-flex;
  align-items: center;
  user-select: none;
  gap: 10px;
}

.credit-form__submit__checkbox-container {
  position: relative;
  width: 40px;
  height: 40px;
}

.credit-form__submit__checkbox {
  position: absolute;
  position: relative;
  top: 2px;
  left: 2px;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  position: relative;
  width: 36px;
  height: 36px;
  background: #eeeeee;
  border: none;
  border-radius: 9px;
  cursor: pointer;
  z-index: 2;

  &:before {
    content: "";
    position: absolute;
    display: none;
    top: 8px;
    left: 6px;
    // transform: translate(-50%, -50%);
    width: 100%;
    height: 100%;
    pointer-events: none;
    // background-size: contain;
    background-image: url(./../../assets/vector.svg);
    background-repeat: no-repeat;
    z-index: 3;
  }
}

.credit-form__submit__checkbox:checked {
  background: linear-gradient(91.4deg, #4052a5 0%, #242f62 100%);
}

.credit-form__submit__checkbox:checked::before {
  display: block;
}

.credit-form__submit__checkbox:checked ~ .credit-form__submit__border {
  background: linear-gradient(91.4deg, #4052a5 0%, #242f62 100%);
}

.credit-form__submit__checkbox:hover ~ .credit-form__submit__border {
  background: linear-gradient(91.4deg, #4052a5 0%, #242f62 100%);
}

.credit-form__submit__border {
  position: absolute;
  top: 0;
  left: 0;
  width: 40px;
  height: 40px;
  background: #eeeeee;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  z-index: 1;
}

.credit-form__submit__label {
  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 19px;
  cursor: pointer;
}

.credit-form__submit__btn {
  justify-self: end;
  width: 240px;
  height: 50px;
  border-radius: 5px;
  background: linear-gradient(91.4deg, #4052a5 0%, #242f62 100%);

  font-family: Roboto;
  font-style: normal;
  font-weight: bold;
  font-size: 16px;
  line-height: 19px;
  color: #ffffff;
  cursor: pointer;

  &:hover {
    background-blend-mode: overlay, normal;
    background: linear-gradient(
        0deg,
        rgba(255, 255, 255, 0.75),
        rgba(255, 255, 255, 0.75)
      ),
      linear-gradient(91.4deg, #4052a5 0%, #242f62 100%);
  }
}
</style>
