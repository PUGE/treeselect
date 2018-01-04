<template>
  <div class="tree-select-box">
    <!-- 加载动画 -->
    <svg v-if="loading === 'loading'" class="loadsvg" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid" style="background: none;">
      <circle cx="50" cy="50" r="31.3416" fill="none" stroke="#8cd0e5" stroke-width="2">
        <animate attributeName="r" calcMode="spline" values="0;40" keyTimes="0;1" dur="1" keySplines="0 0.2 0.8 1" begin="-0.5s" repeatCount="indefinite"></animate>
        <animate attributeName="opacity" calcMode="spline" values="1;0" keyTimes="0;1" dur="1" keySplines="0.2 0 0.8 1" begin="-0.5s" repeatCount="indefinite"></animate>
      </circle>
      <circle cx="50" cy="50" r="10.8383" fill="none" stroke="#376888" stroke-width="2">
        <animate attributeName="r" calcMode="spline" values="0;40" keyTimes="0;1" dur="1" keySplines="0 0.2 0.8 1" begin="0s" repeatCount="indefinite"></animate>
        <animate attributeName="opacity" calcMode="spline" values="1;0" keyTimes="0;1" dur="1" keySplines="0.2 0 0.8 1" begin="0s" repeatCount="indefinite"></animate>
      </circle>
    </svg>
    <!-- 错误面板 -->
    <div v-else-if="loading === 'error'" class="error">
      <svg t="1515032208380" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4561" xmlns:xlink="http://www.w3.org/1999/xlink" width="100" height="100"><path d="M90.217744 7.086395 7.150864 76.526931c148.075477 109.824291 286.129716 236.068554 407.130557 362.632087C228.579999 625.49489 83.146699 808.742425 6.515391 882.403075l158.246117 131.868353c56.121182-115.388007 178.382737-291.057959 341.933673-474.684116 163.741271 184.757934 286.259676 360.993774 342.815763 477.325269 0 0 154.206104-163.164127 167.972643-137.917116-59.526745-66.656119-204.136284-260.442684-399.125233-458.95897 111.663172-114.254184 236.962923-227.308029 370.776581-326.663898L952.72263 25.640983C800.911062 100.82022 656.739499 212.363665 527.017018 331.09072 396.105457 207.815069 247.958348 89.774653 90.217744 7.086395L90.217744 7.086395zM90.217744 7.086395" p-id="4562"></path></svg>
    </div>
    <div class="tree-select-item" v-else-if="loading === 'finish'" v-for="(item, ind) in dataCopy" :key="ind">
      <div class="tree-select-item-bar label">
        <!-- 下拉图标 -->
        <div class="drop-down" @click.stop="clickBar(ind)" :class="{open: show[ind]}">
          <svg t="1514861854021" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2263" xmlns:xlink="http://www.w3.org/1999/xlink" width="16" height="16"><path d="M847.938 514.555l-669.322 392.953 192.683-398.022-195.238-392.993z" p-id="2264"></path></svg>
        </div>
        <input v-if="!radio" name="treeSelect" type="checkbox" :value="item.label" v-model="item.checked" @change.stop="checkBoxClick($event.target, item)"/>
        <p @click.stop="clickBar(ind)">{{item.label}}</p>
        <!-- 判断是否有子项目，如果有显示角标 -->
        <div v-if="item.children" class="childrenNumber">({{item.children.length}})</div>
      </div>
      <div class="children-box" v-if="item.children" :style="getHeight(ind, item.children.length)">
        <div class="children-item label" v-for="(children, index) in item.children" :key="index">
          <input name="treeSelect" type="checkbox" :value="children.value || children.label" v-model="children.checked" @change.stop="checkBoxClick($event.target, children, item)" />
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
        default: () => null
      },
      radio: {
        type: Boolean,
        default: () => false
      },
      dataUrl: String
    },
    data () {
      return {
        dataCopy: this.data,
        loading: 'loading',
        show: {}
      }
    },
    methods: {
      checkBoxClick (event, target, parent) {
        const sendData = {
          checked: event.checked,
          target,
          checkedList: this.dataCopy,
          parent
        }
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
      },
      loadData (url) {
        return new Promise((resolve, reject) => {
          const obj = new XMLHttpRequest()
          obj.open('GET', url, true)
          obj.onreadystatechange = () => {
            if (obj.readyState !== 4) return
            if (obj.status === 304 || obj.status === 200) {
              const responseText = obj.responseText
              resolve(JSON.parse(responseText))
            } else {
              this.$emit('loadError')
              this.loading = 'error'
            }
          }
          obj.send(null)
        })
      }
    },
    mounted () {
      const dataUrl = this.dataUrl
      // 如果没有data，但是有dataUrl,那么请求Url获取数据
      if (!this.dataCopy && dataUrl) {
        this.loadData(dataUrl).then((response) => {
          this.$emit('loadFinish')
          this.dataCopy = response.data
          this.loading = 'finish'
        })
      }
    }
  }
</script>

<style scoped>
  .loadsvg {
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    margin: auto;
  }
  .drop-down {
    padding: 7px;
    display: flex;
    transition: transform 0.2s;
  }
  .drop-down svg {
    fill: #ccc;
  }
  .tree-select-item-bar .open {
    transform:rotate(90deg)
  }
  .tree-select-box {
    position: relative;
    width: calc(100% - 10px);
    height: calc(100% - 20px);
    overflow-x: hidden;
    overflow-y: auto;
    background: #EEE;
    padding: 10px 5px;
  }
  .children-box {
    transition: height 0.2s;
    height: 0;
    overflow: hidden;
    margin: 0 10px;
    padding-left: 10px;
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
  p {
    margin: 0;
    padding: 0;
  }
  .error {
    height: 200px;
    width: 200px;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    margin: auto;
    text-align: center;
    line-height: 200px;
  }
</style>