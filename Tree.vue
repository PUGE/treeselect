<template>
  <div class="tree-select-box">
    <div class="tree-select-item" v-for="(item, ind) in data" :key="ind">
      <div class="tree-select-item-bar label">
        <input name="treeSelect" type="checkbox" :value="item.label" v-model="checkedList[0]" @change.stop="checkBoxClick($event.target, item)"/>
        <p @click.stop="clickBar(ind)">{{item.label}}</p>
        <!-- 判断是否有子项目，如果有显示角标 -->
        <div v-if="item.children" class="childrenNumber">({{item.children.length}})</div>
      </div>
      <div class="children-box" v-if="item.children" :style="getHeight(ind, item.children.length)">
        <div class="children-item label" v-for="(children, index) in item.children" :key="index">
          <input name="treeSelect" type="checkbox" :value="children.value || children.label" v-model="checkedList[1]" @change.stop="checkBoxClick($event.target, children, item)" />
          {{children.label}}
        </div>
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
        ],
        show: {}
      }
    },
    methods: {
      checkBoxClick (event, target, parent) {
        console.log(event)
        const sendData = {
          checked: event.checked,
          target,
          checkedList: this.checkedList,
          parent
        }
        console.log(sendData)
        this.$emit('onCheck', sendData)
      },
      clickBar (ind) {
        if (this.show[ind]) this.show[ind] = this.$set(this.show, ind, false)
        else this.show[ind] = this.$set(this.show, ind, true)
      },
      getHeight (ind, length) {
        if (this.show[ind]) {
          return { height: length * 30 + 'px' }
        }
      }
    }
  }
</script>

<style scoped>
  .tree-select-box {
    width: calc(100% - 20px);
    height: calc(100% - 10px);
    overflow-x: hidden;
    overflow-y: auto;
    background: #EEE;
    padding: 10px 5px;
  }
  .children-box {
    transition: height 0.2s;
    height: 0;
    overflow: hidden;
  }
  .children-box .label {
    padding-left: 20px;
  }
  .show {
    height: auto;
  }
  .label {
    height: 30px;
    line-height: 30px;
    font-size: 0.8rem;
    display: flex;
    cursor: pointer;
    color: #008cba;
  }
  .label:hover {
    background-color: #008cba;
    color: white;
  }
  .label input {
    height: 16px;
    width: 16px;
    margin: 7px;
  }
  .label p {
    width: calc(100% - 65px);
    font-size: 0.9rem;
  }
  .tree-select-item-bar {
    border-bottom: 1px solid #ccc;
    margin: 0 10px;
    padding: 2px 0;
    color: #008cba;
    font-weight: bold;
  }
</style>