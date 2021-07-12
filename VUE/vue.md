#  Vue

##  插值语法

```html
<div id="root">
    <h1>hello,{{sth}}</h1>
    <button @click="mth">click</button>
</div>

<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{sth:"apple"},
        methods:{
            mth:function (){
                this.sth="banana"
            }
        }
    });
</script>
```

##  指令语法

* __v-bind__

```html
<div id="root2">
    <!--可简写为 <a :href="url">click me</a> -->
    <a v-bind:href="url">click me</a>
</div>

<script type="text/javascript">
    const root2 = new  Vue({
        el:"#root2",
        data:{
            url:"http://www.xzalab.top:4000",
        }
    });
</script>
```

##  数据绑定

__v-bind__:单项数据绑定

__v-model__:双向数据绑定(可通过前端修改value值)

```html
<div id="root">
    单项数据绑定：<input v-bind:value="msg1"> <br/><br/>
    双向数据绑定：<input v-model:value="msg2">
    <textarea cols="50" rows="20" v-model:value="msg3"></textarea>
</div>
<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{
            msg1:"sth",
            msg2:"sth",
            msg3:"sth"
        }
    });
</script>
```

##  数据代理

简化数据读取

```html
<div id="root">
    <h2>姓名:{{name}}</h2>
    <h2>属性:{{key}}</h2>
    <h2>技能:{{value}}</h2>
</div>

<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{
            name:"nico",
            key:"cat",
            value:"eat"
        }
    });
    console.log(root);
 </script>
```

<img src=".\img\image-20210629224118192.png" alt="image-20210629224118192" style="zoom:67%;" />

将 `root._data` 中的数据代理了一份到  `root` 中(底层为 `Object.defineProperty()` )

##   事件处理

`methods`

事件修饰符可以连写

```html
<div id="root">
	<button v-on:click="show1">show1</button>
	<button @click="show2">show2</button>
	<button @click="show3">输出按键信息</button>
	 <!-- 绑定事件，传递参数-->
	<button @click="show4(123,$event)">输出按键信息</button>
	 <!-- 绑定事件，阻止默认事件-->
	 <a href="https://www.baidu.com" @click.prevent="show5">点击跳转百度</a>
 	<!-- 绑定事件，阻止冒泡-->
	 <div @click="show6">
	<button @click.stop="show6">禁止冒泡</button>
    </div>
</div>

<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{ },
        methods:{
            show1(){
                console.log("show1");
            },
            show2(){
                console.log("show2");
            },
            show3(event){
                console.log(event);
                console.log(event.target.innerText);
            },
            show4(a,event){
                console.log(a,event);
            },
            show5(){
                console.log("禁止跳转")
            },
            show6(){
                window.alert("禁止冒泡")
            }

        }
    });
</script>
```



###  键盘事件

```html
<div id="root">
    <input @keyup.enter="show1" type="text" placeholder="按下回车提示数据">
    <!---键盘编码-->
    <input @keyup.37="show1" type="text" placeholder="按下左键提示数据">

</div>

<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{ },
        methods:{
            show1(event){
                console.log(event.target.value)
            },
        }
    });

</script>

```

##  计算属性

* 不使用框架编码时，用 `Object.defineProperty()` 表示动态属性：

  ```js
   let person = {
        firstName : "violet",
        lastName: "evergarden"
    }
  
    Object.defineProperty(person,"fullName",{
        set(value) {
            const arr = value.split(" ");
            this.firstName = arr[0];
            this.lastName = arr[1];
        },
  
        get(){
            return this.firstName +" " +this.lastName;
        }
    })
  ```
  
* 使用`Vue`编码，使用计算属性`computed`

  ```html
  <div id="root">
      姓：<input type="text" v-model="firstName">
      名：<input type="text" v-model="lastName">
      <span>全名{{fullName}}</span>
  
  </div>
  
  <script type="text/javascript">
      const root = new Vue({
          el:"#root",
          data:{
              firstName:"violet",
              lastName:"evergarden"
          },
          methods:{},
          computed:{
              /*简写
              fullName(){
                  return this.firstName + " " + this.lastName;
              }
               */
              // 完整写法
              fullName:{
                  set(value){
                      const arr = value.split(" ");
                      this.firstName = arr[0];
                      this.lastName = arr[1];
                  },
                  get(){
                      return this.firstName +" " +this.lastName;
                  }
              }
  
          }
      });
  </script>
  ```

