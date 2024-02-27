<template>
    <li class="d-flex justify-content-between align-items-center">
        <span :class="{ 'text-decoration-line-through': task.completed }">
            {{ task.title }}
        </span>
        <button class="btn btn-danger btn-sm" @click="$emit('delete-task', task.id)">Delete</button>
    </li>
</template>
  
<script>
export default {
    name: 'TodoItem',
    props: ['task'],
    methods: {
        deleteTask(id) {
            this.$emit('delete-task', id);
        },
    },
};
</script>
  
<style scoped>
button {
    float: right;
    margin-top: -2px;
}
</style>
  