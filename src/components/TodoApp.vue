<script setup>
import TodoInput from './TodoInput.vue';
import TodoListView from './TodoListView.vue';
</script>

<template>
    <div class="todoApp">
        <TodoInput />
        <TodoListView />
    </div>
</template>

<style>
.errMsg{
    color: red;
}
</style>