## 数据监视

`watch`

```html
<div id="root" style="color: chocolate; background: darkgreen">
    姓：  <input type="text" v-model="firstName">
    <br/>
    名:  <input type="text" v-model="lastName">
    <br>
    <span>全名: {{fullName}}</span><br/>
    全名：<input type="text" v-model="fullName">

</div>

<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{
            firstName:"violet",
            lastName:"evergarden",
            fullName:""
        },

        watch:{
            /*简单版
            firstName(newValue , oldValue){
                console.log("firstName改变" , newValue , oldValue);
                this.fullName = newValue + " " + this.lastName;
            },
            lastName(newValue , oldValue){
                console.log("firstName改变" , newValue , oldValue);
                this.fullName = this.firstName + " " + newValue;
            }
             */
            firstName:{
                // 若immediate为true，handler在初始化时立即执行一次
                immediate:true,
                handler(newValue,oldValue){
                    this.fullName = newValue + " " + this.lastName;
                }
            }
        }
    });
</script>
```

## 绑定样式

```html
<style>
        .violet{
            border: 3px solid black;
        }
        .classA{
            background-color: coral;
        }
        .classB{
            color: firebrick;
        }
        .classC{
            text-shadow: 2px 2px 3px yellow;
        }

    </style>


<div id="root" class="violet" :class="myStyle">
    姓：  <input type="text" v-model="firstName">
    <br/>
    名:  <input type="text" v-model="lastName">
    <br>
    <span>全名: {{fullName}}</span><br/>
    全名：<input type="text" v-model="fullName">

</div>

<div id="root1" class="violet" :class={classA:hasA,classB:hasB}>
    姓：  <input type="text" v-model="firstName">
    <br/>
    名:  <input type="text" v-model="lastName">
    <br>
    <span>全名: {{fullName}}</span><br/>
    全名：<input type="text" v-model="fullName">

</div>

<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{
            firstName:"violet",
            lastName:"evergarden",
            fullName:"",
            myStyle:"classA"

        },
    });

    const root1 = new Vue({
        el:"#root1",
        data:{
            firstName:"violet",
            lastName:"evergarden",
            fullName:"",
            hasA:true,
            hasB:false
        },
    });
</script>

```

* 动态绑定样式

  ```html
  <div id="root" :style={fontSize:size+"px"}>
      姓：  <input type="text" v-model="firstName">
      <br/>
      名:  <input type="text" v-model="lastName">
      <br>
      <span>全名: {{fullName}}</span><br/>
      全名：<input type="text" v-model="fullName">
  
  </div>
  
  
  <script type="text/javascript">
      const root = new Vue({
          el:"#root",
          data:{
              firstName:"violet",
              lastName:"evergarden",
              fullName:"",
              size:20
          },
      });
  </script>
  ```

  

##  条件渲染

```html
<div id="root">
 <h1>今天吃{{eatCh?"鸡肉":"猪肉"}}</h1>
    <button @click="eatCh = !eatCh">换一样</button>

    <div v-show="eatCh">
        <img src="https://exp-picture.cdn.bcebos.com/f385f29959430401bfe084f1d66b04d1482905b6.jpg?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_500%2Climit_1%2Fquality%2Cq_80" alt="">
        <span>鸡肉好吃</span>
    </div>

    <div v-show="!eatCh">
        <img src="https://inews.gtimg.com/newsapp_bt/0/12114325175/1000" alt="">
        <span>猪肉好吃</span>

    </div>

</div>

<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{
            eatCh:true
        },
    });
</script>
```

##  基本列表渲染

