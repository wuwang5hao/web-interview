store目录结构
```js
-store
 --actions   
 --mutations
 --getters
 --mutations-type
 --index.js // 总入口
 ```
 通过vuex，不用考虑组件之间的传值，直接通过$store获取，写法：this.$store.state.xxx.xxx
 props，methods,data和computed的初始化都是在beforeCreated和created之间完成的。
 如果需要vuex多个数据，用mapState方法，写在computed中

 # mapState 辅助函数
 可以自动将需要的state值映射为实例的计算属性
 注意：方法名和获取的属性名一致
```js
  computed: {
    ...mapState([
      'userInfo', 
      'modalArr'
    ])
  },

// 等价于
userInfo(){
  return this.$store.state.userInfo
},

modalArr(){
 return this.$store.state.modalArr
}，
```

# getters
相当于vue的计算属性，能拿到state里的最新值
getter允许传参，第一个参数是state
```js
// getter.js
export default {
    realName:(state)=> {
        return state.userInfo.userName+'888'
    }
}
// 实例中使用
computed：{
    realName: this.$store.getters.realName,
}

```

# mapGetters 辅助函数
和mapState，用法一致

# mapState和getters区别
getters可以传参，可以针对不同业务需求
如果不需要派生新的值，this.$store.getters.值  就等于 this.$store.state.值

# mutation
类似vue中的methods
2个参数：第一个参数是state，第二个参数是payLoad
```js
methods:{
// 实例部分
 <button @click="handleAddAge">增加一岁</button>
 handleAddAge(){
 //使用mutation用commit关键字来提交mutation
 //第二个参数最好写成对象形式，可以传递更多信息
   this.$store.commit('addAge',{
     number:1
   })
 },
}

// mutations.js
export default {
    addAge:(state,payLoad)=>{
     state.userInfo.age+=payLoad.number
   },
}

```
注意：mutations只能写同步方法，不能写异步，比如axios、setTimeout等，这些都不能写，mutations的主要作用就是为了修改state的。
原因类似：如果在mutations中写异步，也能够调成功，但是由于是异步的，不能被调试工具追踪到，所有不推荐这样写，不利于调试,这是官方的约定。
作用；
- 进行赋值操作
- 方便用调试工具调试，直接改变state中的属性，则无法实时监测

# mapMutations
跟mapState、mapGetters一样
不同的是，mapMutations是将所有mutations里面的方法映射为实例methods里面的方法
```js
// 实例
 <button @click="addAge">增加一岁</button>
 <button @click="AddAge({number:1})">增加一岁</button> // 如果传参
methods:{
 ...mapMutations(['addAge'])
}
 
//相当于
methods:{
 addAge(payLoad){
 
  this.$store.commit('addAge',payLoad)
 }
}

```

# actions
类似于mutation
- action可以提交mutation,然后mutation去更改state
- action不要直接去操作state，而是去操作mutation
- action包含异步操作，类似于axios请求，可以都放在action中写
- action中的方法默认的就是异步，并且返回promise
```js
// vue实例
  <div>{{realName}}</div>
export default{
    computed(){
    //第三步 拿到最新的userInfo
    realName:this.$store.getters.realName
},
created(){ 
    //第一步
    this.reqUserInfo()
},
methods:{
    reqUserInfo(){
       //第二步使用action使用dispatch关键字来派发action
        this.$store.dispatch('getUserInfo')
    }
}
}

// actions.js
import {
    SET_USER_INFO,
} from "./mutation_type.js"

export default{
    //定义一个异步获取用户信息的action
    async getUserInfo(context){
       //context可以理解为它是整个Store的对象.类似于this.$store，里面包含了state，getter，mutations，actions
       const res = await axios.get('/接口url')
       //用到了 mutation_type.js
       context.commit( SET_USER_INFO,{userInfo:res.userData}
       )
    },


    // 解构
    async getUserInfo:({commit,dispatch})=>{
         //1.从接口里异步获取数据
       const res = await axios.get('/接口url')
       //2.将获取到的数据，通过commit提交一个mutation，去更改state里面的userInfo
       commit( SET_USER_INFO, {userInfo:res.userData})
    }
}
```
界面——>派发action——>action提交mutation——>mutation更改state——>getters返回最新的state值到界面

# mapActions
```js
methods:{
    ...mapActions(['getUserInfo','getToken'])
}

// 相当于
methods:{
    getUserInfo(){
        return this.$store.dispatch(‘getUserInfo’)
    },
    getToken(){
        return this.$store.dispatch(‘getToken’)
    },
}
```

## 在action里派发其他action
```js
 async getUserInfo(context){
       const res = await axios.get('/接口url')
       context.commit( SET_USER_INFO,{userInfo:res.userData})
       
       //此处派发另外一个action
       context.dispatch('getToken') 
    },
```

# 总结
- 依赖state得到新的数据，用this.$store.getters.值
- 直接获取state的数据，用this.$store.state.值
- 同步修改state的属性值，就用this.$store.commit('mutation值')
- 异步修改state的属性值，就用this.$store.dispatch('action值')
- mapState、mapMutations、mapGetters、mapActions等辅助函数，便于我们处理多个方法和属性


