<template>
    <div v-bind:class="class" class="flex flex-col">
        <label :for="id">{{ name }}</label>
        <input class="rounded" :type="type" :id="id" @input="$emit('update:modelValue', $event.target.value)" :required="required ? true : false">
    </div>
</template>

<script>
export default {
    props:{
        type: {
            type: String,
            default: 'text'
        },
        name: {
            type: String,
            default: ''
        },
        id: {
            type: String,
            default: ''
        },
        class: {
            type: String,
            default: ''
        },
        required: {
            type: Boolean,
            default: false,
        },
    },
}
</script>