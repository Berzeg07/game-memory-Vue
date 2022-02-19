<template>
    <ol class="reslist">
        <li v-for="(item, index) in list" :key="index + item">{{ item }}</li>
    </ol>
</template>

<script>
export default {
    name: 'ResList',
    props: ['list']
}
</script>