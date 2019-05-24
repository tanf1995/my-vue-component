<template>
    <div :title="name">
        {{name}}
        <br />

        <button @click="sayHi">click me</button>

        {{now}}
        <br />
        {{nowF()}}
    </div>
</template>


<script>
export default {
    name: 'TouchMoveAction',
    data: () => ({
        name: "tf"
    }),
    computed: {
        now: () => Date.now()
    },
    methods: {
        sayHi: function(){
            window.console.log("hello");
            this.name += "z";
        },
        nowF: function(){
            return Date.now()
        }
    }
}
</script>


<style>

</style>
