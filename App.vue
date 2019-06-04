<template>
  <div id="app" ref="app" @scroll=handleScroll>
    <!-- 增加一个高度为所有子元素和的元素，撑开父容器 -->
    <div class="invisible" ref="invisible"></div>
    <ul ref="content">
      <li v-for="(item, index) in visibleData" ref="item" :key="index">{{item}}</li>
    </ul>
  </div>
</template>
<script>
export default {
  data () {
    return {
      longArr: null,
      itemHeight: 70,
      visibleData: []
    }
  },
  name: 'home',
  created () {
    this.heightList = []
    // this.longArr = []
    // for (var i = 0; i < 100000; i++) {
    //   this.longArr.push(i)
    // }
    this.longArr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20]
  },
  mounted () {
    this.wrapperHeight = this.$refs.app.clientHeight
    this.visibleData = this.longArr
    this.$nextTick(() => {
      this.getItemHeightList()
      this.updateVisibleData()
      this.changeInvisibleHeight()
    })
  },
  methods: {
    changeInvisibleHeight () {
      // 改变撑开元素的高度
      const height = this.heightList.reduce((prev, cur) => {
        prev = prev + cur.height
        return prev
      }, 0) + 'px'
      this.$refs.invisible.style.height = height
    },
    getItemHeightList () {
      // 获得所有子元素位置信息，并保存在数组中
      const children = this.$refs.content.children
      for (let i = 0; i < children.length; i++) {
        this.heightList.push({
          top: children.item(i).getBoundingClientRect().top,
          height: children.item(i).getBoundingClientRect().height
        })
      }
    },
    updateVisibleData (scrollTop) {
      scrollTop = scrollTop || 0
      const start = this.heightList.findIndex((item) => {
        return scrollTop < item.height + item.top
      })
      let _end = this.heightList.findIndex((item) => {
        return item.top >= scrollTop + this.wrapperHeight
      })
      const end = _end === -1 ? this.heightList.length : _end
      this.visibleData = this.longArr.slice(start, end)
      const transformHeight = this.heightList.reduce((prev, cur, index, arr) => {
        if (index < start && index < arr.length - 1) {
          prev = prev + cur.height
        }
        return prev
      }, 0)
      // 将滚动区域下移，使之在可视区域范围内
      this.$refs.content.style.webkitTransform = `translate3d(0, ${transformHeight}px, 0)`
    },
    handleScroll () {
      const scrollTop = this.$refs.app.scrollTop
      this.updateVisibleData(scrollTop)
    }
  }
}
</script>
<style lang="less">
ul,li{
  padding: 0;
  margin: 0;
  list-style: none;
}
.invisible {
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  z-index: -1;
}
li{
  padding: 20px;
  text-align: center;
  box-sizing: border-box;
  color: black;
  font-size: 50px;
  background: gray;
  border-bottom: 1px solid red;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  overflow: auto;
}
</style>
