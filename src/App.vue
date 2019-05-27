<template>
    <div id="app">
        <!--<LearnCom />-->
        <!-- <TouchMoveAction /> -->
        <TouchMoveActionDouble />
    </div>
</template>

<script>
export default {
    name: 'app',
    components: {
        // LearnCom: () => import('./components/LearnCom'),
        // TouchMoveAction: () => import('./components/TouchMoveAction'),
        TouchMoveActionDouble: () => import('./components/TouchMoveActionDouble'),
    }
}
</script>

<style>
    body{
        margin: 0;
        padding: 0;
    }
</style>
