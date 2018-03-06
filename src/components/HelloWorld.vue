<template>
  <div id="app" class="app">
    <!-- <nav-controller :pageNum="pageNum"></nav-controller> -->
    <page :currentPage="currentPage">
      <h1 class="text-center font60 color9">{{name}}</h1>
      <section class="animate" ref="section1">
        <p class="demo-intro color6">外号：华仔、胖子，90后纯屌丝，2年前端，未婚</p>
        <p class="demo-intro">工作经验较短，但是学习技术的心不减
          <a href="https://github.com/vuejs/vue-cli" target="_blank"></a>
          <a href="https://github.com/vuejs-templates/webpack-simple" target="_blank"></a>
        </p>
        <p class="demo-intro">vue、webpack，均能上手开发</p>
      </section>
    </page>
    <page :currentPage="currentPage">
      <h1 class="text-center">工作经历</h1>
      <section class="animate move-left">
        <p>在App.vue中修改data函数返回的内容，即为修改相应的配置。现在可以配置三个属性：currentPage、arrowAnimation和options</p>
        <dl>
          <dt>currentPage:</dt>
          <dd>表示当前显示的页面，通过设置currentPage可以改变初始显示的界面</dd>
        </dl>
        <dl>
          <dt>controllerOption:</dt>
          <dd>该属性表示控制器的配置属性，可以自由扩展
            <ul>
              <li>arrowsType表示页面控制器的上下箭头显示类型：no（不显示箭头）、normal（显示箭头）、animate（显示有动画效果的箭头）</li>
            </ul>
          </dd>
        </dl>
        <dl>
          <dt>options:</dt>
          <dd>该属性是一个数组，数组的每一项都是一个对象，通过设置对象内的值，可以改变对应的page组件的样式，可以自由扩展
            <ul>
              <li>
                <span class="text-bold">background:</span> 表示相应page的背景样式</li>
              <li>
                <span class="text-bold">color:</span> 表示相应page的文字颜色（可以手动设置css样式覆盖）</li>
              <li>
                <span class="text-bold">isCenter:</span> 表示相应page的内容是否居中（水平和垂直都包括）</li>
              <li>下一页介绍options内的方法属性</li>
            </ul>
          </dd>
        </dl>
      </section>
    </page>
    <page :currentPage="currentPage">
      <h1 class="text-center">项目经验</h1>
      <section class="animate move-left">
        <p>在每个options的对象中，可以设置两种方法：beforeLeave 和 afterEnter</p>
        <ul>
          <li>beforeLeave 方法表示在离开当前页面前所做的操作</li>
          <li>afterEnter 方法表示在进入当前页面后所做的操作</li>
        </ul>
        <p>这两个方法都有一个默认参数，该参数为当前Page的vue组件实例，方法的this为App.vue的组件实例</p>
      </section>
    </page>
    <page :currentPage="currentPage">
      <h1 class="text-center">联系方式</h1>
      <section class="animate move-left">
        <img class="avatar" src="../assets/avatar.jpg" alt="头像">
        <div class="author-info">
          <p>昵称：Colton</p>
          <p>学校：长沙理工大学</p>
          <p>我的博客地址：
            <a href="http://zhenhualiu.com" target="_blank">http://zhenhualiu.com</a>
          </p>
          <p>我的Github：
            <a href="https://github.com/Hzert" target="_blank">https://github.com/Hzert</a>
          </p>
        </div>
      </section>
    </page>
    <page-controller :pageNum="pageNum" :currentPage="currentPage" @changePage="changePage" :option="controllerOption"></page-controller>
    <nav-controller :pageNum="pageNum" :currentPage="currentPage" @changePage="changePage" :option="controllerOption"></nav-controller>
  </div>
</template>

<script>
import Page from './page.vue'
import PageController from './pageController.vue'
import NavController from './navController.vue'

// 页面进出动画
function afterEnterAnimate ($child) {
  $child.$el.querySelector('.animate').classList.remove('move-left', 'move-right')
}
function beforeLeaveAnimate ($child) {
  let moveType = Math.random() > 0.5 ? 'move-left' : 'move-right'
  $child.$el.querySelector('.animate').classList.add(moveType)
}

export default {
  name: 'app',
  data () {
    return {
      name: '刘振华',
      currentPage: 1,
      index: String,
      options: [
        {
          // the color of background
          background: 'rgba(255, 255, 255,255)',
          // the color of text
          color: '#000',
          // is content center
          isCenter: true,
          // the function before page show
          afterEnter: afterEnterAnimate,
          // the function after page show
          beforeLeave: beforeLeaveAnimate
        },
        {
          background: 'rgba(255, 255, 255,255)',
          color: '#000',
          isCenter: true,
          afterEnter: afterEnterAnimate,
          beforeLeave: beforeLeaveAnimate
        },
        {
          background: 'rgba(255, 255, 255,255)',
          color: '#000',
          isCenter: true,
          afterEnter: afterEnterAnimate,
          beforeLeave: beforeLeaveAnimate
        },
        {
          background: 'rgba(255, 255, 255,255)',
          color: '#000',
          isCenter: true,
          afterEnter: afterEnterAnimate,
          beforeLeave: beforeLeaveAnimate
        }
      ],
      controllerOption: {
        arrowsType: false,
        navbar: true,
        highlight: true,
        loop: false
      }
    }
  },
  computed: {
    // 总page数
    pageNum () {
      return this.options.length
    }
  },
  methods: {
    changePage (index) {
      // beforeLeave Hook
      let beforeIndex = this.currentPage - 1
      let leaveFunction = this.options[beforeIndex].beforeLeave
      typeof leaveFunction === 'function' && leaveFunction.call(this, this.$children[beforeIndex])
      // change page
      this.currentPage = index
      // afterEnter Hook
      let nextIndex = index - 1
      let enterFunction = this.options[nextIndex].afterEnter
      this.$nextTick(function () {
        typeof enterFunction === 'function' && enterFunction.call(this, this.$children[nextIndex])
      })
    }
  },
  components: {
    Page, PageController, NavController
  },
  mounted () {
    console.log(this.$children)
    this.$children.forEach((child, index) => {
      // 动态设置各个page内的options
      console.log(child + '=>' + index)
      if (child.option === null) {
        let childOption = this.options[index]
        this.$set(childOption, 'index', index + 1)
        child.option = childOption
      }
    })
  }
}
</script>
<style scoped>
.app {
  height: 100%;
  width: 100%;
}

.person-img {
  width: 223px;
  height: 185px;
  float: left;
  background-color: #fff;
  box-shadow: 3px 3px 10px #999;
}

.person-img img {
  height: 100%;
  width: 100%;
}

.person-basic-info {
  overflow: hidden;
  float: right;
  margin-left: 40px;
}

.info-line {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: flex-start;
}

.text-center {
  text-align: center;
}

.text-bold {
  font-weight: bold;
}

.demo-intro {
  text-indent: 2em;
}
.font60{
  font-size: 60px;
}
.color9{
  color: #999;
}
.color6{
  color: #666;
}
dt {
  font-weight: bold;
  font-size: 16px;
}

ul {
  padding-left: 1em;
}

.avatar {
  margin: 10px auto;
  display: block;
  box-shadow: 1px 1px 5px #666;
}

.author-info {
  text-align: center;
}
</style>
