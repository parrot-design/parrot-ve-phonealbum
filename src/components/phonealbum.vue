
<!-- —————————————↓HTML————————分界线———————————————————————— -->
<template>
  <transition name="fade" appear>
    <div
      class="vue-phonealbum-container"
      :style="{ zIndex: zIndex }"
      :class="[{ 'is-show-parts': partsVisible }]"
      v-if="phoneAlbum"
      @mouseover="handleMouseOver"
      @mouseout="handleMouseOut"
    >
      <div
        class="vue-phonealbum-bg"
        :style="{ background: backgroundColor }"
      ></div>
      <div class="vue-phonealbum-inner" :class="{ grid: isGrid }">
        <div class="vue-phonealbum-stage">
          <div
            class="vue-phonealbum-slide"
            :style="{
              padding: `${padding}px 0`,
              transform: isZoom
                ? `scale(${zoomMultiples}, ${zoomMultiples})`
                : 'scale(1, 1)',
            }"
          >
            <transition-group name="img" tag="div" class="transition-wrapper">
              <template v-for="(imgItem, imgIndex) in imgsArr">
                <CustomImage
                  :key="imgIndex"
                  :isMobile="isMobile"
                  :imgInfo="imgItem"
                  class="vue-phonealbum-slide-img"
                  v-if="showImage(imgIndex)"
                />
              </template>
            </transition-group>
          </div>
        </div>
        <div class="vue-phonealbum-navigation">
          <div
            class="vue-phonealbum-button left"
            @click="last"
            :class="[{ disabled: isNavigationDisabledLeft }]"
          >
            <span class="ve-phone-iconfont icon-zuojiantou"></span>
          </div>
          <div
            class="vue-phonealbum-button right"
            @click="next"
            :class="[{ disabled: isNavigationDisabledRight }]"
          >
            <span class="ve-phone-iconfont icon-youjiantou"></span>
          </div>
        </div>
        <div class="vue-phonealbum-infobar">
          <span>1</span>
          <span>/</span>
          <span>2</span>
        </div>
        <div class="vue-phonealbum-toolbar">
          <div class="vue-phonealbum-button">
            <span
              class="ve-phone-iconfont icon-sousuo"
              @click="handleZoom"
            ></span>
          </div>
          <div class="vue-phonealbum-button" @click="handlePlay">
            <span
              class="ve-phone-iconfont"
              :class="[isPlay ? 'icon-zanting' : 'icon-bofang']"
            ></span>
          </div>
          <div class="vue-phonealbum-button" @click="handleSwitch">
            <span class="ve-phone-iconfont icon-24gf-grid"></span>
          </div>
          <div class="vue-phonealbum-button" @click="handleClose">
            <span class="ve-phone-iconfont icon-guanbi"></span>
          </div>
        </div>
        <div
          class="vue-phonealbum-progress"
          :style="{
            'transition-duration': progressStart ? `${playSpeed}s` : '0s',
            transform: progressStart ? 'scaleX(1)' : 'scaleX(0)',
            height: `${progressHeight}px`,
            background: progressColor,
          }"
          @transitionend="handleTransitionEnd"
        ></div>
      </div>
      <div v-if="isGrid" class="vue-phonealbum-thumb">
        <div class="vue-phonealbum-thumb__list">
          <template v-for="(imgItem, imgIndex) in imgsArr">
            <div
              :key="imgIndex"
              class="vue-phonealbum-thumb__list-item"
              :class="{ active: showImage(imgIndex) }"
              @click="handleClickGridItem(imgIndex)"
            >
            <img :src="imgItem.src" /></div>
          </template>
        </div>
      </div>
    </div>
  </transition>
</template>

<!-- —————————————↓JS————————分界线———————————————————————— -->
<script>
import CustomImage from "./image.vue";
import { isMobile } from "../util";

