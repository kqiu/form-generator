<template>
  <div :style="{'width': width, 'height': height, 'left': left, 'top': top, 'display': 'inline-block', 'vertical-align': 'top'}" :class="{'el-col-layout': true, 'el-col-absolute-layout': absoluteOn}">
    <slot>
    </slot>
  </div>
</template>
<script>
export default {
  components: {},
  props: ['span', 'type', 'size', 'layout'],
  data() {
    return {
      width: '',
      height: 'auto',
      left: '',
      top: '',
      absoluteOn: false
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
    console.log("updated>>>>>>>>", this.width, this.span)
  },
  mounted() {
    this.updateSize();
    this.updateLayout();
  },
  methods: {
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
