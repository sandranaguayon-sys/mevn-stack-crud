<template>
    <div>

    <nav class="navbar navbar-light bg-light">
        <div class="px-5">
            <a href="/" class="navbar-brand">Tasks App</a>
        </div>
    </nav>

    <div class="container">
        <div class="row pt-5">
            <div class="col-md-5">
                <div class="card">
                    <div class="card-body">
                        <form @submit.prevent="sendTask">
                            <div class="form-group">
                                <input type="text" v-model="task.title"
                                class="form-control"
                                placeholder="Insert a title"
                                required>
                            </div>
                            <div class="from-group mt-3">
                                <textarea v-model="task.description" 
                                cols="30" rows="10" 
                                class="form-control" 
                                placeholder="Insert a description"
                                required></textarea>
                            </div>
                            <template v-if="edit === false">
                                <button class="btn btn-primary w-100 mt-3">Save</button>
                            </template>
                            <template v-else>
                                <button class="btn btn-primary w-100 mt-3">Update</button>
                            </template>
                        </form>
                    </div>
                </div>
            </div>
            <div class="col-md-7">
                <table class="table table-bordered">
                    <thead>
                        <tr>
                            <th>Task</th>
                            <th>Description</th>
                            <th>Actions</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="task of tasks">
                            <td>{{ task.title }}</td>
                            <td>{{ task.description }}</td>
                            <td>
                                <button @click="this.editTask(task._id)" 
                                class="btn btn-warning">
                                    Edit
                                </button>
                                <button @click="this.deleteTask(task._id)" 
                                class="btn btn-danger">
                                    Delete
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    </div>

</template>

<script>

    class Task {
        constructor(title, description) {
            this.title = title,
            this.description = description
        }
    }

    export default {
        data() {
            return { 
                task: new Task(),
                tasks: [],
                edit: false,
                taskToEdit: ''
            }
        },
        created(){ //Se ejecuta a penas la aplicacion esta cargando
            this.getTasks()
        },
        methods: {
            getTasks() {
                fetch('/api/tasks')
                .then(res => res.json())
                .then(data => {
                    this.tasks = data
                })
            },
            sendTask() {
                if(this.edit === false) 
                {
                    fetch('/api/tasks', {
                        method: 'POST',
                        body: JSON.stringify(this.task),
                        headers: {
                            'Accept': 'application/json',
                            'Content-type': 'application/json'
                        }
                    })
                    .then(res => res.json())
                    .then(data => {
                        this.getTasks();
                    });
                } 
                else 
                {
                    fetch('/api/tasks/' + this.taskToEdit, {
                        method: 'PUT',
                        body: JSON.stringify(this.task),
                        headers: {
                            'Accept': 'application/json',
                            'Content-type': 'application/json'
                        }
                    })
                    .then(res => res.json())
                    .then(data => {
                        this.getTasks();
                        console.log(data);
                        this.edit = false;
                    });

                }

                this.task = new Task();
            },
            editTask(id) {
                fetch('/api/tasks/'+ id)
                .then(res => res.json())
                .then(data => {
                    this.task = new Task(data.title, data.description)
                    this.taskToEdit = data._id,
                    this.edit = true
                })
            },
            deleteTask(id) {
                fetch('/api/tasks/'+id, {
                    method: 'DELETE',
                    headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.getTasks();
                    console.log(data);
                })
            }
        }
    }
</script>