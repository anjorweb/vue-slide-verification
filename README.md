

###  一款好用的vue滑块验证组件

## 使用

#### template

```
<touchCode
    move-bgcolor="#edba6b"
    txt="拖动滑块到最右，完成验证"
    @success="touchMsgCodeSuccess"
    @error="touchMsgCodeError"
    style="margin-bottom: .25rem; height: .84rem; border-radius: 5px; overflow:hidden; font-size: .26rem;">
</touchCode>
```

其中
move-bgcolor 滑块滑到最右后的背景色
txt 提示文字
@success 完成滑动验证后回调，可选接收参数 {time: xxx}
@error 没有完成滑动验证回调，每次不滑动到最右就会触发



#### script

```
<script>
import touchCode from '_components/touchCode';
export default {
	components: {
        touchCode
    },
    methods: {
        // 拖拽验证码
        touchMsgCodeSuccess(param) {
            console.log(param.time);
        },
        touchMsgCodeError() {
        }
    }
}
</script>
```

