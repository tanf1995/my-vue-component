<template>
    <div class="wrap">
        <TouchMoveAction :outerWrap="true" />
        <TouchMoveAction></TouchMoveAction>
    </div>
</template>


<script>


export default {
    name: 'TouchMoveActionDouble',
    components: {
        TouchMoveAction: () => import('./components/TouchMoveAction'),
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
