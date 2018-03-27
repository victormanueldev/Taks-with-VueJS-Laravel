<template>
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2">
                <div class="panel panel-default">
                    <div class="panel-heading">My Tasks</div>

                    <div class="panel-body">
                        <div class="input-group">
                            <input type="text" class="form-control" v-model="task.name" @keydown.enter="create">
                            <span class="input-group-btn">
                                <button class="btn btn-success" @click="create">Add</button>
                            </span>
                        </div>

                        <div class="task-list">
                            <ul class="list-unstyled">
                                <li v-for="task in tasks" :key="task.id" :class="{ done: task.completed }">
                                   <div class="checkbox">
                                       <label >
                                           <input type="checkbox" v-model="task.completed" @click="done(task)">{{task.name}}
                                        </label>
                                        <div class="pull-right">
                                            <a href="#" @click.prevent="remove(task)">X</a>
                                        </div>
                                    </div>
                                </li>
                                
                            </ul>
                        </div>
                    </div>
                    <div class="panel-footer" v-if="tasks.length">
                        <span class="label label-default" style="margin-right: 2px">You have {{ tasks.length}} tasks</span>
                        <span class="label label-warning">{{ remainingTask() }} Remaining Tasks</span>
                        <span class="label label-success">{{ completedTask() }} Completed Tasks</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<style>
    body, .task-list{
        padding-top: 20px;
    }
    .done label{
        text-decoration: line-through;
    }
</style>

<script>
    export default {
        mounted() {
            this.fetchData();
        },
        data(){
            return {
                tasks: [],
                task: {
                    name: ''
                }
            }
        },
        methods: {
            remainingTask(){
                return this.tasks.filter(task => {return !task.completed}).length
            },
            completedTask(){
                return this.tasks.filter(task => {return task.completed}).length
            },
            fetchData(){
                axios.get('Task')
                .then((res) => {
                    this.tasks = res.data
                })
                .catch((err) => {
                    console.log(err)
                })
            },
            create (){
                axios.post('Task', this.task)
                    .then((res) => {
                        this.tasks.unshift(res.data)
                        this.task.name = ''
                    })
                    .catch((err) => {
                        console.log(err)
                    })
            },
            done(task){
                var url = 'Task/'+task.id
                axios.put(url, {
                    completed: !task.completed
                })
                .then((res) => {
                    console.log('task updated')
                })
                .catch((err) => {
                    console.log(err)
                })
            },
            remove(task){
                var url = 'Task/'+task.id
                axios.delete(url)
                .then((res) => {
                    const taskIndex = this.tasks.indexOf(task)
                    this.tasks.splice(taskIndex, 1)
                })
                .catch((err) => {
                    console.log(err)
                })
            }
        }
    }
</script>
