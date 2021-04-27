<template>
  <div class="container">
    <div>
      <h4>{{ title }}</h4>
    </div>
    <div
      class="item"
      v-for="{name} in data"
      :key="name"
      v-bind:style="checked(name) ? colorChecked :{}"
      v-bind:onclick="() => onClickItem(name)"
    >
      {{name}}</div>
  </div>
</template>

<script>
export default {
  name: "List",
  props: {
    title: {
      type: String,
      default: ""
    },
    data: {
      type: Array,
      require: true
    },
    onClickItem: {
      type: Function,
      default: null
    },
    checked: {
      type: Function,
      default: () => false
    },
    colorChecked: {
      type: Object,
      default: () => ({})
    }
  }
}
</script>

<style scoped>
  .container {
    border: 1px solid gray;
  }
  .item {
    border: 1px solid gray;
    cursor: pointer;
    padding: 10px;
  }
</style>
