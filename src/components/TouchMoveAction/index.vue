<template>
    <div class="container">
        <ul 
            class="turntable"
            :style="computedTurntableStyle"
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
</template>


<script>
export default {
    name: 'TouchMoveAction',
    data: () => ({
        // 动画
        startX: 0,
        endX: 0,
        timer: null,
        mouseIsDown: false,
        turnRotate: 0,
        xGap: 0,
        lastSpeed: 0,
        // 配置
        timeGap: 20,
        turntableR: 1200,
        cardCount: 50,
        // 数据
        cards: [
            "card1",
            "card2"
        ],
    }),
    computed: {
        direction(){
            return this.xGap > 0 ? 1 : -1;
        },
        computedTurntableStyle(){
            let deg = this.xGap*360 / (2*Math.PI*this.turntableR);

            return {
                transform: `rotate(${deg}deg)`
            }
        }
    },
    methods: {
        handleMouseDown(e){
            this.mouseIsDown = true;
            this.startX = e.clientX;
            this.endX = e.clientX;
        },
        handleMouseUp(){
            this.mouseIsDown = false;
            clearInterval(this.timer);
            this.timer = null;
            this.startX = 0;
            this.endX = 0;
            this.UDLMaction();
            // console.log(this.lastSpeed, this.xGap, this.startX, this.endX);
        },
        handleMouseMove(e){
            this.endX = e.clientX;
            if(!this.mouseIsDown) return;
            if(!this.timer){
                this.timer = setInterval(() => {
                    this.lastSpeed = (this.endX - this.startX)/this.timeGap;
                    this.xGap += this.endX - this.startX;
                    this.startX = this.endX;
                }, this.timeGap);
            }
        },

        computedCardPosStyle(index){
            let deg = index * 360/this.cardCount;
            let absDeg = (deg + Math.atan(this.xGap/this.turntableR)*50 + 360) % 360;
            let z_index = absDeg > 180 ? Math.ceil((absDeg-180)): Math.ceil((180-absDeg));

            return {
                top: -Math.cos(deg*Math.PI/180)*this.turntableR + "px",
                left: Math.sin(deg*Math.PI/180)*this.turntableR + "px",
                transform: `translate(-50%, -50%) rotate(${deg}deg)`,
                zIndex: z_index
            }
        },
        UDLMaction(){
            let a = -0.5*this.direction;
            let timer = setInterval(() => {
                this.lastSpeed = (this.lastSpeed + a)*this.direction >= 0? this.lastSpeed + a: 0;
                this.xGap += (this.lastSpeed) * this.timeGap;
                if(!this.lastSpeed) return clearInterval(timer);
            }, this.timeGap);
        }
    },
    mounted(){
        window.addEventListener('mousedown', this.handleMouseDown.bind(this));
        window.addEventListener('mouseup', this.handleMouseUp.bind(this));
        window.addEventListener('mouseleave', this.handleMouseUp.bind(this));
        window.addEventListener('mousemove', this.handleMouseMove.bind(this));
    },
    beforeDestroy(){
        window.removeEventListener('mousedown', this.handleMouseDown.bind(this));
        window.removeEventListener('mouseup', this.handleMouseUp.bind(this));
        window.removeEventListener('mouseleave', this.handleMouseUp.bind(this));
        window.removeEventListener('mousemove', this.handleMouseMove.bind(this));
    }
}
</script>


<style lang="scss">
    .container, .mask{
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        cursor: pointer;

        .turntable{
            list-style: none;
            padding: 0;
            margin: 0;
            position: absolute;
            bottom: -800px;
            left: 50%;

            .card{
                width: 230px;
                height: 320px;
                background-color: rgb(34, 145, 169);
                position: absolute;
                transition: all 0.2s linear;

                &:nth-child(2n){
                    background-color: gray;
                }
            }
        }
    }
</style>
