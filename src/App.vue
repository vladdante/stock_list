<template>
  <div id="stock_list">

    <div id="top_panel">
      <h1>Добавление товара</h1>
      <select>
        <option @click="sortParam='default'">По умолчанию</option>
        <option @click="sortParam='min'">По цене min</option>
        <option @click="sortParam='max'">По цене max</option>
        <option @click="sortParam='name'">По наименованию</option>
      </select>
    </div>

    <div id="left_panel">
      <div class="label">Наименование товара<div class="dot">•</div></div>
      <input v-model="v$.item_form.name.$model"
             @blur="v$.item_form.name.$touch"
             :class="{ 'error-frame': v$.item_form.name.$error }"
             placeholder="Введите наименование товара"
      />
      <div v-if="v$.item_form.name.$error" class="error">{{ error_message }}</div>

      <div class="label">Описание товара</div>
      <textarea v-model="item_form.description" placeholder="Введите описание товара"/>

      <div class="label">Ссылка на изображение товара<div class="dot">•</div></div>
      <input v-model="v$.item_form.image.$model"
             @blur="v$.item_form.image.$touch"
             :class="{ 'error-frame': v$.item_form.image.$error }"
             placeholder="Введите ссылку"
      />
      <div v-if="v$.item_form.image.$error" class="error">{{ error_message }} Введите ссылку, начинающуюся с "http(s)://"</div>

      <div class="label">Цена товара<div class="dot">•</div></div>
      <input v-model="v$.item_form.price.$model"
             @blur="v$.item_form.price.$touch"
             :class="{ 'error-frame': v$.item_form.price.$error }"
             placeholder="Введите цену"
      />
      <div v-if="v$.item_form.price.$error" class="error">{{ error_message }} Веедите только цифры.</div>

      <button :disabled="!submit" @click="addItem">Добавить товар</button>
    </div>

    <transition-group tag="div"
                      id="items"
                      name="list"
    >
      <div class="item" v-for="(item, index) in sortItems" :key="index">
        <button class="item-remove" @click="removeItem(index)">
          <font-awesome-icon :icon="['fas', 'trash']" />
        </button>
        <img class="item-image" :src="item.image"/>
        <div class="item-block">
          <p class="item-block-name">{{ item.name }}</p>
          <p class="item-block-description">{{ correctDescription(item.description) }}</p>
          <p class="item-block-price">{{ correctPrice(item.price) }}</p>
        </div>
      </div>
    </transition-group>

  </div>
</template>

<script>
import useVuelidate from "@vuelidate/core"
import { required, url, numeric } from "@vuelidate/validators"
export default {
  setup: () => ({ v$: useVuelidate() }),
  data: () => ({
    items: [],
    item_form: { name: null, image: null, description: null, price: null },
    sortParam: "",
    error_message: "Поле является обязательным. "
  }),
  validations: () => ({
    item_form: {
      name: { required },
      image: { required, url },
      price: { required, numeric }
    }
  }),
  created() { this.getSomeItems() },
  computed: {
    submit() {
      return !this.v$.item_form.name.$invalid && !this.v$.item_form.image.$invalid && !this.v$.item_form.price.$invalid
    },
    sortItems() {
      switch (this.sortParam) {
        // case "default": return this.getSomeItems()
        case "min": return this.sortMin()
        case "max": return this.sortMax()
        case "name": return this.sortByName()
        default: return this.items
      }
    }
  },
  methods: {
    getSomeItems() { this.items = require("../data.json") },
    addItem() {
      this.items.unshift(this.item_form)
      this.item_form = {}
    },
    removeItem(id) {
      this.items.splice(id,1)
    },
    correctPrice(price) { return parseFloat(price).toLocaleString("ru")  + " руб." },
    correctDescription(description) {
      if (description) {
        let max_length = 200
        return description.slice(0, max_length) + (description.length > max_length ? " ..." : "")
    }},
    sortMax() { return this.items.sort((a, b) => { return (Number(a.price) < Number(b.price)) ? 1 : -1 }) },
    sortMin() { return this.items.sort((a, b) => { return (Number(a.price) > Number(b.price)) ? 1 : -1 }) },
    sortByName() { return this.items.sort((a, b) => { return (a.name.toLowerCase() > b.name.toLowerCase()) ? 1 : -1 }) }
  }
}
</script>

<style lang="sass">
$corner: 7px

body
  font: 1.2em "Ubuntu", sans-serif

#stock_list
  display: grid
  grid-template-columns: 25% 75%
  grid-auto-rows: 20% 80%
  grid-template-areas: "head head" "sidebar main"

#top_panel
  grid-area: head
  padding: 5px 20px
  display: flex
  flex-flow: row
  justify-content: space-between
  align-items: center
  select
    background-color: white
    border: none
    border-radius: $corner
    box-shadow: rgba(99, 99, 99, 0.2) 0 2px 8px 0
    padding: 10px
    height: fit-content
    font-size: 0.8em
#left_panel
  grid-area: sidebar
  display: flex
  flex-flow: column
  margin: 3%
  padding: 3%
  height: fit-content
  box-shadow: rgba(0, 0, 0, 0.2) 0 5px 15px
  border-radius: $corner
  position: sticky
  position: -webkit-sticky
  top: 3%
  .label
    margin-top: 10px
    font-size: 0.8em
    display: flex
    .dot
      color: red
      line-height: 5px
      padding: 2px
  input, textarea
    border-radius: $corner
    border: none
    font-size: 0.8em
    padding: 10px
    margin: 10px 0
    box-shadow: rgba(99, 99, 99, 0.2) 0 2px 8px 0
    &::placeholder
      color: #606060
    &:focus-visible
      -webkit-transition: 0.5s
      transition: 0.5s
  textarea
    height: 120px
    resize: none
    font-size: 1em
  button
    border: none
    border-radius: $corner
    height: 40px
    margin: 10px 0
    font-weight: bold
    color: white
    font-size: 0.8em
    background-color: #5dd75d
    &:disabled
      background-color: #eaeaea
      color: #b7b7b7

#items
  grid-area: main
  display: flex
  flex-wrap: wrap

.item
  border-radius: $corner
  box-shadow: rgba(0, 0, 0, 0.2) 0 5px 15px
  display: flex
  flex: 1 1 31%
  flex-direction: column
  margin: 1%
  transition: all 1s
  &:hover
    cursor: pointer
    & > .item-remove
      display: block
      cursor: pointer
  &-image
    border-top-left-radius: $corner
    border-top-right-radius: $corner
    height: 280px
    width: 100%
  &-remove
    position: absolute
    align-self: flex-end
    background-color: #ea8e8e
    border: none
    font-size: 1.5em
    padding: 10px 15px
    border-radius: 18px
    color: white
    margin: -15px
    display: none
    transition: 0.5s
    -webkit-transition: 0.5s
  &-block
    display: flex
    flex-direction: column
    height: 100%
    justify-content: space-between
    padding: 5%
    &-name
      font-weight: bold
    &-price
      font-weight: bold

.error
  color: red
  font-size: 0.6em
  &-frame
    border: solid 1px red !important

.list-enter, .list-leave-to
  opacity: 0
  transform: translateY(1px)

.list-leave-active
  position: absolute

@media only screen and (max-width: 768px)
  #stock_list
    display: block
  #top_panel
    display: block
  #left_panel
    margin: 40px 20px
    position: relative
  #items
    display: block
  .item
    margin: 20px
</style>
