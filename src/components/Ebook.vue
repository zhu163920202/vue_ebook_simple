<template>
  <div class="ebook">
    <title-bar :ifTitleAndMenuShow = "ifTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitltAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar :ifTitleAndMenuShow = "ifTitleAndMenuShow" :fontSizeList="fontSizeList" :defaultFontSize='defaultFontSize' @setFontSize="setFontSize" :themeList="themeList" :defaultTheme="defaultTheme" @setTheme="setTheme"
    :bookAvailable="bookAvailable" @onProgressChange="onProgressChange" :navigation="navigation" @jumpTo="jumpTo" ref="menuBar"></menu-bar>
  </div>
</template>
<script>
  import TitleBar from '@/components/TitleBar'
  import MenuBar from '@/components/MenuBar'
  import Epub from 'epubjs'
  const DOWNLOAD_URL = '/static/2018_Book_BigDataInContext.epub'
  export default {
    components: {
      TitleBar,
      MenuBar
    },
    data () {
      return {
        ifTitleAndMenuShow: false,
        fontSizeList: [
          {fontSize: 12},
          {fontSize: 14},
          {fontSize: 16},
          {fontSize: 18},
          {fontSize: 20},
          {fontSize: 22},
          {fontSize: 24}
        ],
        defaultFontSize: 16,
        themeList: [
          {
            name: 'default',
            style: {
              body: {
                'color': '#000', 'background': '#fff'
              }
            }
          },
          {
            name: 'eye',
            style: {
              body: {
                'color': '#000', 'background': '#ceeaba'
              }
            }
          },
          {
            name: 'night',
            style: {
              body: {
                'color': '#fff', 'background': '#000'
              }
            }
          },
          {
            name: 'gold',
            style: {
              body: {
                'color': '#000', 'background': 'rgb(241, 236, 226)'
              }
            }
          }
        ],
        defaultTheme: 0,
        // 表图书是否处于可用状态
        bookAvailable: false
      }
    },
    methods: {
      // 根据链接跳转到指定位置
      jumpTo (href) {
        this.rendition.display(href)
        // 标题栏和菜单栏隐藏
        this.hideTitleAndMenu()
      },
      hideTitleAndMenu () {
        // 隐藏菜单栏和标题栏
        this.ifTitleAndMenuShow = false
        // 隐藏菜单栏弹出的设置栏
        this.$refs.menuBar.hideSetting()
        // 隐藏目录
        this.$refs.menuBar.hideContent()
      },
      onProgressChange (progress) {
        const percentage = progress / 100
        const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
        this.rendition.display(location)
      },
      setTheme (index) {
        this.themes.select(this.themeList[index].name)
        this.defaultTheme = index
      },
      registerTheme () {
        this.themeList.forEach(theme => {
          this.themes.register(theme.name, theme.style)
        })
      },
      setFontSize (fontSize) {
        this.defaultFontSize = fontSize
        if (this.themes) {
          this.themes.fontSize(fontSize + 'px')
        }
      },
      toggleTitltAndMenu () {
        this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
        // 如果菜单栏隐藏则设置栏也应该隐藏
        if (!this.ifTitleAndMenuShow) {
          // 相当于DOM选择器，快速选择相应元素
          this.$refs.menuBar.hideSetting()
        }
      },
      // 向前翻页 Rendition 的 prev next 方法
      prevPage () {
        if (this.rendition) {
          this.rendition.prev()
        }
      },
      // 向后翻页
      nextPage () {
        if (this.rendition) {
          this.rendition.next()
        }
      },
      // 电子书的解析和渲染
      showEpub () {
        // 生成book对象
        this.book = new Epub(DOWNLOAD_URL)
        // console.log(this.book)
        // 生成Redition对象，通过renderTo方法挂载在id为read的DOM节点上
        this.rendition = this.book.renderTo('read', {
          width: window.innerWidth,
          height: window.innerHeight
        })
        // 通过display方法渲染电子书
        this.rendition.display()
        // 获取themes对象
        this.themes = this.rendition.themes
        // 调用方法设置默认字体大小
        this.setFontSize(this.defaultFontSize)
        this.registerTheme()
        this.setTheme(this.defaultTheme)
        this.book.ready.then(() => {
          this.navigation = this.book.navigation
          // console.log(this.navigation) toc为数组存放章节链接href
          return this.book.locations.generate()
        }).then(result => {
          this.locations = this.book.locations
          this.bookAvailable = true
        })
      }
    },
    mounted () {
      this.showEpub()
    }
  }
</script>
<style lang="scss" scoped>
  @import '@/assets/styles/global.scss';
  .ebook {
    position: relative;
    .read-wrapper {
      .mask {
        position: absolute;
        left: 0;
        top: 0;
        z-index: 99;
        display: flex;
        width: 100%;
        height: 100%;
        .left {
          flex: 0 0 px2rem(100);
          // background: red;
        }
        .center {
          flex: 1;
        }
        .right {
          flex: 0 0 px2rem(100);
          // background: blue;
        }
      }
    }
  }
</style>
