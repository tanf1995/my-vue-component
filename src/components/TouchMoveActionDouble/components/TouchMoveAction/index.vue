<template>
    <div 
        class="outer"
        ref="outerWrap"
        :style="{
            top: `${outerWrap? 0 : screenHeight - wrapScale*(cardHeight + 150)}px`
        }"
    >
        <div 
            class="container"
            :style="{
                width: `${turntableR*2 + cardWidth}px`,
                height: `${turntableR*2 + cardHeight}px`,
                left: `${screenWidth/2 - turntableR-cardWidth/2}px`,
                top: `${
                    outerWrap? -(1-wrapScale)*(turntableR+cardHeight/2) + screenHeight - wrapScale*(cardHeight + bottomPos + cardHeight*reletiveTop): 
                    -(1-wrapScale)*(turntableR+cardHeight/2)
                }px`,
                transform: `scale(${wrapScale})`,
            }"
            ref="container"
        >
            <ul
                class="turntable"
                :style="{
                    transform: `rotate(${turnRotate}deg)`,
                    top: `${turntableR + cardHeight/2}px`
                }"
            >
                <li
                    v-for="index in cardCount"
                    :key="index"
                    class="card"
                    :style="computedCardPosStyle(index-1)"
                    @mousedown.prevent
                    @mouseup.prevent
                    @mousemove.prevent
                >
                    {{index}}
                </li>
            </ul>
        </div>
    </div>
</template>


