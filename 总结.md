### -*1.**js事件循环机制（eventLoop）**

js是一门单线程的脚本语言，js的任务分为同步任务和异步任务，同步任务进入主线程（主执行栈）执行，异步任务进入任务队列（先进先出)，主线程任务执行完，会去任务队列中读取对应任务，推入主线程执行，这个过程不断重复，构成事件循环（Event Loop）

![image](C:\Users\111\Desktop\image.png)

异步任务分为宏任务与微任务，在事件循环会进行多轮，在事件循环中，每进行一次循环操作称为tick，每一次 tick 的任务键的步骤可以总结如下：1.执行一个宏任务（栈中没有就从事件队列中获取）；2.执行过程中如果遇到微任务，就将它添加到微任务的任务队列中；3.宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）；4.当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染；5.渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

![image1](C:\Users\111\Desktop\image1.png)

宏任务主要包含：script( 整体代码)、setTimeout、setInterval、I/O、UI 交互事件、setImmediate(Node.js 环境)，ajax
微任务主要包含：Promise、MutaionObserver、process.nextTick(Node.js 环境) 

扩展：浏览器的渲染进程是多线程的，浏览器每一个tab标签都代表一个独立的进程，其包含的线程有以下几种

GUI 渲染线程：负责渲染页面，解析 HTML，CSS 构成 DOM 树；

JS 引擎线程：解释执行代码、用户输入和网络请求；

事件处理线程：click、mouse等交互事件发生后将事件函数放入队列；

定时器触发线程：等时间结束后执行函数推入任务队列中；

http网络请求线程：处理用户的get、post请求，返回结果推入任务队列中。

### 2.**js垃圾回收机制**

内存泄漏可能导致的后果：变慢，崩溃，延迟大

原因：

全局变量

dom 清空时，还存在引用

定时器未清除

标记清理：