```html
<div id="root">
    <h2>人员名单</h2>
    <ul>
        <li v-for="item in person" >{{item.id + "--" + item.name +"--"+item.age+"--"+item.sex}}</li>

    </ul>
</div>

<script type="text/javascript">

    const root = new Vue({
        el:"#root",
        data:{
            person:[
                {id:"1",name:"apple",age:"12",sex:"man"},
                {id:"2",name:"banana",age:"31",sex:"female"},
                {id:"3",name:"orange",age:"42",sex:"man"},
                {id:"4",name:"pen",age:"43",sex:"female"}
            ]
        }
    });
</script>
```

* 列表过滤

  ```html
  <div id="root">
      <h2>人员名单</h2>
      <input type="text" placeholder="姓名" v-model="keyWord">
      <ul style="font-size: 45px">
          <li v-for="item in nameValue" >{{item.name +"--"+item.age +"--" +item.sex}}</li>
  
      </ul>
  </div>
  
  <script type="text/javascript">
  
      const root = new Vue({
          el:"#root",
          data:{
              person:[
                  {id:"1",name:"马冬梅",age:"12",sex:"man"},
                  {id:"2",name:"周冬雨",age:"31",sex:"female"},
                  {id:"3",name:"周杰伦",age:"42",sex:"man"},
                  {id:"4",name:"温兆伦",age:"43",sex:"female"}
              ],
              keyWord:""
          },
          computed:{
              nameValue(){
                  const {person,keyWord} = this
                  return person.filter(p => p.name.indexOf(keyWord) !== -1);
              }
          }
      });
  </script>
  ```

  

* 列表排序

  ```html
  <div id="root">
      <h2>人员名单</h2>
      <input type="text" placeholder="姓名" v-model="keyWord">
      <button @click="sortKey = 1">年龄升序</button>
      <button @click="sortKey = 2">年龄降序</button>
      <button @click="sortKey = 0">源顺序</button>
      <ul style="font-size: 45px">
          <li v-for="item in nameValue" >{{item.name +"--"+item.age +"--" +item.sex}}</li>
  
      </ul>
  </div>
  
  <script type="text/javascript">
  
      const root = new Vue({
          el:"#root",
          data:{
              sortKey:0,
              person:[
                  {id:"1",name:"马冬梅",age:"12",sex:"man"},
                  {id:"2",name:"周冬雨",age:"31",sex:"female"},
                  {id:"3",name:"周杰伦",age:"42",sex:"man"},
                  {id:"4",name:"温兆伦",age:"43",sex:"female"}
              ],
              keyWord:"",
          },
          computed:{
              nameValue(){
                  const {person,keyWord,sortKey} = this
                  const arr = person.filter(p => p.name.indexOf(keyWord) !== -1);
                  if(sortKey){
                      arr.sort((a,b)=>{
                          if(sortKey === 1) return a.age-b.age;
                          else return b.age-a.age;
                      })
                  }
                  return arr;
              }
          },
      });
  </script>
  ```

  
## 收集表单

```html
<div id="root">
    <form @submit.prevent @click="sub">
        账号：<input type="text" v-model="userinfo.account"><br/><br/>
        密码：<input type="text" v-model="userinfo.password"><br/><br/>
        性别: 男<input type="radio" name="sex" v-model="userinfo.sex" value="male">
             女<input type="radio" name="sex" v-model="userinfo.sex" value="female"><br/><br/>
        爱好：apple<input type="checkbox" v-model="userinfo.hobby" value="apple">
             banana<input type="checkbox" v-model="userinfo.hobby" value="banana">
             orange<input type="checkbox" v-model="userinfo.hobby" value="orange"><br/><br/>
        所属校区：<select v-model="userinfo.add" >
        <option>请选择校区</option>
        <option value="bs" >宝山</option>
        <option value="jd">嘉定</option>
        <option value="yc">延长</option>
    </select><br/><br/>
        其他信息：<textarea cols="30" rows="10" v-model="userinfo.other"></textarea><br/><br/>
        <input type="checkbox" v-model="userinfo.agree">阅读并接受<a href="http://www.xzalab.top:4000" >《xxxx协议》</a>
        <br/><br/>
        <button >提交</button>
    </form>
</div>

<script type="text/javascript">
    const root = new Vue({
        el:"#root",
        data:{
            userinfo:{
                account: "",
                password: "",
                sex: "",
                hobby: [],
                add: "",
                other: "",
                agree: false
            }
        },
        methods:{
            sub(){
                console.log(this.userinfo)
            }
        }
    })
</script>
```