<script>
export default {
    name: 'TouchMoveAction',
    props: {
        turntableR: {  // 转盘半径
            type: Number,
            default: 1000
        },
        // 卡牌信息
        cardCount: {
            type: Number,
            default: 35
        },
        cardWidth: {
            type: Number,
            default: 230
        },
        cardHeight: {
            type: Number,
            default: 320
        },
        // 上部分距下部分卡片距离 = a*cardHeight
        reletiveTop: {
            type: Number,
            default: 0.8
        },
        // true: 上部分， false: 下部分
        outerWrap: {
            type: Boolean,
            default: false
        },
        // 下部分距离
        bottomPos: {
            type: Number,
            default: 150
        }
    },
    data: () => ({
        // 动画
        startX: 0,
        endX: 0,
        timer: null,
        UDLMactionTimer: null,
        mouseIsDown: false,
        xGap: 0,
        lastSpeed: 0,
        direction: 1,
        wrapScale: 1,
        // 配置
        timeGap: 20,
        turnRotateProportion: 500,
        reduceSpeed: 0.7,
        // 数据
        cards: [
            "card1",
            "card2"
        ],
    }),
    computed: {
        turnRotate(){
            return this.xGap*this.turnRotateProportion / (2*Math.PI*this.turntableR);
        },
        unitCardDeg(){
            return 360/this.cardCount;
        },
        screenWidth(){
            return document.documentElement.clientWidth;
        },
        screenHeight(){
            return document.documentElement.clientHeight;
        }
    },
    methods: {
        handleMouseDown(e){
            e.preventDefault();
            clearInterval(this.UDLMactionTimer);
            this.mouseIsDown = true;
            this.startX = e.clientX || e.touches[0].clientX;
            this.endX = e.clientX || e.touches[0].clientX;
        },
        handleMouseUp(e){
            e.preventDefault();
            this.mouseIsDown = false;
            clearInterval(this.timer);
            clearInterval(this.UDLMactionTimer);
            this.timer = null;
            this.startX = 0;
            this.endX = 0;
            if(this.lastSpeed) this.UDLMaction();
        },
        handleMouseMove(e){
            e.preventDefault();
            this.endX = e.clientX || e.touches[0].clientX;
            if(!this.mouseIsDown) return;
            if(!this.timer){
                this.timer = setInterval(() => {
                    let moveGap = this.endX - this.startX;

                    this.lastSpeed = moveGap/this.timeGap;
                    this.xGap += moveGap;
                    this.direction = moveGap > 0 ? 1 : -1;
                    this.startX = this.endX;
                }, this.timeGap);
            }
        },

        computedCardPosStyle(index){
            let deg = index * this.unitCardDeg;
            let absDeg = Math.abs((deg + this.turnRotate) % 360);
            let z_index = absDeg > 180 ? Math.ceil(absDeg-180): Math.ceil(180-absDeg);

            return {
                width: this.cardWidth + "px",
                height: this.cardHeight + "px",
                top: -Math.cos(deg*Math.PI/180)*this.turntableR + "px",
                left: Math.sin(deg*Math.PI/180)*this.turntableR + "px",
                transform: `translate(-50%, -50%) rotate(${deg}deg)`,
                zIndex: z_index
            }
        },
        UDLMaction(){
            let a = -this.reduceSpeed*this.direction;
            this.UDLMactionTimer = setInterval(() => {
                this.lastSpeed = (this.lastSpeed + a)*this.direction >= 0? this.lastSpeed + a: 0;
                this.xGap += (this.lastSpeed) * this.timeGap;
                if(!this.lastSpeed){
                    this.moreDynamic();
                    return clearInterval(this.UDLMactionTimer);
                }
            }, this.timeGap);
        },
        moreDynamic(){
            let time = 10;
            let timer = setInterval(() => {
                this.xGap += this.direction*3;
                if(--time <= 0) clearInterval(timer);
            }, 20)
        },
        responseContainerScale(){
            if(document.documentElement.clientWidth < 650){
                this.turnRotateProportion = 800;
                this.wrapScale = document.documentElement.clientWidth/650;
                console.log(this.wrapScale);
            }
        }
    },
    mounted(){
        let container_dom = this.outerWrap ? this.$refs.outerWrap : this.$refs.container;

        container_dom.addEventListener('mousedown', this.handleMouseDown.bind(this));
        container_dom.addEventListener('mouseup', this.handleMouseUp.bind(this));
        container_dom.addEventListener('mouseleave', this.handleMouseUp.bind(this));
        container_dom.addEventListener('mousemove', this.handleMouseMove.bind(this));
        container_dom.addEventListener('touchstart', this.handleMouseDown.bind(this));
        container_dom.addEventListener('touchend', this.handleMouseUp.bind(this));
        container_dom.addEventListener('touchcancel', this.handleMouseUp.bind(this));
        container_dom.addEventListener('touchmove', this.handleMouseMove.bind(this));

        window.addEventListener('resize', this.responseContainerScale.bind(this));
        window.addEventListener('load', this.responseContainerScale.bind(this));
    },
    beforeDestroy(){
        let container_dom = this.outerWrap ? this.$refs.outerWrap : this.$refs.container;

        container_dom.removeEventListener('mousedown', this.handleMouseDown.bind(this));
        container_dom.removeEventListener('mouseup', this.handleMouseUp.bind(this));
        container_dom.removeEventListener('mouseleave', this.handleMouseUp.bind(this));
        container_dom.removeEventListener('mousemove', this.handleMouseMove.bind(this));
        container_dom.removeEventListener('touchstart', this.handleMouseDown.bind(this));
        container_dom.removeEventListener('touchend', this.handleMouseUp.bind(this));
        container_dom.removeEventListener('touchcancel', this.handleMouseUp.bind(this));
        container_dom.removeEventListener('touchmove', this.handleMouseMove.bind(this));

        window.removeEventListener('resize', this.responseContainerScale.bind(this));
    }
}
</script>


<style lang="scss">
    .outer{
        width: 100%;
        height: 100vh;
        position: absolute;
        overflow: hidden;
        cursor: pointer;

        .container{
            position: absolute;
            border-radius: 50%;

            .turntable{
                list-style: none;
                padding: 0;
                margin: 0;
                position: absolute;
                left: 50%;

                .card{
                    background-color: rgb(34, 145, 169);
                    position: absolute;

                    line-height: 320px;
                    text-align: center;
                    color: #fff;
                    font-size: 30px;

                    &:nth-child(2n){
                        background-color: gray;
                    }
                }
            }
        }
    }
</style>
