<!--
  设计思路：
    1.用store中的requestCount请求计数池来判断当前是否有正在使用tipRequest的请求
    2.当requestCount由0变为其他数字，表示应当开始动画
    3.当requestCount由非零变非零，不做处理
    4.当requestCount变为0，表示应当结束动画
-->
<template>
  <div class="loading-animation-component" v-show="isShowMask">
    <div class="progress-box">
      <div :class="['progress-fill', classOfProgress, classOfTransition]" ref="fill"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'loading-animation-component',
  props: {
    mode: {  //当前进度条的展现形式  1.speed 请求结束时立即关闭遮罩层 2.其他有后续动画，但影响页面操作
      type: String,
      default: 'speed'
    }
  },
  data () {
    return {
      isShowMask: false,
      classOfProgress: 'fill-0',
      classOfTransition: 'transition-none',
      timer: null
    }
  },
  methods: {
    startProgress () {
      if (this.isShowMask) return;
      this.isShowMask = true;
      this.classOfTransition = 'transition-long';
      // TODO 这里由于使用$nextTick来变更class后transition无法执行,暂时使用timeout来代替，期望不使用timeout来避免这20ms的延迟
      setTimeout(() => {
        this.classOfProgress = 'fill-95';
      }, 20);
    },
    completeProgress () {
      this.classOfTransition = 'transition-short';
      this.classOfProgress = 'fill-100';
    },
    endTransition () {
      this.isShowMask = false;
      this.classOfProgress = 'fill-0';
      this.classOfTransition = 'transition-none';
    }
  },
  computed: {
    requestCount () {
      return this.$store.state.requestCount;
    }
  },
  watch: {
    requestCount (newCount, oldCount) {
      if (newCount !== 0 && oldCount === 0) this.startProgress();
      if (newCount === 0 && oldCount !== 0) this.mode === 'speed' ? this.endTransition() : this.completeProgress();
    }
  },
  mounted () {
    this.$refs.fill.addEventListener('transitionend', () => {  //由于transitionend时无法保证请求池内没有请求了，因此加定时器去判断当前请求是否都结束了
      if (this.classOfProgress === 'fill-100') {
        if (this.requestCount) {
          this.timer = setInterval(() => {
            if (!this.requestCount) {
              this.endTransition();
              clearInterval(this.timer);
              this.timer = null;
            }
          }, 20);
        } else {
          this.endTransition();
        }
      }
    });
  }
}
</script>

<style lang="scss">
  .loading-animation-component {
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background-color: #fff;
    z-index: 999;
    opacity: .6;
    .progress-box {
      height: 3px;
      position: relative;
      .progress-fill {
        position: absolute;
        top: 0;
        left: 0;
        height: 100%;
        background-color: $color-theme;
        &.transition-long {
          transition: width 1s;
        }
        &.transition-short {
          transition: width 1s;
        }
        &.fill-0 {
          width: 0;
        }
        &.fill-95 {
          width: 95%;
        }
        &.fill-100 {
          width: 100%;
        }
      }
    }
  }
</style>