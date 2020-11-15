<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List UGD</h3>

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

                <v-btn color="success" dark @click="dialogedit = true">
                    tambah
                </v-btn>
            </v-card-title>

            <v-data-table 
                :headers="headers" 
                :items="todos"
                :search="search">

                <template v-slot:[`item.priority`]="{ item }">
                    <td>
                        <v-chip v-if="item.priority == 'Penting'" color="red" outlined>
                            {{ item.priority }}
                        </v-chip>
                        <v-chip v-else-if="item.priority == 'Biasa'" color="primary" outlined>
                            {{ item.priority }}
                        </v-chip>
                        <v-chip v-else color="success" outlined>
                            {{ item.priority }}
                        </v-chip>
                    </td>
                </template>

                <template v-slot:[`item.actions`]="{ item }">
                    <v-btn small class="mr-2" @click="editData(item)">Edit</v-btn>
                    <v-btn small @click="deleteData(item)">Delete</v-btn>
                </template>

                <template v-slot:[`item.multipleDel`]="{item}">
                    <v-checkbox multipleDel :key="item" @click.capture.stop="multipleCheck(item)"/>              
                </template>
            </v-data-table>
        </v-card>
        <v-card v-if="multi.length" class="mt-2" style="overflow-y:scroll; height:250px">
        <v-card-title class="text-left">Delete Multipe : </v-card-title>
            <v-list-item class="text-left" v-for="(item, i) in multi" :key="i">
                    <v-list-item-content>
                        <v-list-item-title> - {{item.task}}</v-list-item-title>
                    </v-list-item-content>
            </v-list-item>
        <v-btn color="error ml-3 mt-3 mb-3 float-left" dark @click="deleteAll">
                Hapus Semua
        </v-btn>
        </v-card>
        <v-dialog v-model="dialogedit" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline" 
                        v-if="adding == true" >
                           Form Todo
                    </span>
                    <span class="headline" 
                        v-else>
                           Form Todo
                    </span>
                </v-card-title>

                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                            autofocus
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

                    <v-btn v-if="adding == true" 
                        color="blue darken-1" 
                        text 
                        @click="save">
                        Save
                    </v-btn>

                    <v-btn v-else 
                        color="blue darken-1" 
                        text 
                        @click="edit(formTodo)">
                        Save
                    </v-btn>

                </v-card-actions>
            </v-card>
        </v-dialog>            
        <v-dialog v-model="dialogdel" persistent max-width="375px">
            <v-card>
                <v-card-title>
                        Yakin ingin menghapus?
                </v-card-title>

                <v-card-actions>
                    <v-spacer></v-spacer>
                    
                    <v-btn color="green darken-1" text @click="cancel">
                        Tidak
                    </v-btn>
                    <v-btn color="red darken-1" text @click="confirmdelete">
                        Ya
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
            adding: true,
            search: null,
            editdata: null,
            dialogedit: false,
            dialogdel: false,
            dialognote: false,
            multi: [],
            todos: [
                {
                    task: "bernafas",
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
            sortTodo: {
                priority: null,
            },

            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                
                { 
                    text: "Priority",
                    field: "priority", 
                    value: "priority" 
                },
                {
                    text: "Note",
                    field: "note",
                    value: "note",
                },
                { 
                    text: "Actions", 
                    value: "actions", 
                    sortable: false,
                },
                {
                    text: "",
                    value: "multipleDel",
                }
            ],
            find: {
                search: '',
                priority: '',
            },


        };
    },

    methods: {
        save() {
            this.todos.push(this.formTodo);
            this.cancel();
        },
        deleteData(item) {
            this.dialogdel = true;
            this.editdata = item;
        },
        confirmdelete() {
            this.todos.splice(this.todos.indexOf(this.editdata), 1);
            this.dialogdel = false;
        },
        cancel() {
            this.resetForm();
            this.dialogedit = false;
            this.dialogdel = false;
            this.dialognote = false;
            this.editdata = null;
            this.adding = true;
        },
        editData(item) {
            this.adding = false;
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note: item.note,
            };
            this.editdata = item;
            this.dialogedit = true;

        },
        edit(formTodo) {
            this.editdata.task = formTodo.task;
            this.editdata.priority = formTodo.priority;
            this.editdata.note = formTodo.note;
            this.cancel();
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        multipleCheck(item){
            if(this.multi.includes(item)) {
                this.multi.splice(this.multi.indexOf(item), 1);
            } else {
                this.multi.push(item);                
            }
        },
        deleteAll:function(){             
            for(var i = 0; i < this.multi.length; i++){            
                this.todos.splice( this.todos.indexOf(this.multi[i]), 1);
            }
            this.multi=[];                            
        }
    },
};
</script>