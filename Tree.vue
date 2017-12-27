<template>
  <div class="tree-select-box">
    <div class="tree-select-item" v-for="(item, ind) in data" :key="ind">
      <label class="tree-select-item-bar">
        <input name="treeSelect" type="checkbox" :value="item.label" v-model="checkedList[0]" @change="checkBoxClick($event.target, item)"/>
        {{item.label}}
      </label>
      <div class="children-box" v-if="item.children">
        <label class="children-item" v-for="(children, index) in item.children" :key="index">
          <input name="treeSelect" type="checkbox" value="" v-model="checkedList[1]" @change="checkBoxClick($event, item)" />
          {{children.label}}
        </label>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      data: {
        type: Array,
        default: () => []
      }
    },
    data () {
      return {
        checkedList: [
          [],
          []
        ]
      }
    },
    methods: {
      checkBoxClick (event, target) {
        const sendData = {
          checked: event.checked,
          target,
          checkedList: this.checkedList
        }
        console.log(sendData)
        this.$emit('onCheck', sendData)
      }
    }
  }
</script>

<style scoped>
  .tree-select-box {
    width: 100%;
    height: 100%;
    background-color: white;
  }
  .children-box {
    margin-left: 20px;
  }
  label {
    height: 25px;
    line-height: 25px;
    display: block;
    font-size: 0.8rem;
    display: flex;
  }
  label input {
    height: 15px;
    width: 15px;
    margin: 5px;
  }
  .tree-select-item-bar {
    border-bottom: 1px solid #ccc;
    padding: 0 5px;
    background-color: gainsboro;
    color: #333;
  }
</style>