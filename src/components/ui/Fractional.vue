<template>
  <div class="element">
    <input
      type="number"
      class="element__input"
      v-model.number="output[0]"
      :min="min"
      :max="max"
      :step="step"/>
    <span class="element__spliter"/>
    <input
      type="number"
      class="element__input"
      v-model.number="output[1]"
      :min="min"
      :max="max"
      :step="step"/>
    <div class="element__toolbar toolbar" v-if="removable">
      <div class="toolbar__item toolbar__item--delete" @click="$emit('on-remove')"/>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Fractional',
  props: {
    value: Array,
    default: {
      type: Array,
      default: () => {
        return ['', '']
      }
    },
    min: {
      type: Number,
      default: 1
    },
    max: {
      type: Number,
      default: 99
    },
    step: {
      type: Number,
      default: 1
    },
    removable: {
      type: Boolean,
      default: false
    }
  },
  watch: {
    value: {
      deep: true,
      handler (val) {
        // Осуществлем проверяем значение аргумента на валидность
        // ? - записываем значение аргумента
        // : - записываем стандартное значение
        this.output = this.validate ? val : this.default
      }
    },
    output: {
      deep: true,
      handler (val) {
        val.forEach((x, i) => {
          // Проверяем существет ли значение и не равно ли оно нулю
          // Избегаем ошибки вычисления при возможном значение "0"
          // В случае если условие удаолитворенно, назначаем минимальное значение
          if (!x) val[i] = this.min
        })
        this.$emit('input', val) // Публикуем значение на родителя.
      }
    }
  },
  data () {
    return {
      output: this.$props.default
    }
  },
  methods: {
    validate (val) {
      // 1. Проверяем является ли значение аргумента массивом
      // 2. Проверяем является ли каждое значени массива числом и не равняется ли нулю
      return val.constructor === Array && val.every(x => typeof x === 'number' && x !== 0)
    }
  }
}
</script>

<style lang="scss" scoped>
.element {
  display: inline-block;
  position: relative;

  &__input {
    display: block;
    width: 32px;
    border: none;
    outline: none;
  }

  &__spliter {
    display: block;
    position: relative;
    width: 100%;
    border: 1px solid black;
    margin: 8px 0;
  }

  &__toolbar {
    position: absolute;
    bottom: calc(100% + 4px);
    width: 100%;
    text-align: center;

    & > *:not(:last-child) {
      margin-right: 4px;
    }
  }
}

.toolbar {
  &__item {
    display: inline-flex;
    width: 16px;
    height: 16px;
    border-radius: 50%;
    justify-content: center;
    align-items: center;
    background-color: darken(white, 20);
    color: lighten(black, 50);
    cursor: pointer;

    &:hover {
      background-color: lighten(white, 15);
    }

    &--delete {
      background-color: red;
      color: white;

      &:hover {
        background-color: lighten(red, 15);
      }

      &:before {
        content: '⨯';
        position: relative;
        top: -1px;
      }
    }
  }
}
</style>