##  生命周期

* 简单的生命周期

  ```html
  <div id="root">
      <h2 :style="{opacity:opac}">生命的终止</h2>
      <button @click="death">死亡</button>
  </div>
  
  <script type="text/javascript">
      const root = new Vue({
          el:"#root",
          data:{
              opac:1,
          },
          methods:{
              death(){
                  this.$destroy()
              }
          },
          mounted() {
              this.timer = setInterval(()=>{
                  console.log("回调中")
                  if (this.opac<=0) this.opac=1;
                  this.opac -=0.01
  
              },16);
          },
  
          // vm被destroy之前
          beforeDestroy() {
              clearInterval(this.timer)
          }
      })
  </script>
  ```

Vue的生命周期示意图  

<img src="https://cn.vuejs.org/images/lifecycle.png" style="zoom:50%;" />

##  过渡和动画

```html
 <style>
        .pic{
           zoom: 50%;
        }
        /* 离开的起点 */
        .erciyuan-leave,.erciyuan-enter-to{
            opacity: 1;
            transform: scale(1) rotate(0deg);
        }
        /*离开的终点*/
        .erciyuan-leave-to,.erciyuan-enter{
            opacity: 0;
            transform: scale(0.0) rotate(360deg);
        }
        /*离开的过程*/
        .erciyuan-leave-active,.erciyuan-enter-active{
            transition: 4s all linear;
        }
        /**/

    </style>
</head>
<body>
<div id="root">
    <button @click="isShow = !isShow">切换</button><br/><br/>
    <button @click="setMask">切换</button><br/><br/>
    <transition name="erciyuan">
        <img  src="https://pic2.zhimg.com/v2-e9f4b89a0744613daa31e77f1be19f81_1440w.jpg?source=172ae18b" class="pic"  v-show="isShow">
    </transition>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el:"#root",
        data:{
            isShow:true
        },
        methods:{
            setMask(){//设置遮罩层
                const mask = document.createElement('div');
                mask.id="mask"
                mask.style.width = window.innerWidth + 'px';
                mask.style.height = window.innerHeight + 'px';
                mask.style.background= "#282C34"
                mask.style.opacity = '1';
                mask.style.position = 'fixed';
                mask.style.top = '0';
                mask.style.left = '0';
                mask.style.zIndex = 10086;
                document.body.appendChild(mask);
                setTimeout(()=>{document.body.removeChild( document.getElementById("mask") )}, 3000 )
            }
        }
    })
</script>
```



##  全局过滤器

```html
<div id="root">
    <h2>显示格式化后的时间</h2>
    <h3>{{fmtDate()}}</h3>
    <h3>{{fmtDate("YYYY-MM-DD HH:mm:ss")}}</h3>
    <h3>{{time | dateFormater}}</h3>
</div>

<script type="text/javascript">
    
    // 全局过滤器(好像要放在Vue实例上面）
    Vue.filter('dateFormater', function (value , str = "YYYY-MM-DD HH:mm:ss"){
        return  moment(value).format(str);
    })
    
    const vm = new Vue({
        el:"#root",
        data:{
            time:Date.now(),
        },
        methods:{
          fmtDate(str = "YYY-MM-DD"){
              return moment(this.time).format(str);
          }
        }
    })
</script>
```

##  常用指令

<img src=".\img\image-20210630232934212.png" alt="image-20210630232934212" style="zoom:67%;" />

##  自定义指令

