<template>
  <div id="stock_list">

    <div id="top_panel">
      <h1>Добавление товара</h1>
      <select>
        <option>По умолчанию</option>
        <option>По цене min</option>
        <option>По цене max</option>
        <option>По наименованию</option>
      </select>
    </div>

    <div id="left_panel">
      <div class="label">Наименование товара<div class="dot">•</div></div>
      <input v-model="item_form.name" placeholder="Введите наименование товара"/>
      <div class="label">Описание товара</div>
      <textarea v-model="item_form.description" placeholder="Введите описание товара"/>
      <div class="label">Ссылка на изображение товара<div class="dot">•</div></div>
      <input v-model="item_form.image" placeholder="Введите ссылку"/>
      <div class="label">Цена товара<div class="dot">•</div></div>
      <input v-model="item_form.price" placeholder="Введите цену"/>

      <button :disabled="!submit" @click="addItem">Добавить товар</button>
    </div>

    <div id="items">
      <div class="item" v-for="(item, index) in items" :key="index">
        <button class="item-remove" @click="removeItem(index)">
          <font-awesome-icon :icon="['fas', 'trash']" />
        </button>
        <img class="item-image" :src="item.image"/>
        <div class="item-block">
          <p class="item-block-name">{{ item.name }}</p>
          <p class="item-block-description">{{ item.description }}</p>
          <p class="item-block-price">{{ correctPrice(item.price) }}</p>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  data: () => ({
    components: { },
    items: [],
    item_form: { name: null, image: null, description: null, price: null }
  }),
  created() {
    this.getSomeItems()
  },
  computed: {
    submit: function () { return this.item_form.name && this.item_form.image && this.item_form.price }
  },
  methods: {
    getSomeItems() {
      for (let i = 0; i < 9; i++) {
        this.items[i] = {
          name: "Наименование товара",
          image: "https://i.pcmag.com/imagery/roundups/018cwxjHcVMwiaDIpTnZJ8H-22..1570842461.jpg",
          description: `Довольно таки интересное описание товара в несколько строк.
                          Довольно таки интересное описание товара в несколько строк.`,
          price: "10000"
        }
      }
    },
    addItem() {
      this.items.unshift(this.item_form)
      this.item_form = {}
    },
    removeItem(id) {
      this.items.splice(id, 1)
    },
    correctPrice(price) { return parseFloat(price).toLocaleString("ru")  + " руб." }
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
  flex: 1 1 31%
  margin: 1%
  box-shadow: rgba(0, 0, 0, 0.2) 0 5px 15px
  border-radius: $corner
  display: flex
  flex-direction: column
  &:hover
    cursor: pointer
    & > .item-remove
      cursor: pointer
      display: block
  &-image
    width: 100%
    border-top-right-radius: $corner
    border-top-left-radius: $corner
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
    padding: 5%
    &-name
      font-weight: bold
    &-price
      font-weight: bold

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
