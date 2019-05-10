# page页面的拓展
* ## 实现思路,重写page页面的部分方法以实现自定义配置
* ## 知识来源于[辉锅](https://github.com/guohui666)
* ## 代码如下,主要重写了onload事件
```
import regeneratorRuntime from './baseCode/regenerator-runtime/runtime'

const oldPage = Page

Page = function (options) {
    const onLoad = options.onLoad
    let app = getApp()
    // 为onLoad添加一些自定义通用方法，其他生命周期同理
    options.onLoad = async function (e) {
        await app.loginAuth;
		//绑定全局的invoke方法
        this.invoke = (target) => {
            console.info('invoke')
            if (target && target.currentTarget.dataset.method && this.methods[target.currentTarget.dataset.method])
                this.methods[target.currentTarget.dataset.method](target)
        }
        const userInfo = app.globalData.userInfo    // 用户信息
        const GOptions = e || {} // onLoad中options参数
        this.setData({GOptions, userInfo})
        // 为当前实例添加一个上一页实例对象
        const _pages = getCurrentPages()
        if (_pages.length > 1) {
            this._previousPage = _pages[_pages.length - 2]
        } else {
            this._previousPage = this
        }
        // 执行页面中的onLoad
        onLoad && onLoad.call(this, e)
        this.methods && this.methods.initUI && this.methods.initUI(e)
    }
    // 传入自定义后的onLoad
    return oldPage(options)
}
```