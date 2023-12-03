# ARouter 动态注册路由

在实际项目中，可能登录界面需要实现多种不同样式，针对不同客户或应用场景切换不同界面。

可以通过 ARouter 提供的接口实现动态注册。

```kotlin
class LoginRouteGroupImpl : IRouteGroup {

    override fun loadInto(atlas: MutableMap<String, RouteMeta>) {
        if (PackageUtils.isPackageCus()) {
            atlas[RouterActivityPath.User.PAGER_LOGIN] = getCustomRouteMeta()
        } else {
            atlas[RouterActivityPath.User.PAGER_LOGIN] = getStandardRouteMeta()
        }
    }

    private fun getCustomRouteMeta(): RouteMeta {
        return RouteMeta.build(
            RouteType.ACTIVITY,         // Route type
            BTLoginActivity::class.java,  // Target class
            RouterActivityPath.User.PAGER_LOGIN,      // Path
            "user",                  // Group
            0,                          // not need
            0                           // Extra tag, Used to mark page feature
        )
    }

    private fun getStandardRouteMeta(): RouteMeta {
        return RouteMeta.build(
            RouteType.ACTIVITY,         // Route type
            LoginActivity::class.java,  // Target class
            RouterActivityPath.User.PAGER_LOGIN,        // Path
            "user",                  // Group
            0,                          // not need
            0                           // Extra tag, Used to mark page feature
        )
    }
}

ARouter.getInstance().addRouteGroup(LoginRouteGroupImpl())
```

后续目标页面和服务不需要使用 `@Route` 注解进行标记，同一批只能注册同一组的路由信息，即如果还有注册页面也需要动态注册路由，必须重新实现一个 `IRouteGroup`。