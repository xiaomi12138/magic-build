<template>
  <div class="wrap">
    <ul class="m-components-list" @dragstart="dragStart" @dragend="dragEnd">
      <li draggable="true" data-name="topHeader">
          <top-header />
      </li>
      <li draggable="true" data-name="banner">
          <banner></banner>
      </li>
      <li draggable="true" data-name="list">
          <list></list>
      </li>
      <li draggable="true" data-name="hotpicture">
          <hotpicture></hotpicture>
      </li>
      <li draggable="true" data-name="componentHolder">
          <component-Holder></component-Holder>
      </li>
    </ul>
  </div>
</template>

<script>
import modules from "./modules"
import { mapActions } from 'vuex'
import { guid } from '@utils'
const $window = window;
const componentHolderName = "componentHolder"
export default {
  data() {
    return {
      componentHolderName: componentHolderName
    };
  },
  methods: {
    ...mapActions([
      'delComponentHolder'
    ]),
    dragStart(e) {
      let el = e.target
      // 设置添加flag
      e.dataTransfer.setData('addFlag', true)
      let componentName = el.getAttribute("data-name");
      //获取组件默认数据
      let compData = require('./modules/' + componentName + '/' + componentName + '.json')
      let id = guid()
      let info = {
        name: componentName,
        data: compData,
        id: id,
        showToolBar: false
      };
      e.dataTransfer.effectAllowed = "copy";
      e.dataTransfer.setData("info", JSON.stringify(info));
      // 设置拖拽过程中元素样式
      console.log('el', el)
      let target = this.scaleEle(el);
      e.dataTransfer.setDragImage(target, 0, 10);
    },
    scaleEle(target) {
      // 缩放拖拽过程元素
      let width = parseInt(getComputedStyle(target).getPropertyValue("width"));
      let height = parseInt(
        $window.getComputedStyle(target).getPropertyValue("height")
      );
      let $app = document.querySelector("#app");
      let node = target.cloneNode(true);
      node.id = '_temp'
      Object.assign(node.style,{
        listStyle: "none",
        opacity: "1",
        position: "fixed",
        width: width * 0.448 + "px",
        height: height * 0.448 + "px",
        top: "-200000px",
      })
      $app.appendChild(node);
      return node;
    },
    dragEnd(e) {
      /*拖拽结束*/
      // console.log('drag end1')
      document.querySelector('#app').removeChild(document.querySelector('#_temp'))
      e.dataTransfer.clearData("info");
      // 清除flag, 避免影响移动组件
      e.dataTransfer.clearData('addFlag')
      this.delComponentHolder(this.componentHolderName)
      return false;
    }
  },
  components: {
    ...modules
  }
};
</script>

<style lang="less" scoped>
.wrap {
  width: 380px;
  padding: 30px;
  box-sizing: border-box;
  background-color: #ede7f6;
}
</style>

<style lang="less">
.m-components-list {
  margin: 0;
  padding: 0;
  width: 750px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  list-style: none;
  transform: scale(0.43, 0.43) translate(-498px, -36px);
  > li {
    min-height: 36px;
    font-size: 18px;
    -webkit-user-select: none;
    margin-bottom: 10px;
    transition: transform 0.2s;
    cursor: move !important;
    &:hover {
      transform: scale(1.1) translate(0);
    }
    i {
      vertical-align: middle;
    }
  }
}
</style>