当变量进入执行环境(声明变量）的时候，垃圾回收器将该变量进行了标记，当该变量离开环境的时候，将其再度标记，随之进行删除。它的机制就是跟踪某一个值得引用次数，当声明一个变量并且将一个引用类型赋值给变量得时候引用次数加1，当这个变量指向其他一个时引用次数减1，当为0时出发回收机制进行回收，循环引用会导致内存泄漏

引用计数：即该变量被引用的次数

策略思想：

1 跟踪记录每个变量值被使用的次数

2 当声明一个变量并且将一个引用类型数据赋值给这个变量的时候，这个引用类型数据的引用次数就标记为 1

3 如果当这个引用类型数据又赋值给另一个变量，那么引用次数就+1

4 如果变量被其他的值覆盖，则引用次数-1

5 当这个引用类型数据的引用次数变为0的时候,这个变量就没有被使用了，也无法访问，垃圾回收器就会在执行时，销毁引用次数为0的引用类型数据，回收其所占用的内存空间。

```typescript
 let a = {
	  name: "江流",
     age: 20
		};   //此时该对象的引用计数标记为1（a 引用）
	let b = a;	//此时对象的引用计数标记为2（a、b 引用）
	a = null;	//此时对象的引用计数标记为1(（b 引用）)
	b = null;	//此时对象的引用计数标记为0（无变量引用）
	... 		//等待GC 回收此对象
```

循环引用会导致内存泄漏

```typescript
function test()
{
  let A = new Object();
  let B = new Object();
  A.pointer= B;
  B.pointer = A;
}
test()
```

### 3.**闭包 的优点与缺点**

闭包通俗理解：

有权访问另一个函数作用域内变量的函数都是闭包

闭包的优点：

闭包内部可以访问到定义它们的外部函数的参数和变量（除了this和arguments）。

闭包的缺点：是它会保存包含它的函数的作用域，因此比一般函数占用更多的内存空间，因此不宜过度使用闭包

```typescript
function a(){
 var n = 0;
 this.inc = function () {
  n++; 
  console.log(n);
 };
}
var c = new a();
c.inc();  //控制台输出1
c.inc();  //控制台输出2
```

 

### 4.**怎样改变this 指向，不同点在哪里**

![image2](C:\Users\111\Desktop\image2.png)

### 5.**typescript数据类型**

1.  boolean（布尔类型）

2. number（数字类型）

3. string（字符串类型）

4. array（数组类型）

5.  object（对象类型）

6. null和undefined类型

7. tuple（元组类型）

   let tupleArr:[number,string,boolean];

   tupleArr = [12,'34',true]; // yes

   tupleArr = [12,'34']; // no

8. enum（枚举类型）

   enum Color {Red,Green,Blue}

   let c:Color = Color.Green;

14.  any（任意类型）

15. void类型

16.  never类型

 

### 6. **webpack打包机制**

1. 初始化参数：从配置文件读取与合并参数，得出最终的参数
2. 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，开始执行编译
3. 确定入口：根据配置中的 entry 找出所有的入口文件
4. 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模5块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
5. 完成模块编译：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

### 7.**js是没有文件读写能力的，怎样要做到文件的读和写**

引入node 的fs模块

###  8.**vue3的生命周期**

生命周期基础

![image3](C:\Users\111\Desktop\image3.png)

vue3也提供了两种不同的生命周期表现形式1.Composition API（组件式api）形式的生命周期钩子，与vue2.x中钩子对应关系如下,

组件式api:

```typescript
<script setup>
import { ref, onMounted } from 'vue'
const el = ref()
onMounted(() => {
 el.value // <div>
})

</script>
```

1. ```typescript
   . beforeCreate =》setup() : 开始创建组件之前，在beforeCreate和created之前执行，创建的是data和method。
   2. beforeMount=》onBeforeMount() : 组件挂载到节点上之前执行的函数。
   3. mounted=》onMounted(): 组件挂载完成之后执行的函数。
   4. beforeUpdate=>onBeforeUpdate(): 组件更新之前执行的函数。
   5. updated=>onUpdated()：组件更新完成之后执行的函数。
   6. beforeDestoryed=>omBeforeUnmount()： 组件卸载之前执行的函数。
   7. destoryed=>onUnmounted()： 组件卸载完成之后执行的函数。
   8. onActivated():keep-alive 被包含中的组件，会多出来两个生命周期钩子函数。被激活时执
   9. onDeactivated(): 比如从A组件，切换到B组件，A组件消失时执行。
   10. onErrorCaptured()：注册一个钩子，在捕获了后代组件传递的错误时调用
   ```

 选项式api与vue2的生命周期一致，不同的是beforeDestoryed更名为beforUnmount

destoryed更名为unMounted()

选项式api

```typescript
export default {
 data() {
  return { a: 1 }
 },
 created() {
  console.log(this.a) // 1
  console.log(this.$data) // { a: 1 }
 }

}
```



### 9.**ts场景题,后端的数据结构不完善，那么前端在定义数据时怎样进行定义**

 

### 10.**解决地狱回调**

地狱回调

为什么会产生地狱回调：

js分为同步任务和异步任务，其中异步任务是没有办法保证执行顺序的，比如ajax请求，有可能在后面的请求先执行。如果我们想让异步任务向同步任务一样按顺序执行，就会产生嵌套的情况，一层一层嵌套，一直嵌套到地狱

```typescript
     setTimeout(function () {  //第一层
       console.log('武林要以和为贵 ');
       setTimeout(function () {  //第二程
         console.log('要讲武德');
         setTimeout(function () {  //第三层
           console.log(' 不要搞窝里斗');
         }, 1000)
       }, 2000)
     }, 3000)

```

/*
依次输出：武林要以和为贵 要讲武德 不要搞窝里斗
*/

这样的代码可读性差，而且也不容易维护，那么如何解决这个问题？

```typescript
   function fn(str){
     var p=new Promise(function(resolve,reject){
       //处理异步任务
       var flag=true;
       setTimeout(function(){
         if(flag){
           resolve(str)
         }
         else{
           reject('操作失败')
         }
       })
     })
     return p;
   }
​
   fn('武林要以和为贵')
   .then((data)=>{
     console.log(data);
     return fn('要讲武德');
   })
   .then((data)=>{
     console.log(data);
     return fn('不要搞窝里斗')
   })
   .then((data)=>{
     console.log(data);
   })
   .catch((data)=>{
     console.log(data);
   })
```

现在看起来是不是好很多，但是Promise最大的问题就是代码冗余，原来的异步任务被Promise封装一下，不管什么操作都用than，就会导致一眼看过去全是then…then…then…,这样也是不利于代码维护的。使用es6 async await

```typescript
   //封装一个返回promise的异步任务
   function fn(str) {
     var p = new Promise(function (resolve, reject) {
       var flag = true;
       setTimeout(function () {
         if (flag) {
           resolve(str)
         } else {
           reject('处理失败')
         }
       })
     })
     return p;
   }
​
   //封装一个执行上述异步任务的async函数
   async function test(){
     var res1=await fn('武林要以和为贵');  //await直接拿到fn()返回的promise的数据，并且赋值给res
     var res2=await fn('要讲武德');
     var res3=await fn('不要搞窝里斗');
     console.log(res1,res2,res3);
   }
   //执行函数
   test();
```

总结：在一些场景中，我们想让异步代码跟同步代码一样，按顺序执行，这个时候就会产生地狱回调的问题，解决地狱回调使用到了es6的promise方法，但是promise可读性不太好，升级为async await语法糖

 

### 11.**a b c 三个线程，执行完之后才能执行d**

同步不存在这个问题，必然是异步函数，解决异步问题的执行顺序问题，使用promise

### 12.**定时器和promise有一道宏任务与微任务的题**

 

### 13.**代码提交规范**

1.  配置

   ```typescript
   # 切换到 develop 分支
   git checkout develop
   # 拉取最新代码
   git pull
   # 切换到自己分支
   git checkout feature/user
   # 拉取最新代码（如果之前在别的电脑提过代码，如在家办公）
   git pull
   # 合并 develop 分支
   git merge develop
   # 推送到中央库
   git push
   
   ```

   

2. git提交

   1. 开发前**先合并 develop 的最新代码**！

      ```typescript
      # 切换到 develop 分支
      git checkout develop
      # 拉取最新代码
      git pull
      # 切换到自己分支
      git checkout feature/user
      # 拉取最新代码（如果之前在别的电脑提过代码，如在家办公）
      git pull
      # 合并 develop 分支
      git merge develop
      # 推送到中央库
      git push
      
      ```

      

   2. 在本地开发这个小任务

   3. 开发完成后，**保证在本地运行不报错之后！**再提交本次代码到中央库

      ```typescript
      git add .
      # 提交 git，-s 表示加上签名，-m 表示加提交记录，每次提交必须说明本次提交的内容
      git commit -m ":sparkles: 【功能】权限-组织机构-机构列表：mock 列表展示（未与后端联调）" -s
      # 推送到中央库
      git push
      
      ```

      常见的提交记录样例：

      - 功能开发：`git commit -m ":sparkles: 【功能】模块名：内容" -s`
      - Bug 修复：`git commit -m ":bug: 【Bug】模块名：内容" -s`
      - 代码重构：`git commit -m ":recycle: 【重构】模块名：内容" -s`

      上边的“模块名”和“内容”根据项目具体情况进行替换

      `:sparkles:` 是 Git 表情图标，详见：[gitmoji](https://gitmoji.dev/)

   4. 创建合并请求，详见[创建合并请求](https://document.riemann.tech/specification/common/pull-request.html)

   5. 在开发下一个小任务前，**重复步骤 1，即重新合并一下最新的 develop 代码！**

   

### 14.**eslint**

 规则太多，去官网[查看](https://cn.eslint.org/docs/rules/)

### 15.**介绍项目**

 

### 16.**项目部署流程**

 

### 17.**es6新增哪些属性**

1.  let const

2. 变量的解构赋值

3. 字符串-模板字符串

4. proxy

5. promise

6. async await

7. class

8. set 和map数据结构

   set:类似数组，成员都是唯一(去重可用到)

   ```javascript
   const s = new Set();
   
   [2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
   
   for (let i of s) {
     console.log(i); // 2 3 5 4
   }
   ```

   去重：

   ```javascript
   // 去除数组的重复成员
   [...new Set(array)]
   // 去除数组的字符串成员
   [...new Set('ababbc')].join('')
   // "abc"
   ```

### 18.**promise的状态**,promise.all,promise.race

 pending reject resolve



### **19.vue3的优点 **

1. vue3支持vue2的大多数特性，实现对vue2的兼容
2. vue3对比vue2具有明显的性能提升
   1. 打包大小减少41%
   2. 初次渲染快55%，更新快133%
   3. 内存使用减少54%
3. vue3具有的composition API实现逻辑模块化和重用
4. 增加了新特性，如Teleport组件，全局API的修改和优化等

### 20.**怎样在typescript中定义对象**

 

### 21.**箭头函数注意事项**

 1、箭头函数的this是静态的   this始终指向的是函数声明时所在的作用域下的this的值

2、不能作为构造函数实例化对象

3、不能够使用arguments变量

4、箭头函数的简写   (省略小括号【当只有一个参数的时候】  省略花括号【当只有一条执行语句的时候】)

###  22.**生产环境去解决跨域的问题**

 

### 23.**小程序的生命周期，新代码发布完之后怎样让用户立刻能感知到**

```
 小程序的生命周期分为页面的生命周期和整个应用的生命周期。

应用的生命周期主要有onLaunch、onShow、onHide

onLaunch 是当小程序初始化完成时，会触发 onLaunch（全局只触发一次）；

onShow 是当小程序启动，或从后台进入前台显示，会触发 onShow；

onHide 是当小程序从前台进入后台，会触发 onHide；

页面的生命周期会比较多一些，有onLoad、onReady、onShow、onHide、onUnload

onLoad 是监听页面加载的函数

onReady 是监听页面初次渲染完成的函数

onShow 是监听页面显示的函数

onHide 是监听页面隐藏的函数

onUnload 是监听页面卸载的函数
```

 * ```typescript
 /**
    
   * 检测当前的小程序是否是最新版本，判断是否需要下载、更新当前小程序
     */
       function checkUpdateVersion() {
         //判断小程序版本是否可以使用小程序更新机制API
         if (wx.canIUse('getUpdateManager')) {
       //创建 UpdateManager 实例
       const updateManager = wx.getUpdateManager();
       //检测版本更新
       updateManager.onCheckForUpdate(function(res) {
         // 请求完新版本信息的回调
         if (res.hasUpdate) {
           //监听小程序有版本更新事件
           updateManager.onUpdateReady(function() {
             //调用 applyUpdate 应用新版本并重启 （ 此处进行了自动更新操作）
             updateManager.applyUpdate();
           })
           updateManager.onUpdateFailed(function() {
             // 新版本下载失败
             wx.showModal({
               title: '已经有新版本喽~',
               content: '请您删除当前小程序，到微信 “发现-小程序” 页，重新搜索打开哦~',
             })
         })
         }
       })
         } else {
       wx.showModal({
       title: '溫馨提示',
      content: '当前微信版本过低，无法使用该功能，请升级到最新微信版本后重试。'
    })
      }
    }
 ```
 
 

### 24.**http协议状态码**

 ```
200 : 成功，表示访问成功，正常状态。

301 : 永久移动，表示本网页已经永久性的移动到一个新的地址，在客户端自动将请求地址改为服务器返回的新地址。

302 : 临时重定向，表示网页暂时性的转移到一的新的地址，客户端在以后可以继续向本地址发起请求。

303 : 表示必须临时重定向，并且必须使用GET方式请求。

304 : 重定向至浏览器本身，当浏览器多次发起同一请求，且内容未更改时，使用浏览器缓存，这样可以减少网络开销。

401 : 表示用户没有访问权限，需要进行身份认证。

403 : 服务器拒绝访问请求。

404 : 这是最常见的错误，表示找不到系统资源，但是只是暂时性地。

500 : 表示服务器程序错误，一个通用的错误信息。

503 : 表示服务器繁忙，或者服务器负载，通常这只是一个临时状态。
> ```
> 
>  
> 
> ### 25. **对vue的优化**
> 
> 去查看
> 
> ### 26.**使用canvals实现一个正方形，具体的api**
> 
> ```javascript
> context.moveTo(60, 60);
> context.lineTo(60, 260);
> context.lineTo(260, 260);
> context.lineTo(260, 60);
> context.lineTo(60, 60);
> context.stroke();
> ```
>



### 27. **对vue打包后静态资源的管理**(问题含糊)

 

### 28.**使用flex实现一个滚动的效果**

 去查看

### 29.**typescript面试题:给一个函数传入参数，可能是string array number，1要求原样返回输出2要求length属性不能报错**

 

### 30.**js数据结构有哪些**

 数组、列表、栈、队列、链表、字典、集合

数据结构教程：[去查看](https://xpoet.cn/2020/07/JavaScript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%EF%BC%88%E4%B8%93%E8%BE%91%EF%BC%89/)

### 31.**js数据结构排序**

 js五种排序：[去查看](https://blog.csdn.net/weixin_46224014/article/details/121231814?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166303862416782417071943%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166303862416782417071943&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-121231814-null-null.142^v47^new_blog_pos_by_title,201^v3^control_2&utm_term=js%E6%8E%92%E5%BA%8F&spm=1018.2226.3001.4187)

### 32.**你遇到最难的问题是什么，你怎么解决的**

 

### 33.**ts泛型继承**

 

### 34.**职业发展规划**

###  **比刚入行进步的地方**

 

### 35.**v-for循环key值理解**

 vue官网对于key的解释：[去查看](https://cn.vuejs.org/api/built-in-special-attributes.html#key)

### 36.**对react了解多少**

 

### 37.**如何将类数组转为真正的数组**

 一共有五种方法：

```javascript
1、使用拓展运算符（ES6）
let arr = [...obj];
2、Array.from(arr)（ES6）
let arr = Array.from(obj);
3、call、apply
//apply
let arr = [];
arr.push.apply(arr,obj);
let arr = [].slice.apply(obj)
//call
let arr = [].slice.call(obj);
let arr = Array.protype.slice.call(obj);
4、for 循环
let arr = [];
for(let item of obj){
	arr.push(item);
}
```

产生原因：

```javascript
querySelectorAll，getElementByclassName，getElementsByTagName，arguments的集合
```

相同点与不同点

数组的类型是 Array ，类数组的类似是 Object
类数组不具备数组所具备的所有方法
类数组只能用 for 循环

类数组实例

```javascript
//下来这个就是一个类数组
obj = {
	0:'1',
	1:'2',
	length:2
}
```

### 38.**固定定位**

 

### 39.**怎样让几十万条数据流畅加载**

 

### 40.**a[1,2,3]  b[4,3,2] 数组交集 并集 补集**

```javascript
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// （a 相对于 b 的）差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```

### 41.路由传参方式，各有什么不同点

  1、写法不一样：

```typescript
存：
// query:
this.$router.push({path:'',query:{}})     
// params:
this.$router.push({name:'',params:{}})
//子路由配置
{
  path: '/child/:id',
  component: Child
}
//父路由编程式传参(一般通过事件触发)
this.$router.push({
    path:'/child/${id}',
})

取:

 this.$route.query                    

 this.$route.params
```

2、用query在刷新页面时 参数不会消失，但用params会，不过可以用本地存储解决

3、query传的参数显示在地址栏中，相当于get请求传参；params传的参数不在地址栏中，相当于post请求传参

### 42.手写JSON.stringify

[去查看](https://blog.csdn.net/xch____/article/details/123793724)

### 43.js数据类型常考面试题

[去查看](https://www.jb51.net/article/258082.htm#_label0)

### 44.页面会跟随弹框滑动

[去查看](https://blog.csdn.net/hahahhahahahha123456/article/details/107864001)

### 45.父子组件生命周期执行顺序

加载渲染过程

　　`父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted`

子组件更新过程

```
　　父beforeUpdate->子beforeUpdate->子updated->父updated
```

父组件更新过程

```
　　父beforeUpdate->父updated
```

销毁过程

```
　　父beforeDestroy->子beforeDestroy->子destroyed->父destroyed
```

### 46.实现图片懒加载

### 47.浅拷贝和深拷贝具体的定义

### 48.promise.all  promise.race promise

### 49.使用less实现点击更换所有颜色

### 50.css缩放  transform

### 51.vue解决跨域问题

### 52.函数传值，不确定有几个参数的情况下，怎么接收

### 53.css选中子级元素