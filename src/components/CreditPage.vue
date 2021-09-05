<template>
  <div class="CreditPage">
    <div class="left-column">
      <img alt="Logo" src="../assets/logo.svg" />
    </div>
    <div class="content">
      <div class="header">
        <h1 class="header__title">Кредит под залог недвижимости</h1>
        <p class="header__text">
          Получите решение по паспорту, без справок о доходах и, главное, вы
          остаетесь собственником!
        </p>
      </div>
      <div class="features">
        <div class="features__row">
          <div class="item-features">
            <h3 class="item-features__title">Минимум документов</h3>
            <p class="item-features__text">
              Нужен паспорт РФ и документы, подтверждающие право собственности
              на залог
            </p>
          </div>
          <div class="item-features">
            <h3 class="item-features__title">Вы остаетесь собственником</h3>
            <p class="item-features__text">
              Не нужно выписываться или переоформлять недвижимость на банк
            </p>
          </div>
        </div>
        <div class="features__row">
          <div class="item-features">
            <h3 class="item-features__title">Большая сумма</h3>
            <p class="item-features__text">
              Вы получите до 8 000 000 рублей под ставку от 8,8% годовых
            </p>
          </div>
          <div class="item-features">
            <h3 class="item-features__title">Комфортный платеж</h3>
            <p class="item-features__text">
              Вы можете взять кредит на длительный срок: до 20 лет
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue"

export default defineComponent({
  name: "CreditPage",
  components: {},
})
</script>

<style lang="scss">
.CreditPage {
  display: grid;
  grid-template-columns: 240px 1fr;
}

.left-column {
  text-align: center;
}

.header__title {
  padding-bottom: 40px;

  font-family: PT Sans Caption;
  font-style: normal;
  font-weight: bold;
  font-size: 36px;
  line-height: 47px;

  color: #000000;
}

.header__text {
  padding-bottom: 81px;

  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 24px;

  color: #000000;
}

.features__row {
  padding-bottom: 40px;

  display: grid;
  grid-template-columns: repeat(2, 520px);
  gap: 40px;
}

.item-features__title {
  padding-bottom: 20px;

  font-family: PT Sans Caption;
  font-style: normal;
  font-weight: bold;
  font-size: 28px;
  line-height: 36px;

  color: #000000;
}

.item-features__text {
  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 24px;

  color: #000000;
}
</style>
