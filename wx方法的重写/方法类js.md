# 重写的目的是方便阅读和使用代码
* ## 代码如下
```
export function showModal(title, content, success, fail, complete) {
    wx.showModal({
        title, content, success: res => {
            if (res.confirm === true) {
                if (success instanceof Function) success();
            } else if (fail instanceof Function) fail();
        }, complete
    })
}

export function selector(str) {
    console.info("select")
    let pageObj = getPage(0)
    let app = getApp();
    if (!app.globalData.selector || app.globalData.url !== (app.globalData.url = pageObj.route)) {
        console.info("flushSelector")
        app.globalData.selector = wx.createSelectorQuery();
    }
    return app.globalData.selector.select(str)
}

export function showToast(title, success, fail, complete, icon) {
    let image = undefined
    if (!(success instanceof Function)) {
        image = success
        success = undefined
    }
    if (title.length > 7)
        icon = 'none'
    wx.showToast({title, success, fail, complete, icon, image})
}

export function call(phoneNumber, success, fail, complete) {
    wx.makePhoneCall({phoneNumber, success, fail, complete})
}

export function forward(url, success, fail, complete) {
    wx.navigateTo({url, success, fail, complete})
}

export function backLastPage(delta, success, fail, complete) {
    wx.navigateBack({delta, success, fail, complete})
}

export function redirect(url, success, fail, complete) {
    wx.redirectTo({url, success, fail, complete})
}

export function relaunch(url, success, fail, complete) {
    wx.reLaunch({url, success, fail, complete})
}

export function getPage(lastPageSize) {
    let pages = getCurrentPages()
    return pages[lastPageSize = lastPageSize < pages.length ? pages.length - 1 - lastPageSize : 0]
}

export function showLoading(title, mask, success, fail, complete) {
    if (!(mask instanceof Boolean))
        mask = true
    wx.showLoading({title, mask, success, fail, complete})
}
```