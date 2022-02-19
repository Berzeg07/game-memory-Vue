<template>
    <div class="card" @click="clickCard">
        <span>
            <slot>Test</slot>
        </span>
    </div>
</template>

<script>
export default {
    name: 'Card',
    methods: {
        clickCard(e) {
            this.$emit('clickCard', {
                name: e.currentTarget.getAttribute('name'),
                index: e.currentTarget.getAttribute('index')
            })
        }
    }
}
</script>