export default {
  name: "pt-vue-phonealbum",
  props: {
    //组件层级
    zIndex: {
      default: 10000,
      type: Number,
    },
    //鼠标离开时关闭操作按钮的时间
    leaveTime: {
      type: Number,
      default: 2000,
    },
    //图片数组
    imgsArr: {
      type: Array,
      default: [],
    },
    //padding距离
    padding: {
      type: Number,
      default: 44,
    },
    //缩放倍数
    zoomMultiples: {
      type: Number,
      default: 1.5,
    },
    //播放速度 秒
    playSpeed: {
      type: Number,
      default: 2,
    },
    //进度条高度
    progressHeight: {
      type: Number,
      default: 3,
    },
    //进度条颜色
    progressColor: {
      type: String,
      default: "#ff5268",
    },
    backgroundColor: {
      type: String,
      default: "#111",
    },
  },
  data() {
    return {
      //指定组件是否渲染
      phoneAlbum: false,
      //零件是否渲染
      partsVisible: false,
      leaveTimeout: null,
      currentIndex: 0,
      isMobile: false,
      //默认没有缩放
      isZoom: false,
      //是否正在播放
      isPlay: false,
      //进度条开始
      progressStart: false,
      // //进度条结束
      // progressEnd:false,
      //播放间隔
      playInterval: null,
      //是否有网格
      isGrid: false,
    };
  },
  components: {
    CustomImage,
  },
  computed: {
    isNavigationDisabledLeft() {
      return this.currentIndex === 0;
    },
    isNavigationDisabledRight() {
      return this.currentIndex === this.imgsArr.length - 1;
    },
  },
  mounted() {
    this.isMobile = isMobile();
  },
  methods: {
    show: function (options = {}) {
      this.currentIndex = Number(options.index) || 0;
      this.phoneAlbum = true;
    },
    reset: function () {
      this.currentIndex = 0;
      this.phoneAlbum = false;
      //关闭时关闭轮播
      this.progressStart = false;
      this.isPlay = false;
    },
    handleClose: function () {
      this.reset();
    },
    handleClickGridItem:function(index){
      this.currentIndex = index;
    },
    handleMouseOver: function () {
      this.partsVisible = true;
      if (this.leaveTimeout) {
        clearTimeout(this.leaveTimeout);
        this.leaveTimeout = null;
      }
    },
    handleMouseOut: function () {
      this.leaveTimeout = setTimeout(() => {
        this.partsVisible = false;
      }, this.leaveTime);
    },
    //是否展示图片
    showImage: function (index) {
      return this.currentIndex === index;
    },
    dealPlay: function (dealPlay) {
      if (dealPlay === "auto") {
        return;
      }
      if (this.isPlay) {
        this.isPlay = false;
      }
    },
    //上一张
    last: function () {
      this.dealPlay();
      this.currentIndex = Math.max(this.currentIndex - 1, 0);
    },
    //下一章
    next: function (isAuto) {
      this.dealPlay(isAuto);
      if (this.isNavigationDisabledRight) {
        return;
      }
      //如果正在进行幻灯片
      if (this.isPlay) {
        this.progressStart = true;
      }
      this.currentIndex = Math.min(
        this.currentIndex + 1,
        this.imgsArr.length - 1
      );
    },
    //点击放大镜按钮
    handleZoom() {
      this.isZoom = !this.isZoom;
    },
    //播放
    handlePlay() {
      this.isPlay = !this.isPlay;
      this.progressStart = true;
      if (!this.isPlay) {
        this.progressStart = false;
      }
    },
    handleTransitionEnd() {
      this.progressStart = false;
      this.$nextTick(() => {
        this.next("auto");
      });
    },
    handleSwitch() {
      this.isGrid = !this.isGrid;
    },
  },
};
</script>

