<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div class="menu-wrapper" v-show="ifTitleAndMenuShow" :class="{'hide-box-shadow': ifSettingShow || !ifTitleAndMenuShow}">
        <div class="icon-wrapper">
          <span class="icon-menu icon" @click="showSetting(3)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-progress icon"  @click="showSetting(2)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-bright icon" @click="showSetting(1)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-a icon" @click="showSetting(0)">A</span>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper" v-show="ifSettingShow">
        <div class="setting-font-size" v-if="showTag === 0">
          <div class="preview" :style="{fontSize: fontSizeList[0].fontSize + 'px'}">A</div>
          <div class="select">
            <div class="select-wrapper" v-for="(item, index) in fontSizeList" @click="setFontSize(item.fontSize)" :key="index">
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defaultFontSize === item.fontSize">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div class="preview" :style="{fontSize: fontSizeList[fontSizeList.length - 1].fontSize + 'px'}">A</div>
        </div>
        <div class="setting-theme" v-else-if="showTag === 1">
          <div class="setting-theme-item" v-for="(item, index) in themeList" :key="index" @click="setTheme(index)">
            <div class="preview" :style="{'background': item.style.body.background}" :class="{'no-border': item.style.body.background !== '#fff'}"></div>
            <div class="text" :class="{'selected': index === defaultTheme}">{{ item.name }}</div>
          </div>
        </div>
        <div class="setting-progress" v-else-if="showTag === 2">
          <div class="progress-wrapper">
            <!-- change 松开进度条时调用的方法 input在拖动进度条时调用 disabled 进度条没有解析完成时-->
            <input class="progress" type="range" max="100" min="0" step="1" @change="onProgressChange($event.target.value)" @input="onProgressInput($event.target.value)" :value="progress" :disabled="!bookAvailable" ref="progress">
          </div>
          <div class="text-wrapper">
              <span>{{ bookAvailable ? progress + '%' : 'loading...'}}</span>
          </div>
        </div>
      </div>
    </transition>
    <content-view :ifShowContent="ifShowContent" v-show="ifShowContent" :navigation="navigation" :bookAvailable="bookAvailable" @jumpTo="jumpTo"></content-view>
    <transition name="fade">
        <!-- 目录栏蒙版 渐入渐出效果-->
        <div class="content-mask" v-show="ifShowContent" @click="hideContent"></div>
    </transition>
  </div>
</template>
<script>
  import ContentView from '@/components/Content'
  export default {
    components: {
      ContentView
    },
    data () {
      return {
        ifSettingShow: false,
        showTag: 0,
        progress: 0,
        ifShowContent: false
      }
    },
    props: {
      ifTitleAndMenuShow: {
        type: Boolean,
        default: false
      },
      fontSizeList: Array,
      defaultFontSize: Number,
      themeList: Array,
      defaultTheme: Number,
      bookAvailable: {
        type: Boolean,
        default: false
      },
      navigation: Object
    },
    methods: {
      jumpTo (href) {
        this.$emit('jumpTo', href)
      },
      hideContent () {
        this.ifShowContent = false
      },
      onProgressInput (progress) {
        this.progress = progress
        // 根据百分比设置进度条百分比
        this.$refs.progress.style.backgroundSize = '$(this.progress) % 100%'
      },
      onProgressChange (progress) {
        this.$emit('onProgressChange', progress)
      },
      setTheme (index) {
        this.$emit('setTheme', index)
      },
      setFontSize (fontSize) {
        this.$emit('setFontSize', fontSize)
      },
      showSetting (tag) {
        this.showTag = tag
        // 如果tag为3需要将设置栏进行隐藏
        if (tag === 3) {
          this.ifSettingShow = false
          this.ifShowContent = true
        } else {
        this.ifSettingShow = true
        }
      },
      hideSetting () {
        this.ifSettingShow = false
      }
    }
  }