```html
<div id = "root">
    <h2 v-upper-text="name"></h2>
    <h2 v-lower-text="name"></h2>
</div>

<script type="text/javascript">
    // 定义一个全局指令
    Vue.directive("upper-text",function (elem,binding){
        elem.innerText += binding.value.toUpperCase()
    })

    const vm = new Vue({
        el:"#root",
        data:{
            name:"Violet"
        },
        directives:{
            "lower-text":function(elem,binding){
                elem.innerText += binding.value.toLowerCase()
            }
        }
    })
</script>
```

##  插件

```js
// 插件是包含install方法的对象
// plug.js

const plug = {}

plug.install = function (Vue, options){
    // 定义一个全局指令
    Vue.directive("upper-text",function (elem,binding){
        elem.innerText += binding.value.toUpperCase()
    })

    // 给Vue添加属性
    Vue.projectName="XXXXX"

    // 给Vue原型对象上添加数据，供Vue使用
    Vue.prototype.$random = function (){
        return xxxx
    }
}
```

在html文件中引入：

```html
<script src="plug.js"></script>

<div id = "root">
    <h2>{{$now}}</h2>
</div>

<script type="text/javascript">
	// 插件使用
    Vue.use(plug)
    const vm = new Vue({
        el:"#root",
        data:{ },

    })
</script>

```



##  组件

*  单文件组件

  >一个文件就是一个组件，而且后缀为`.Vue`

* 非单文件组件

  > 一个文件中定义所有组件，而且后缀不为`.Vue`

1. 非单文件组件

   ```html
   <body>
       <div id="root">
           <School></School>
       </div>
   
       <div id="root1">
           <School></School>
       </div>
   
   <script type="text/javascript" >
       /*
        *  定义School组件:
        *      1. 使用Vue.extend(options)创建
        *      2. School本质是一个构造函数
        *      3. 不写el
        *      4. data要写成函数
        *      5. 在组件的模板结构要配置在template属性中，值为html字符串，而且要用模板字符串
        */
       const School = Vue.extend({
           data(){
               return {
                   school: "shanghai university",
                   address: "shanghai baoshan",
               }
           },
           template:`
           <div>
             <h2>学校名:{{school}}</h2>
             <h2>地址：{{address}}</h2>
           </div>`,
       })
   
       /*
       *  注册组件
       */
       Vue.component("School",School)
   
       /*
        * 定义一个vm管理所有组件
        */
   
       const vm = new Vue({
           el:"#root",
       })
   
       /*
        * 局部注册
        */
       const vm1 = new Vue({
           el:"#root1",
           components:{
               School
           }
       })
   </script>
   ```

   *  单文件组件 && 脚手架

     ![image-20210701171638232](.\img\image-20210701171638232.png)

##  refs $$ props

* __ref__用于组件内通信
* __props__用于父子组件间通信(父组件向子组件发送数据)

``` vue
/*
 * App.Vue
 * 父组件
 */
<template>
  <div>
  <School :username="username"></School>
    <input type="text" ref="password">
    <button type="button" @click="sub">提交</button>
  </div>
</template>

<script>
import School from "./components/School";

export default {
  name: "App",
  components:{
    School
  },
  data(){
    return{
      username:"violet"
    }
  },
  methods:{
    sub(){
      console.log(this.$refs.password.value)
      this.username = this.$refs.password.value
    }
  }
}
</script>

```

```Vue
/* School
 * 子组件
 * 配置模板结构
 */
<template>
    <div >
      <h2 class="title">{{school}}</h2>
      <h2 @click="show" class="info">{{address}}</h2>
      <h2>{{username}}</h2>
    </div>
</template>

<script>

// 此处为组件配置
export default {
  name: "School",
  data(){
    // console.log(this)
    return {
      school:"shanghai university",
      address:"shanghai baoshan"
    }
  },
  methods:{
    show(){
      console.log(this.address)
    }
  },
  props:{
    username:{
      type:String,
      required:true,
      default:"evergarden"
    }
  }
}

</script>


// 配置组件样式
<style scoped>
.title{
  color: violet;
}
.info{
  background-color: firebrick;
}
</style>
```

```js
/*
 * main.js
 */

import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')

```