<!-- —————————————↓Css————————分界线———————————————————————— -->
<style lang="scss">
@import "../icon/iconfont.scss";
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
.vue-phonealbum {
  &-container {
    position: fixed;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
    outline: none;
    -webkit-tap-highlight-color: transparent;
    touch-action: manipulation;
    transform: translateZ(0);

    * {
      box-sizing: border-box;
    }

    &.is-show-parts {
      .vue-phonealbum-infobar {
        opacity: 1;
        visibility: visible;
      }
      .vue-phonealbum-toolbar {
        opacity: 1;
        visibility: visible;
      }
    }
  }

  &-button {
    color: #ccc;
    border: 0;
    border-radius: 0;
    box-shadow: none;
    cursor: pointer;
    display: inline-flex;
    height: 44px;
    margin: 0;
    position: relative;
    transition: color 0.2s;
    vertical-align: top;
    visibility: inherit;
    width: 44px;
    background-color: #111;
    cursor: pointer;
    align-items: center;
    justify-content: center;

    &:hover {
      color: #fff;
    }

    .ve-phone-iconfont {
      color: inherit;
      font-size: 1.4em;
    }

    &.disabled {
      opacity: 0.5;
      cursor: not-allowed;

      &:hover {
        color: #ccc;
      }
    }
  }

  &-bg,
  &-inner,
  &-stage {
    bottom: 0;
    left: 0;
    position: absolute;
    right: 0;
    top: 0;
  }

  &-inner {
    &.grid {
      right: 212px;
    }
  }

  &-bg {
    //background: rgb(230, 106, 106);
    opacity: 0.9;
  }

  &-infobar,
  &-toolbar {
    opacity: 0;
    position: absolute;
    visibility: hidden;
    transition: opacity 0.25s ease 0s, visibility 0s ease 0s;
  }

  &-infobar {
    font-size: 13px;
    height: 44px;
    left: 0;
    line-height: 44px;
    min-width: 44px;
    mix-blend-mode: difference;
    padding: 0 10px;
    pointer-events: none;
    top: 0;
    user-select: none;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  &-toolbar {
    right: 0;
    top: 0;
    display: flex;
  }

  &-navigation {
    .vue-phonealbum-button {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      &.left {
        left: 0;
      }
      &.right {
        right: 0;
      }
    }
  }

  &-slide {
    height: 100%;
    width: 100%;
    left: 0;
    top: 0;
    outline: none;
    overflow: auto;
    padding: 44px;
    position: absolute;
    text-align: center;
    transition: transform 0.2s cubic-bezier(0.075, 0.82, 0.165, 1);
    white-space: normal;

    .img-enter-active,
    .img-leave-active {
      transition: all 0.2s;
    }
    .img-enter,
    .img-leave-to {
      opacity: 0;
    }
    .img-enter-to,
    .img-leave {
      opacity: 1;
    }

    .transition-wrapper {
      height: 100%;
      overflow: hidden;
    }
  }

  &-slide-img {
    overflow: hidden;
    height: 100%;

    img {
      height: 100%;
    }
  }

  &-progress {
    left: 0;
    position: absolute;
    right: 0;
    top: 0;
    transform: scaleX(0);
    transform-origin: 0;
    transition-property: transform;
    transition-timing-function: linear;
  }

  &-thumb {
    background: #ddd;
    bottom: 0;
    margin: 0;
    -webkit-overflow-scrolling: touch;
    padding: 2px 2px 4px;
    position: absolute;
    right: 0;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
    top: 0;
    width: 212px;
    z-index: 99995;

    &__list {
      font-size: 0;
      height: 100%;
      list-style: none;
      margin: 0;
      overflow-x: hidden;
      overflow-y: auto;
      padding: 0;
      position: absolute;
      position: relative; 
      width: 100%;

      &-item {
        background-color: rgba(0, 0, 0, 0.1);
        height: 75px;
        margin: 2px;
        max-height: calc(100% - 8px);
        max-width: calc(50% - 4px);
        outline: none;
        overflow: hidden;
        padding: 0;
        position: relative;
        -webkit-tap-highlight-color: transparent;
        width: 100px;
        display: inline-block;

        img{
          width:100%;
          height:100%;
        }

        &.active {
          &:after {
            opacity: 1;
          }
        }

        &:after {
          border: 4px solid #ff5268;
          bottom: 0;
          content: "";
          left: 0;
          opacity: 0;
          position: absolute;
          right: 0;
          top: 0;
          transition: all 0.2s cubic-bezier(0.25, 0.46, 0.45, 0.94);
        }
      }
    }
  }
}
</style>