</script>
<style lang="scss" scoped>
  @import '@/assets/styles/global.scss';
  .menu-bar {
    .menu-wrapper {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: px2rem(48);
      display: flex;
      z-index: 100;
      background: white;
      box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);
      &.hide-box-shadow {
        box-shadow: none;
      }
      .icon-wrapper {
        flex: 1;
        @include center;
        .icon-progress {
          font-size: px2rem(28);
        }
        .icon-bright {
          font-size: px2rem(24);
        }
      }
      // &.slide-up-enter, &.slide-up-leave-to {
      //   transform: translate3d(0, 100%, 0)
      // }
      // &.slide-up-enter-to, &.slide-up-leave {
      //   transform: translate3d(0, 0, 0)
      // }
      // &.slide-up-enter-active, &.slide-up-leave-active {
      //   transition: all .3s linear;
      // }
    }
    .setting-wrapper {
      position: absolute;
      bottom: px2rem(48);
      left: 0;
      height: px2rem(60);
      width: 100%;
      z-index: 100;
      background: white;
      box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,.15);
      .setting-font-size {
        display: flex;
        height: 100%;
        .preview {
          flex: 0 0 px2rem(40);
          @include center;
          // background: blue;
        }
        .select {
          flex: 1;
          display: flex;
          .select-wrapper {
            flex: 1;
            // background: pink;
            display: flex;
            align-items: center;
            &:first-child {
              .line {
                &:first-child {
                  // background: red;
                  border-top: none;
                }
              }
            }
            &:last-child {
              .line {
                &:last-child {
                  border-top: none;
                }
              }
            }
            .line {
              flex: 1;
              height: 0;
              border-top: px2rem(1) solid #ccc;
            }
            .point-wrapper {
              position: relative;
              flex: 0 0 0;
              width: 0;
              height: px2rem(7);
              border-left: px2rem(1) solid #ccc;
              .point {
                position: absolute;
                top: px2rem(-8);
                left: px2rem(-10);
                width: px2rem(20);
                height: px2rem(20);
                border-radius: 50%;
                background: #fff;
                border: px2rem(1) solid #ccc;
                box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, .15);
                @include center;
                .small-point {
                  width: px2rem(5);
                  height: px2rem(5);
                  border-radius: 50%;
                  background: #000;
                }
              }
            }
          }
        }
      }
      .setting-theme {
        height: 100%;
        display: flex;
        .setting-theme-item {
          flex: 1;
          display: flex;
          flex-direction: column;
          padding: px2rem(5);
          box-sizing: border-box;
          .preview {
            flex: 1;
            border: px2rem(1) solid #ccc;
            box-sizing: border-box;
            &.no-border {
              border: none;
            }
          }
          .text {
            flex: 0 0 px2rem(20);
            font-size: px2rem(14);
            color: #ccc;
            @include center;
            &.selected {
              color: #333;
            }
          }
        }
      }
      .setting-progress {
        width: 100%;
        height: 100%;
        @include center;
        padding: 0 px2rem(30);
        display: flex;
        flex-direction: column;
        box-sizing: border-box;
        .progress-wrapper {
          width: 100%;
          height: 100%;
          @include center;
          padding: 0 px2rem(30);
          box-sizing: border-box;
          .progress {
            width: 100%;
            // 覆盖进度条默认样式
            -webkit-appearance: none;
            height: px2rem(2);
            // 设置进度条两端的颜色，左端较深，右端浅
            background: -webkit-linear-gradient( #999, #999)   no-repeat, #ddd;
            // 设置背景图像宽0，高100%
            background-size: 0 100%;
            &:focus {
              outline: none;
            }
            &::-webkit-slider-thumb {
              -webkit-appearance: none;
              height: px2rem(20);
              width: px2rem(20);
              border-radius: 50%;
              background: white;
              box-shadow: 0 4px 4px rgba(0, 0, 0, .15);
              border: px2rem(1) solid #ddd;
            }
          }
        }
        .text-wrapper {
          width: 100%;
          @include center;
          font-size: px2rem(16);
          color: #333;
        }
      }
    }
    .content-mask {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      display: flex;
      width: 100%;
      height: 100%;
      background: rgba(51, 51, 51, .8);
    }
  }
</style>
