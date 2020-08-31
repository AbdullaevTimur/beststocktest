<template>
  <div class="form">
    <div class="form__formula formula">
      <div class="formula__item" v-for="(item, index) in inputs" :key="index">
        <Fractional
          v-model="inputs[index]"
          :removable="index + 1 > items"
          @on-remove="removeItem(index)"/>
        <div class="formula__symbol">{{ index + 1 === inputs.length ? '=' : '+' }}</div>
      </div>
      <div class="formula__result">{{ this.result }}</div>
    </div>
    <div class="form__controls controls">
      <button class="controls__button" @click="appendItem">{{ 'add new element' }}</button>
    </div>
  </div>
</template>

<script>
import Fractional from '@/components/ui/Fractional.vue'

export default {
  name: 'Form',
  components: {
    Fractional
  },
  props: {
    items: {
      type: Number,
      default: 2
    },
    max: {
      type: Number,
      default: 5
    }
  },
  computed: {
    result () {
      // Вычесляем результат: сумму всех указанные знаений
      // Деструктурируем "текущее значние" итеррации на @numerator и @denominator, и определяем для них стандартное значение равное "0"
      return this.inputs.reduce((acc, [numerator = 0, denominator = 0]) => {
        if (numerator && denominator) {
          // Проверяем есть ли у @numerator и @denominator значение, суммируем вычесление к существующему значению функции.
          acc += (numerator / denominator)
        }
        return acc
      }, 0)
    }
  },
  data () {
    return {
      inputs: []
    }
  },
  methods: {
    getSymbol (index) {
      // Определяем как символ выводить
      // ? - Выводим символ "=" после последнего поля ввода
      // : - В противном случае выводим символ "+"
      return index + 1 === this.inputs.length ? '=' : '+'
    },
    appendItem () {
      // Если текущия кол-во полей ввода меньше максимально доступного @this.max, то добавлем новое поле ввода со стандартным значение []
      if (this.inputs.length < this.max) {
        this.inputs.push([])
      }
    },
    removeItem (index) {
      this.inputs.splice(index, 1) // Удаляем поле ввода и знаяение по индексу
    }
  },
  mounted () {
    // Выводим поля ввода, равные количеству указанные в props.items
    for (let i = 0; i < this.items; i++) {
      this.appendItem()
    }
  }
}
</script>

<style lang="scss" scoped>
.controls {
  &__button {
    padding: 6px;
    cursor: pointer;
  }
}

.form {
  &__formula {
    margin: 32px;
  }
}

.formula {
  display: inline-flex;
  align-items: center;

  &__item {
    display: inline-flex;
    align-items: center;
  }

  &__symbol {
    margin: 0 8px;
  }
}
</style>
