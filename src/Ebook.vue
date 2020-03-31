<template>
  <div class="ebook">
    <!-- 标题栏 -->
    <TitleBar :ifTitleAndMenuShow='ifTitleAndMenuShow'></TitleBar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <MenuBar 
      :ifTitleAndMenuShow = 'ifTitleAndMenuShow'
      :defaultFontSize = 'defaultFontSize'
      ref="MenuBar" 
      :fontSizeList = 'fontSizeList'
      @setFontSize='setFontSize'
      :themeList = 'themeList'
      :defaultTheme = 'defaultTheme'
      @setTheme = 'setTheme'
      ></MenuBar>
  </div>
</template>

<script>
import TitleBar from "@/components/TitleBar";
import MenuBar from "@/components/MenuBar";
import Epub from "epubjs";
import { fail } from 'assert';
const DOWNLOAD_URL = '/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
global.ePub = Epub //定义全局变量

export default {
  components:{
    TitleBar,MenuBar
  },
  data() {
    return {
      ifTitleAndMenuShow:false,
      fontSizeList: [
        {fontSize: 12},
        {fontSize: 14},
        {fontSize: 16},
        {fontSize: 18},
        {fontSize: 20},
        {fontSize: 22},
        {fontSize: 24},
      ],
      defaultFontSize: 16,
      theme:'',
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000','background': '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              'color': '#000','background': '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              'color': '#fff','background': '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              'color': '#000','background': 'rgb(241,236,226)'
            }
          }
        },
      ],
      defaultTheme:0,
    }
  },
  methods: {
    setTheme(index) {
      this.themes.select(this.themeList[index].name);
      this.defaultTheme = index;
    },
    // 注册主题
    registerTheme() {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style);
      });
    },
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize;
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    toggleTitleAndMenu(){
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow;
      if(!this.ifTitleAndMenuShow){
        this.$refs.MenuBar.hideSetting();
      }
    },
    // 返回上一页
    prevPage() {
      // rendition.prev
      if (this.rendition) {
        this.rendition.prev()
      }
    },

    // 下一页
    nextPage() {
      // rendition.next
      if (this.rendition) {
        this.rendition.next();
      }
    },

    // 电子书的解析与渲染
    showEpub () {
      // 生成Book对象
      this.book = new Epub(DOWNLOAD_URL)
      // 生成Rendition  使电子书充满全屏
      this.rendition = this.book.renderTo('read',{
        width: window.innerWidth,
        height: window.innerHeight
      })
      // 通过Rendition.display渲染电子书
      this.rendition.display();
      // 获取theme对象
      this.themes = this.rendition.themes;
      // 设置默认字体
      this.setFontSize(this.defaultFontSize);
      // 设置主题
      // this.themes.register(name,styles);
      // this.themes.select(name)
      this.registerTheme();
      this.setTheme(this.defaultTheme)
    }
  },
  mounted() {
    this.showEpub();
  }
}
</script>

<style lang="scss" scoped>
 @import 'assets/styles/global';
 .ebook{
   position: relative;
   .read-wrapper{
     .mask{
       position: absolute;//绝对定位  相对ebook
       top: 0;
       left: 0;
       z-index: 100;
       display: flex;//弹性布局
       width: 100%;
       height: 100%;
     }
     .left{
       flex: 0 0 px2rem(100);  //左边100像素
     }
     .center{
       flex: 1; //全铺满
     }
     .right{
       flex: 0 0 px2rem(100);
     }
   }
 }
</style>

