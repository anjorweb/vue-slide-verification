<template>
    <div class="move_msg_code" ref="btnView">
        <span :class="{move: true, success: success}" :style="{left: `${vX}px`, backgroundColor: success ? '' : btnBgColor}"></span>
        <div :class="{txt: true, success: success}"><i v-show="!success"></i><span>{{mTxt}}</span><strong v-show="success"></strong></div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            vX: 0,
            oldX: 0,
            mTxt: '',
            btnBgColor: '',
            successTxtPos: 'left',
            success: false,
            moveing: false
        }
    },
    props: {
        txt: {
            type: String,
            default: '拖动滑块到最右，完成验证'
        },
        moveBgcolor: {
            type: String,
            default: '#edba6b'
        },
        successPosition: {
            type: String,
            default: 'center'
        }
    },
    created() {
        this.mTxt = this.txt;
        this.btnBgColor = this.moveBgcolor;
        this.successTxtPos = this.successPosition;
    },
    mounted() {
        this._init();
    },
    methods: {
        _init() {
            this.btnView = this.$refs.btnView;
            this.btn = this.btnView.querySelector('.move');
            this.maxX = this.btnView.clientWidth - this.btn.clientWidth;
            this.btn.addEventListener('touchstart', this._start, false);
            this.btnView.addEventListener('touchmove', this._move, false);
            this.btnView.addEventListener('touchend', this._end, false);
        },
        _destoryed() {
            this.btn.removeEventListener('touchmove', this._start, false);
            this.btnView.removeEventListener('touchmove', this._move, false);
            this.btnView.removeEventListener('touchend', this._move, false);
            this.btn = null;
            this.btnView = null;
            this._end = null;
            this._start = null;
            this._move = null;
            this._init = null;
        },
        _start(evt) {
            if (this.moveing) {
                return;
            }
            if (evt.changedTouches.length === 1) {
                this.oldX = evt.changedTouches[0].screenX;
                this.curMove = evt.target;
                this.startTime = Date.now();
            }
        },
        _move(evt) {
            if (this.moveing) {
                return;
            }
            if (this.curMove && evt.changedTouches.length === 1) {
                const touchEvt = evt.changedTouches[0];
                const moveX = touchEvt.screenX - this.oldX;
                this.vX += moveX;
                if (this.vX >= this.maxX) {
                    this.vX = this.maxX;
                    // 拖到最右边了
                    this._moveComplete();
                } else if (this.vX < 0) {
                    this.vX = 0;
                }
                this.oldX = touchEvt.screenX;
            }
        },
        _end() {
            if (this.moveing) {
                return;
            }
            // 清除记录的滑块
            this.curMove = null;
            if (this.vX < this.maxX) {
                this.moveing = true;
                this._resetVx();
            }
        },
        _moveComplete() {
            this.success = true;
            const endTime = Date.now();
            // 这是毫秒
            const yTime = endTime - this.startTime;
            this._destoryed();
            let per = 80;
            const time = (yTime / 1000).toFixed(2);
            if (yTime < 500) {
                per = 99;
            } else if (yTime < 1000) {
                per = 98;
            } else if (yTime < 1500) {
                per = 97;
            } else {
                per = 95;
            }
            this.$emit('success', { time: yTime });
            // 计算时间
            this.mTxt = `用了${time}秒，超越了${per}%的用户`;
        },
        // 还原位置
        _resetVx() {
            clearTimeout(this.clearMove);
            this.clearMove = setTimeout(() => {
                if (this.vX < this.maxX && this.vX > 0.3) {
                    this.vX -= this.vX / 3;
                    this._resetVx();
                } else {
                    this.vX = 0;
                    this.moveing = false;
                }
            }, 30);
        }
    }
}
</script>
<style>
.move_msg_code {
    position: relative;
    border: 1px solid #d3d0d4;
    background:#f5f5f5;
    min-height: 30px;
}
.move_msg_code .txt {
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    color:#c0c0c0;
    position: absolute;
    width: 100%;
    left: 0;
    top: 0;
    transition: color .5s ease;
}
.move_msg_code .txt i, .move_msg_code .txt strong {
    height: 100%;
    width: 40px;
}
.move_msg_code .txt span {
    text-align: center;
    flex: 1;
}
.move_msg_code .txt.success {
    box-sizing: border-box;
    color:#000000;
}
.move_msg_code .move {
    width: 40px;
    height: 100%;
    position: absolute;
    left: 0;
    top: 0;
    z-index: 3;
    background-image: url(./move_msg.png);
    background-position: center center;
    background-repeat: no-repeat;
    background-size: auto 50%;
    transition: background .5s ease;
}
.move_msg_code .move.success {
    background-image: url(./ok.png);
    background-color:#d3d0d4;
    background-size: auto 50%;
}
</style>
