# hash与history模式区别
- url地址不同，hash有#号，history没有#号
- hash兼容性较好，history的兼容性相比hash要差
- 关于分享页面到app里，hash模式若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。history模式应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题，可以让后端配置一下apache或是nginx的url重定向，重定向到你的首页路由上就ok了



# 对前端路由的理解
- 传统的路由指的是：当用户访问一个url时，对应的服务器会接收这个请求，然后解析url中的路径，从而执行对应的处理逻辑。这样就完成了一次路由分发
- 前端路由是不涉及服务器的，是前端利用hash或者HTML5的history API来实现的，一般用于不同内容的展示和切换
