<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>

                <v-spacer></v-spacer>

                <v-btn color="indigo mr-2" dark @click="dialogFinished = true">
                    Todo Selesai
                </v-btn>
                 <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>
            <v-data-table :headers="headers" :items="todos" :search="search">
                <template v-slot:[`item.priority`]="{ item }">
                   <span v-if="item.priority == 'Penting'">
                        <v-chip
                            class="ma-2"
                            color="red"
                            label
                            outlined>
                                {{item.priority}}
                        </v-chip>
                   </span>
                   <span v-else-if="item.priority == 'Tidak penting'">
                        <v-chip
                            class="ma-2"
                            color="green"
                            label
                            outlined>
                                {{item.priority}}
                        </v-chip>
                   </span>
                    <span v-else-if="item.priority == 'Biasa'">
                        <v-chip
                            class="ma-2"
                            color="blue"
                            label
                            outlined>
                                {{item.priority}}
                        </v-chip>
                   </span>
                </template>
                <template v-slot:[`item.actions`]="{ item }">
                     <v-icon class="mr-2" @click="dialog=true, editItem(item)" color="blue">
                            mdi-pencil
                    </v-icon>
                     <v-icon class="mr-2" @click="confirmDeleteDialog(item)" color="red">
                            mdi-delete
                    </v-icon>
                </template>
               
            </v-data-table>
        </v-card>

        <!-- Finished Todo -->
        <v-dialog v-model="dialogFinished" persistent max-width="600px">
            <v-card>
                <v-card-title>
                        <span class="headline">Finished To Do</span>
                </v-card-title>
                <v-data-table :headers="headers2" :items="todosFinished" :search="search">
                    <template v-slot:[`item.priority`]="{ item }">
                   <span v-if="item.priority == 'Penting'">
                        <v-chip
                            class="ma-2"
                            color="red"
                            label
                            outlined>
                                {{item.priority}}
                        </v-chip>
                   </span>
                   <span v-else-if="item.priority == 'Tidak penting'">
                        <v-chip
                            class="ma-2"
                            color="green"
                            label
                            outlined>
                                {{item.priority}}
                        </v-chip>
                   </span>
                    <span v-else-if="item.priority == 'Biasa'">
                        <v-chip
                            class="ma-2"
                            color="blue"
                            label
                            outlined>
                                {{item.priority}}
                        </v-chip>
                   </span>
                </template>
                </v-data-table>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="dialogFinished=false">
                        Close
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <!-- Delete Dialog -->
        <v-dialog v-model="deleteDialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Yakin Ingin Menghapus?</span>
                </v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel_delete()">
                        Tidak
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="deleteItem()">
                        Ya
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <!-- Tambah Edit Dialog -->
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>
                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>
                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-main>
</template>
<script>
    export default {
        name: "List",
        data() {
            return {
                search: null,
                dialog: false,
                dialogFinished: false,
                itemSelect: null,
                add: 1,
                deleteDialog: false,
                headers: [
                    {
                        text: "Task",
                        align: "start",
                        sortable: true,
                        value: "task",
                    },
                    { text: "Priority", value: "priority" },
                    { text: "Note", value: "note" },
                    { text: "Actions", value: "actions" },
                ],
                headers2: [
                    {
                        text: "Task",
                        align: "start",
                        sortable: true,
                        value: "task",
                    },
                    { text: "Priority", value: "priority" },
                    { text: "Note", value: "note" },
                ],
                todos: [
                    {
                        task: "Bernafas",
                        priority: "Penting",
                        note: "huffttt",
                    },
                    {
                        task: "nongkrong",
                        priority: "Tidak penting",
                        note: "bersama tman2",
                    },
                    {
                        task: "masak",
                        priority: "Biasa",
                        note: "masak air 500ml",
                    },
                ],
                formTodo: {
                    task: null,
                    priority: null,
                    note: null,
                },
                todosFinished:[],
            };
        },

        methods: {
            save() {
                if(this.add == 1){
                    this.todos.push(this.formTodo);
                    this.resetForm();
                }
                else{
                    this.todos[this.todos.indexOf(this.itemSelect)].task = this.formTodo.task;
                    this.todos[this.todos.indexOf(this.itemSelect)].priority = this.formTodo.priority;
                    this.todos[this.todos.indexOf(this.itemSelect)].note = this.formTodo.note;
                }
                this.dialog = false;
            },

            cancel() {
                this.resetForm();
                this.dialog = false;
            },

            resetForm() {
                this.formTodo = {
                    task: null,
                    priority: null,
                    note: null,
                };
            },

            deleteItem() {
                const todoIndex = this.todos.indexOf(this.itemSelect);
                this.todosFinished.push(this.todos[todoIndex]);
                this.todos.splice(todoIndex, 1);
                this.deleteDialog = false;
            },

            confirmDeleteDialog(item){
                this.deleteDialog = true;
                this.itemSelect = item;
            },

            cancel_delete(){
                this.deleteDialog = false;
            },

            editItem(item){
                this.add = 0;
                this.itemSelect = item;
                this.formTodo.task = item.task;
                this.formTodo.priority = item.priority;
                this.formTodo.note = item.note;
                this.dialog = true;
            },
        },
    };
</script>