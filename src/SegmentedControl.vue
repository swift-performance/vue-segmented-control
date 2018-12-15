<template>
  <div class="segmented-control" :style="[segmentedControlStyle]">
    <div class="segmented-item" :style="[valuesSelected.includes(option[segValue]) ? itemSelectedStyle : null, itemStyle]"
         :class="{'is-selected': valuesSelected.includes(option[segValue])}"
         v-for="option in options" @click="onSelect(option)">
      {{ option[segLabel] }}
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      value: {
        type: null,
        required: false
      },
      options: {
        type: Array,
        required: true
      },
      segLabel: {
        type: String,
        default: 'label'
      },
      segValue: {
        type: String,
        default: 'value'
      },
      color: {
        type: String,
        default: '#fff'
      },
      activeColor: {
        type: String,
        default: '#000'
      },
      multiple: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        optionsSelected: []
      }
    },
    computed: {
      segmentedControlStyle: function () {
        return {
          color: this.activeColor,
          border: `solid 1px ${this.activeColor}`
        }
      },
      itemStyle: function () {
        return {
          borderRight: `solid 1px ${this.activeColor}`
        }
      },
      itemSelectedStyle: function () {
        return {
          color: this.color,
          background: this.activeColor
        }
      },
      valuesSelected: function () {
        return this.optionsSelected.map(option => option[this.segValue])
      }
    },
    methods: {
      update () {
        this.optionsSelected = []
        if (this.multiple === true) {
          if (this.value instanceof Array) {
            for (let val in this.value) {
              this.optionsSelected.push(this.options.find(optionSelected => optionSelected.value === this.value[val]))
            }
          } else {
            this.optionsSelected = this.options.find(optionSelected => optionSelected.value === this.value[val])
          }
        } else {
          this.optionsSelected = [this.options.find(optionSelected => optionSelected.value === this.value)]
        }
      },
      onSelect (option) {
        if (this.multiple === true) {
          if (this.optionsSelected.find(optionSelected => optionSelected.value === option.value)) {
            this.optionsSelected = this.optionsSelected.filter(optionSelected => optionSelected.value !== option.value)
          } else {
            this.optionsSelected.push(option)
          }
        } else {
          this.optionsSelected = [option]
        }

        this.$emit('select', this.optionsSelected)

        let values = this.optionsSelected.reduce((a, b) => {
          a.push(b[this.segValue])
          return a
        }, [])
        if (this.multiple === true) {
          this.$emit('input', values)
        } else {
          this.$emit('input', values[0])
        }
      }
    }
  }
</script>

<style>
  .segmented-control {
    display: flex;
    flex-direction: row;
    border-radius: 5px;
  }

  .segmented-item {
    flex: 1;
    padding: 10px;
    transition: all .3s ease;
    text-align: center;
    user-select: none;
  }

  .segmented-item:last-child {
    border: none!important;
  }

  .is-selected {
    color: white;
  }
</style>