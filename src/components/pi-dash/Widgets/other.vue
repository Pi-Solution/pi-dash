<template>
    <div class="pd-other"
         :style="{
        gridRow: widget.position.row + '/' + (widget.position.row + widget.size.width),
        gridColumn: widget.position.column + '/' + (widget.position.column + widget.size.height)
    }"
    >
        Hello I'm List component
    </div>
</template>

<script>
    export default {
        name: "other",
        props:{
            widget: Object
        }
    }
</script>

<style scoped>
    .pd-other{
        background: coral;
    }
</style>