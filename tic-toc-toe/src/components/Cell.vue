<template>
    <div @click.once="clickHandler"></div>
</template>

<script>
export default {
    name:'Cell',
    methods:{
        clickHandler(e){
            this.$emit('on-click', e)
        }
    }
}
</script>