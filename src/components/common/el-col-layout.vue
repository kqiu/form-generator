<template>
  <div :style="{'width': width, 'height': height, 'left': left, 'top': top, 'display': 'inline-block', 'vertical-align': 'top'}" 
    :class="{'el-col-layout': true, 'el-col-absolute-layout': absoluteOn}"
    @mousedown="onMouseDown"
  >
    <slot>
    </slot>
  </div>
</template>
<script>
let globalZIndex = 1;
let canDrag = false;
let selectedDom = null;

import { EventBus } from "@/views/index/event-bus.js";

export default {
  components: {},
  props: ['span', 'type', 'size', 'layout'],
  data() {
    return {
      width: '',
      height: 'auto',
      left: '',
      top: '',
      absoluteOn: false,
      switch: false,
      lastX: 0,
      lastY: 0
    }
  },
  computed: {
  },
  watch: {
    layout(newName, oldName) {
      this.updateLayout();
    },
    type(newName, oldName) {
      this.updateSize();
    },
    span(newName, oldName) {
      this.updateSize();
    },
    size(newName, oldName) {
      this.updateSize();
      this.updateLayout();
    }
  },
  created() {
  },
  updated() {
    // console.log("updated>>>>>>>>", this.width, this.span)
  },
  mounted() {
    this.updateSize();
    this.updateLayout();

    document.addEventListener("keydown", this.handleKeyDown, false);
    document.addEventListener("keyup", this.handleKeyUp, false);
  },
  beforeDestroy() {
        document.removeEventListener("keydown", this.handleKeyDown);
        document.removeEventListener("keyup", this.handleKeyUp);
  },
  methods: {
    handleKeyDown(e) {
      if (e.ctrlKey) {
          e.preventDefault();
          this.canDrag = true;
          EventBus.$emit("before-draggable");
      }
    },
    handleKeyUp(e) {
      e.preventDefault();
      this.canDrag = false;
      EventBus.$emit("after-draggable");
    },
    onMouseDown(e) {
      if (this.canDrag) {
        if (selectedDom == null) {
            selectedDom = this.$el;
            selectedDom.style.zIndex = globalZIndex++;
            //给需要移动的元素添加onmousedown事件
            selectedDom.onmousedown = function (ev) {
              var event = window.event || ev;
              // 获取屏幕中可视化的宽高的坐标
              var dx = event.clientX - selectedDom.offsetLeft; 
              var dy = event.clientY - selectedDom.offsetTop;
              // console.log(event);
              // console.log(dy)
              //实时改变目标元素odiv的位置
              document.onmousemove = function (ev) {
                  var event = window.event || ev;
                  selectedDom.style.left = event.clientX - dx + 'px';
                  selectedDom.style.top = event.clientY - dy + 'px';
              }
              //抬起停止拖动
              document.onmouseup = function () {
                  document.onmousemove = null;
                  document.onmouseup = null;
                  selectedDom = null;
              }
          }
        }
      }
    },
    updateSize() {
      if (this.type == '0') {
        this.width = this.span + '%';
      } else if (this.type == '1') {
        this.width = this.size.width + 'px';
        if (this.size.height == 0) {
          this.height = 'auto';
        } else {
          this.height = this.size.height + 'px';
        }
      }
    },
    updateLayout() {
      this.absoluteOn = this.layout == 'absolute';
      if (this.absoluteOn) {
        this.width = this.size.width + 'px';
        this.height = this.size.height + 'px';
        this.left = this.size.left + 'px';
        this.top = this.size.top + 'px';
      }
    }
  }
}
</script>

<style lang="scss" scoped>
@import '@/styles/mixin.scss';
.el-col-absolute-layout {
  position: absolute;
}
</style>
