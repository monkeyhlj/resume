<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
  </div>
</template>

<script>
  import StyleEditor from './components/StyleEditor'
  import ResumeEditor from './components/ResumeEditor'
  import './assets/reset.css'

  export default {
    name: 'app',
    components: {
      StyleEditor,
      ResumeEditor
    },
    data() {
      return {
        interval: 40,
        currentStyle: '',
        enableHtml: false,
        fullStyle: [
          `/*
* Inspired by http://strml.net/
* 大家好，我是侯丽娟 :)
* 多年前在网上看到了一份动态编码的简历，链接如上，感觉很有意思。
* 最近正好在Github上看到了类似的小项目，说做就做，我也来试着写一份动态简历！
*/

/* 首先给所有元素加上过渡效果 */
* {
  transition: all .3s;
}

/* 白色背景太单调了，我们来点背景 */
html {
  color: rgb(222,222,222); 
  background: rgb(0,64,64);
}

/* 文字离边框太近了 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  margin: .5em;
  overflow: auto;
  width: 48vw; height: 90vh;
  background: rgb(48, 48, 48);
  box-shadow: -4px 4px 2px 0 rgba(0,0,0,0.3);
  transform: translateX(99%) rotateY(-10deg);
  transform-origin: right;
}

/* 代码高亮 */
.token.selector{ color: rgb(133,153,0); }
.token.property{ color: rgb(187,137,0); }
.token.punctuation{ color: yellow; }
.token.function{ color: rgb(42,161,152); }

/* 接下来我给自己准备一个编辑器 */
.resumeEditor{
  position: fixed; left: 0; top: 0;
  padding: .5em;  margin: .5em;
  width: 48vw; height: 90vh;
  border: 1px solid white;
  background: white; color: #222;
  transform: rotateY(10deg);
  transform-origin: left;
  overflow: auto;
  box-shadow: 0px 0px 20px 5px rgba(255,255,255,0.4);
}

/* 好了，我开始写简历了 */


`,
          `
/* 这个简历好像差点什么
 * 对了，这是 Markdown 格式的，我需要变成对 HR 更友好的格式
 * 简单，用开源工具翻译成 HTML 就行了
 */
`
          ,
          `
/* 再对 HTML 加点样式 */
.resumeEditor{
  padding: 2em;
}
.resumeEditor h2{
  display: inline-block;
  border-bottom: 1px solid;
  margin: 1em 0 .5em;
}
.resumeEditor ul,.resumeEditor ol{
  list-style: none;
}
.resumeEditor ul> li::before{
  content: '•';
  margin-right: .5em;
}
.resumeEditor ol {
  counter-reset: section;
}
.resumeEditor ol li::before {
  counter-increment: section;
  content: counters(section, ".") " ";
  margin-right: .5em;
}
.resumeEditor blockquote {
  margin: 1em;
  padding: .5em;
  background: #ddd;
}
`],
        currentMarkdown: '',
        fullMarkdown: `侯丽娟
====

坐标：四川成都。

目前就读于西南交通大学信息科学与技术学院，研一在读，专业为电子信息。


技能
====

后端开发
----
  - 用户管理
  - 权限管理
  - 博客系统
  - 小程序开发
  - API接口开发
  - ...

前端开发
----
  - Web前端开发
  - 微信小程序
  - uni-app
  - ...

技术及语言
----
  - Java: SpringMVC, SpringBoot, MyBatis, Shiro
  - Node.js: express, vue, react, axios
  - Python: pandas, numpy, matplotlib
  - DB: SQLServer, MySQL, redis
  - WebServer: apache, nginx, tomcat
  - OS: Ubuntu, CentOS, Windows
  - Others: Docker, git, Xmind, Axure
  - ...

项目经历
----

1. 建筑工程智慧消防系统
2. 智能桥梁检测系统
3. ...

链接
----

* [GitHub](https://github.com/monkeyhlj)
* [码云](https://gitee.com/monkeyhlj)
* [我的博客](https://blog.csdn.net/hhhmonkey)

> 该简历主要开发技术为Node.js，框架为Vue，如果你喜欢这个效果，Fork [我的项目](https://github.com/monkeyhlj/resume)，打造你自己的简历！

`
      }
    },
    created() {
      this.makeResume()
    },

    methods: {
      makeResume: async function () {
        await this.progressivelyShowStyle(0)
        await this.progressivelyShowResume()
        await this.progressivelyShowStyle(1)
        await this.showHtml()
        await this.progressivelyShowStyle(2)
      },
      showHtml: function () {
        return new Promise((resolve, reject) => {
          this.enableHtml = true
          resolve()
        })
      },
      progressivelyShowStyle(n) {
        return new Promise((resolve, reject) => {
          let interval = this.interval
          let showStyle = (async function () {
            let style = this.fullStyle[n]
            console.log("fullstyle"+style)
            if (!style) { return }
            // 计算前 n 个 style 的字符总数
            let length = this.fullStyle.filter((_, index) => index <= n).map((item) => item.length).reduce((p, c) => p + c, 0)
            console.log("length:"+length)
            let prefixLength = length - style.length
            console.log("prefixLength:"+prefixLength)
            if (this.currentStyle.length < length) {
              let l = this.currentStyle.length - prefixLength
              console.log("this.currentStyle.length - prefixLength:"+l)
              let char = style.substring(l, l + 1) || ' '
              this.currentStyle += char
              if (style.substring(l - 1, l) === '\n' && this.$refs.styleEditor) {
                this.$nextTick(() => {
                  this.$refs.styleEditor.goBottom()
                })
              }
              setTimeout(showStyle, interval)
            } else {
              resolve()
            }
          }).bind(this)
          showStyle()
        })
      },
      progressivelyShowResume() {
        return new Promise((resolve, reject) => {
          let length = this.fullMarkdown.length
          let interval = this.interval
          let showResume = () => {
            if (this.currentMarkdown.length < length) {
              this.currentMarkdown = this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
              let lastChar = this.currentMarkdown[this.currentMarkdown.length - 1]
              let prevChar = this.currentMarkdown[this.currentMarkdown.length - 2]
              if (prevChar === '\n' && this.$refs.resumeEditor) {
                this.$nextTick(() => this.$refs.resumeEditor.goBottom())
              }
              setTimeout(showResume, interval)
            } else {
              resolve()
            }
          }
          showResume()
        })
      }
    }
  }

</script>

<style scoped>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  html {
    min-height: 100vh;
  }
  *{
    box-sizing: border-box;
  }
</style>
