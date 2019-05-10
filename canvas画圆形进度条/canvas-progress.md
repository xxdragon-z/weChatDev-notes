# canvas进度条实现
* ## wxml页面内容
```
 <view class="canvas_view" style="width:{{canvasWidth}}px;">
            <view class="big_circle"></view>
            <view class="little_circle"></view>
            <canvas canvas-id="runCanvas" id="runCanvas" class='canvas'>
            </canvas>
        </view>
```
* ###wxss内容
```
.canvas_view {
    height: 80%;
    /*border: 1px solid red;*/
    position: relative;
}

.title_padding_view {
    height: 4%;
}

.safety_score_tip {
    font-size: 22rpx;
    color: rgb(3, 162, 255);
}

.big_circle {
    width: 100%;
    height: 100%;
    /*border: 1px solid black;*/
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    border-radius: 50%;
    margin: auto auto;
    background-color: #ddd;
}

.little_circle {
    width: 80%;
    height: 80%;
    /*border: 1px solid green;*/
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    border-radius: 50%;
    margin: auto auto;
    background-color: #eee;
}

.canvas {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 99;
}
```
* ## js内容
```
import Canvas from './../../../utils/canvas'

let _this;
Page({
    ...Canvas.options,
    /**
     * 页面的初始数据
     */
    data: {
        ...Canvas.data
    },
 init(){
	let height = (sysInfo.windowHeight * 0.3) * 0.8;
            getApp().globalData.height = sysInfo.windowHeight
            _this.setData({canvasWidth: height})
	_this.draw('runCanvas', Math.floor(Math.random() * 101), 1000);
 }
```
* ## canvas组件js
```
export default {
    data: {
        percentage: '', //百分比
        animTime: '', // 动画执行时间
        ctx2Text: Object
    },
    options: {
        // 绘制圆形进度条方法
        run(c, w, h) {
            let that = this;
            // var num2 = (2 * Math.PI / 100 * c) - 0.5 * Math.PI;
            const num = (2 * c / 100) * Math.PI - 0.5 * Math.PI
            if (c === 1 || c % 15 === 0) {
                that.data.ctx2Text = that.formatText(c)
                that.setData({bgColor: this.data.ctx2Text.color})
            }
            that.data.ctx2.arc(w, w, 0.9 * w, -0.5 * Math.PI, num); //每个间隔绘制的弧度
            that.data.ctx2.setStrokeStyle(this.data.ctx2Text.color);
            that.data.ctx2.setLineWidth(0.2 * w);
            that.data.ctx2.setLineCap("butt");
            that.data.ctx2.stroke();
            that.data.ctx2.beginPath();
            that.data.ctx2.setFontSize(w / 2); //注意不要加引号
            that.data.ctx2.setFillStyle(this.data.ctx2Text.color);
            that.data.ctx2.setTextAlign("center");
            that.data.ctx2.setTextBaseline("middle");
            that.data.ctx2.fillText(c + "分", w, h);
            that.data.ctx2.setFontSize(w / 5);
            // that.data.ctx2.setFillStyle(obj.color)
            that.data.ctx2.fillText(this.data.ctx2Text.text, w, h + (w / 2));
            that.data.ctx2.draw();

        },
        formatText(score) {
            let obj = {color: '', text: ''}
            if (score < 40) {
                obj.text = '高风险'
                obj.color = 'red'
            } else if (score < 60) {
                obj.text = '较高风险'
                obj.color = 'red'
            } else if (score < 80) {
                obj.text = '存在风险'
                obj.color = 'orange'
            } else if (score < 95) {
                obj.text = '极低风险'
                obj.color = '#47e3fd'
            } else {
                obj.text = '无风险'
                obj.color = '#47e3fd'
            }
            return obj
        },
        /**
         * start 起始百分比
         * end 结束百分比
         * w,h 其实就是圆心横纵坐标
         */
        // 动画效果实现
        canvasTap(start, end, time, w, h) {
            let that = this;
            if (++start > end) {
                return false;
            }
            that.run(start, w, h);
            setTimeout(function () {
                that.canvasTap(start, end, time, w, h);
            }, time);
        },
        /**
         * id----------------canvas画板id
         * percent-----------进度条百分比
         * time--------------画图动画执行的时间
         */
        draw: function (id, percent, animTime) {
            var that = this;
            const ctx2 = wx.createCanvasContext(id);
            that.setData({
                ctx2: ctx2,
                percentage: percent,
                animTime: animTime
            });
            var time = that.data.animTime / that.data.percentage;
            // wx.createSelectorQuery().select('#' + id).boundingClientRect(function (rect) { //监听canvas的宽高
            //     var w = parseInt(rect.width / 2); //获取canvas宽的的一半
            //     var h = parseInt(rect.height / 2); //获取canvas高的一半，
            //     // that.run(that.data.percentage,w,h)//这个没有动画
            //     that.canvasTap(0, that.data.percentage, time, w, h)
            // }).exec();
            let w = that.data.canvasWidth / 2
            that.canvasTap(0, that.data.percentage, time, w, w)
        },
    }
}
```