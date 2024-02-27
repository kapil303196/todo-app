<template>
    <div class="container my-5">
        <h1 class="text-center mb-4">Todo List</h1>
        <div class="input-group mb-3">
            <input type="text" class="form-control" placeholder="Add new task" v-model="newTaskTitle"
                @keyup.enter="addTask">
            <div class="input-group-append">
                <button class="btn btn-primary" type="button" @click="addTask" :disabled="isLoading">
                    {{ isLoading ? 'Adding...' : 'Add Task' }}
                </button>
            </div>
        </div>

        <div v-if="loadingTasks" class="text-center my-3">
            <div class="spinner-border text-primary" role="status">
                <span class="visually-hidden">Loading...</span>
            </div>
        </div>
        <ul class="list-group" v-else>
            <todo-item v-for="task in tasks" :key="task.id" :task="task" @delete-task="deleteTask"
                class="list-group-item"></todo-item>
        </ul>

    </div>
</template>

<script>
import { db } from '../firebase';
import { collection, addDoc, onSnapshot, deleteDoc, doc, query, orderBy } from 'firebase/firestore';
import TodoItem from './TodoItem.vue';

export default {
    name: 'TodoApp',
    components: {
        TodoItem,
    },
    data() {
        return {
            newTaskTitle: '',
            tasks: [],
            isLoading: false,
            loadingTasks: true,
        };
    },
    created() {
        this.fetchTasks();
    },
    methods: {
        async fetchTasks() {
            this.loadingTasks = true;
            const tasksCol = query(collection(db, 'tasks'), orderBy('createdAt', 'desc')); // Order by 'createdAt'
            onSnapshot(tasksCol, (snapshot) => {
                this.tasks = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                this.loadingTasks = false;
            });
        },
        async addTask() {
            if (this.newTaskTitle.trim()) {
                this.isLoading = true; // Loading state on
                const tasksCol = collection(db, 'tasks');
                await addDoc(tasksCol, {
                    title: this.newTaskTitle,
                    completed: false,
                    createdAt: new Date(),
                });
                this.newTaskTitle = '';
                this.isLoading = false; // Loading state off
            }
        },
        async deleteTask(taskId) {
            const taskDocRef = doc(db, 'tasks', taskId);
            await deleteDoc(taskDocRef);
        },
    },
};
</script>


<style scoped>
input {
    margin-bottom: 20px;
    padding: 10px;
    width: 100%;
    box-sizing: border-box;
}
</style>
  