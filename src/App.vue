<template>
    <div id="app">
        <!--<LearnCom />-->
        <TouchMoveAction />
    </div>
</template>

<script>
export default {
    name: 'app',
    components: {
        // LearnCom: () => import('./components/LearnCom'),
        TouchMoveAction: () => import('./components/TouchMoveAction'),
    }
}
</script>

<style>
    body{
        margin: 0;
        padding: 0;
    }
</style>
