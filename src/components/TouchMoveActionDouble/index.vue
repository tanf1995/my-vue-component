<template>
    <div class="wrap">
        <TouchMoveAction 
            :outerWrap="true"
        />

        <TouchMoveAction />
    </div>
</template>


<script>


export default {
    name: 'TouchMoveActionDouble',
    components: {
        TouchMoveAction: () => import('./components/TouchMoveAction')
    },
    methods: {
        renderItem(){
            return <CardItem />
        }
    }
}
</script>


<style lang="scss">
    body{
        overflow: hidden;
    }

    .wrap{
        width: 100%;
        height: 100vh;
        overflow: hidden;
    }
</style>
