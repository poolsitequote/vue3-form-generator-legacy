<template>
  <div class="customSelect">
    <div class="btn-group-select">
      <li class="dropdown-toggle-select" @click="toggleMenu()">
        {{ getVuleItem(value) }}
        <span class="caret" />
      </li>

      <ul v-if="showMenu" class="dropdown-select">
        <li v-for="item in items" :key="getItemValue(item)">
          <a href="javascript:void(0)" @click="updateOption(item)">
            {{ getItemName(item) }}
          </a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { isObject, isNil, find } from 'lodash'
import abstractField from '../abstractField.js'

export default {
  name: 'FieldSelect',
  mixins: [ abstractField ],
  props: {
    options: {
      type: [ Array, Object ]
    },
    selected: {}
  },
  data() {
    return {
      selectedOption: {
        name: 'Please select an item'
      },
      showMenu: false
    }
  },
  computed: {
    selectOptions() {
      return this.schema.selectOptions || {}
    },

    items() {
      let values = this.schema.values
      if (typeof values == 'function') {
        return this.groupValues(values.apply(this, [ this.model, this.schema ]))
      } else return this.groupValues(values)
    }
  },
  methods: {
    text_truncate(str, length, ending) {
      if (length == null) {
        length = 100
      }
      if (ending == null) {
        ending = '...'
      }
      if (str.length > length) {
        return str.substring(0, length - ending.length) + ending
      } else {
        return str
      }
    },
    updateOption(option) {
      this.value = this.getItemValue(option)
      this.selectedOption = option
      this.showMenu = false
      this.$emit('updateOption', this.selectedOption)
    },
    toggleMenu() {
      this.showMenu = !this.showMenu
    },
    formatValueToField(value) {
      if (isNil(value)) {
        return null
      }
      return value
    },
    groupValues(values) {
      let array = []
      let arrayElement = {}
      array.push({id:null,name:'Not selected'})
      values.forEach(item => {
        arrayElement = null

        if (item.group && isObject(item)) {
          // There is in a group.

          // Find element with this group.
          arrayElement = find(array, i => i.group === item.group)

          if (arrayElement) {
            // There is such a group.
            arrayElement.ops.push({
              id: item.id,
              name: item.name
            })
          } else {
            // There is not such a group.

            // Initialising.
            arrayElement = {
              group: '',
              ops: []
            }
            // Set group.
            arrayElement.group = item.group

            // Set Group element.
            arrayElement.ops.push({
              id: item.id,
              name: item.name
            })

            // Add array.
            array.push(arrayElement)
          }
        } else {
          // There is not in a group.
          array.push(item)
        }
      })
      // With Groups.
      return array
    },

    getGroupName(item) {
      if (item && item.group) {
        return item.group
      }

      throw 'Group name is missing! https://icebob.gitbooks.io/vueformgenerator/content/fields/select.html#select-field-with-object-items'
    },

    getVuleItem(value) {

      for(let key in this.items){
        if(this.items[key].id === value)
          return this.items[key].name
        if(this.items[key] === value)
          return this.items[key]
      }
      return  'Please select an item'
    },

    getItemValue(item) {
      if (isObject(item)) {
        if (typeof this.schema['selectOptions'] !== 'undefined' && typeof this.schema['selectOptions']['value'] !== 'undefined') {
          return item[this.schema.selectOptions.value]
        } else {
          // Use 'id' instead of 'value' cause of backward compatibility
          if (typeof item['id'] !== 'undefined') {
            return item.id
          } else {
            throw '`id` is not defined. If you want to use another key name, add a `value` property under `selectOptions` in the schema. https://icebob.gitbooks.io/vueformgenerator/content/fields/select.html#select-field-with-object-items'
          }
        }
      } else {
        return item
      }
    },

    getItemName(item) {
      if (isObject(item)) {
        if (typeof this.schema['selectOptions'] !== 'undefined' && typeof this.schema['selectOptions']['name'] !== 'undefined') {
          return item[this.schema.selectOptions.name]
        } else {
          if (typeof item['name'] !== 'undefined') {
            return item.name
          } else {
            throw '`name` is not defined. If you want to use another key name, add a `name` property under `selectOptions` in the schema. https://icebob.gitbooks.io/vueformgenerator/content/fields/select.html#select-field-with-object-items'
          }
        }
      } else {
        return item
      }
    }
  }
}
</script>

<style lang="scss">
.customSelect{
  width: 100%;
  .btn-group-select {
    display: block;
    padding: 7px 17px 7px 10px;
    font-size: 13px;
    line-height: 1.42857143;
    color: #555;
    background-color: #fff;
    background-image: none;
    border: 1px solid #ccc;
    border-radius: 4px;
    -webkit-box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
    box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
    -webkit-transition: border-color .15s ease-in-out,-webkit-box-shadow .15s ease-in-out;
    transition: border-color .15s ease-in-out,-webkit-box-shadow .15s ease-in-out;
    transition: border-color .15s ease-in-out,box-shadow .15s ease-in-out;
    transition: border-color .15s ease-in-out,box-shadow .15s ease-in-out,-webkit-box-shadow .15s ease-in-out;
  }
  .btn-group-select a:hover {
    text-decoration: none;
  }
  .dropdown-toggle-select:hover {
    background: #e1e1e1;
    cursor: pointer;
  }
  .dropdown-select {
    position: absolute;
    top: 100%;
    left: 0;
    z-index: 1000;
    float: left;
    min-width: 160px;
    padding: 5px 0;
    margin: 2px 0 0;
    list-style: none;
    font-size: 14px;
    text-align: left;
    background-color: #fff;
    border: 1px solid #ccc;
    border-radius: 4px;
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.175);
    background-clip: padding-box;
  }
  .dropdown-select > li > a {
    padding: 10px 30px;
    display: block;
    clear: both;
    font-weight: 400;
    color: #333;
    font-size: 0.8rem;
    width: 500px;
  }
  .dropdown-select > li > a:hover {
    background: #efefef;
    color: #409FCB;
  }
  .dropdown-select > li {
    overflow: hidden;
    width: 100%;
    position: relative;
    margin: 0;
  }
  .caret {
    display: block;
    width: 0;
    top: 48px;
    border-top: 4px dashed;
    border-top: 4px solid\9;
    border-right: 4px solid transparent;
    border-left: 4px solid transparent;
    float: right;
  }
  li {
    list-style: none;
  }
}
</style>