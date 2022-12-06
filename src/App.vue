<template>
  <v-app>
    <v-container>
      <div>
        <h2 class="text-center">ToDo</h2>
      </div>
    </v-container>
    <v-container>
      <v-card class="pb-5" v-for="toDo in toDoList" :key="toDo.id">
        <v-card-title v-text="'Title: ' + toDo.title"></v-card-title>
        <v-card-subtitle v-text="'ID: ' + toDo.id"></v-card-subtitle>
        <v-card-text v-text="'Description: ' + toDo.description"></v-card-text>
        <v-container>
          <div class="text-center ma-2">
            <v-btn
                dark
                @click="(title1=toDo.title),(description1=toDo.description)(snackbar1 = true)"
                color="primary"
            >
              <v-icon>mdi-pencil</v-icon>
            </v-btn>
            <v-btn
                v-on:click="deleteById(toDo.id)"
                color="error"
            >
              <v-icon>mdi-trash-can-outline</v-icon>
            </v-btn>
            <v-snackbar
                v-model="snackbar1"
            >
              <v-text-field type="text" v-model="title1" class="form-control ml-2"
                            placeholder="Новое название"></v-text-field>
              <v-text-field type="text" v-model="description1" class="form-control ml-2" placeholder="Новое описание"/>

              <template v-slot:action="{ attrs }">
                <v-container>
                  <v-btn
                      color="pink"
                      text
                      v-bind="attrs"
                      @click="snackbar1 = false"
                  >
                    Закрыть
                  </v-btn>
                  <v-btn color="yellow"
                         text
                         v-bind="attrs"
                         v-on:click="updateById(toDo.id);"
                         @click="snackbar1 = false, visible = true">Обновить
                  </v-btn>
                </v-container>
              </template>
            </v-snackbar>
          </div>
        </v-container>
      </v-card>
    </v-container>
    <v-container v-show="visible1">
      <v-card>
        <v-card-title class="fill-height align-end" v-text="'Title: ' + toDo1.title"></v-card-title>
        <v-card-subtitle class="fill-height align-end" v-text="'ID: ' + toDo1.id"></v-card-subtitle>
        <v-card-text class="fill-height align-end" v-text="'Description: ' + toDo1.description"></v-card-text>
      </v-card>
    </v-container>
    <v-spacer>
    </v-spacer>
    <v-container>
      <v-row justify="center">
        <v-col
            cols="12"
            sm="6"
            md="3"
        >
          <v-text-field
              label="Поиск по ID"
              placeholder="Введите ID"
              outlined
              v-model="inputId"
          ></v-text-field>
        </v-col>
      </v-row>
    </v-container>
    <v-container>
      <v-row justify="center">
        <v-col>
          <v-row justify="center">
            <v-btn
                variant="flat"
                color="error"
                class="ma-2 white--text"
                @click="deleteAll()">
              Удалить всё
            </v-btn>
          </v-row>
        </v-col>
        <v-col>
          <v-row justify="center">
            <v-btn
                v-on:click="visible=!visible"
                color="blue-grey"
                class="ma-2 white--text"
                @click="loadAll()">
              <v-icon dark>
                mdi-cloud-download
              </v-icon>
            </v-btn>
            <v-btn
                v-on:click="visible1=!visible1"
                color="blue-grey"
                class="ma-2 white--text"
                @click="getById(inputId)">
              <v-icon dark>
                mdi-magnify
              </v-icon>
            </v-btn>
          </v-row>
        </v-col>
        <v-col>
          <v-row justify="center">
            <div class="text-center ma-2">
              <v-btn
                  dark
                  @click="snackbar = true"
              >
                Добавить
              </v-btn>
              <v-snackbar
                  v-model="snackbar"
              >
                <v-text-field type="text" v-model="title" class="form-control ml-2" placeholder="Введите название"/>
                <v-text-field type="text" v-model="description" class="form-control ml-2"
                              placeholder="Введите описание"/>
                <div class="alert alert-success" v-if="isSuccess">Создание успешно</div>

                <template v-slot:action="{ attrs }">
                  <v-container>
                    <v-btn
                        color="pink"
                        text
                        v-bind="attrs"
                        @click="snackbar = false"
                    >
                      Закрыть
                    </v-btn>
                    <v-btn color="green"
                           text
                           v-bind="attrs"
                           @click="create(), snackbar = false">
                      Создать
                    </v-btn>
                  </v-container>
                </template>
              </v-snackbar>
            </div>
          </v-row>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import axios from "axios";

export default {
  name: 'App',
  data: () => ({
    toDoList: [],
    toDo1: [],
    loading: [],
    title: "",
    description: "",
    title1: "",
    description1: "",
    inputId: "",
    visible: false,
    visible1: false,
    snackbar: false,
    snackbar1: false,
  }),
  methods: {
    async loadAll() {
      try {
        const responce = await axios.get("http://localhost:3000/todos");
        this.toDoList = responce.data.toDoList;
      } catch (e) {
        console.log("loadAll" + e);
      }
    },
    async getById(id) {
      try {
        const responce = await axios.get("http://localhost:3000/todos/" + id);
        this.toDo1 = responce.data.toDo;
        this.loadAll();
      } catch (e) {
        console.log("getById" + e);
      }
    },
    async create() {
      try {
        const responce = await axios.post("http://localhost:3000/todos", {
          title: this.title,
          description: this.description
        })
        console.log(responce);
        this.loadAll();
        this.title = '';
        this.description = '';
      } catch (e) {
        console.log("create" + e);
      }
    },
    async deleteAll() {
      try {
        const responce = await axios.delete("http://localhost:3000/todos")
        console.log(responce);
        this.loadAll();
      } catch (e) {
        console.log("deleteAll" + e);
      }
    },
    async updateById(id) {
      try {
        const responce = await axios.patch("http://localhost:3000/todos/" + id, {
          title: this.title1,
          description: this.description1
        })
        console.log(responce);
        this.loadAll();
      } catch (e) {
        console.log("deleteAll" + e);
      }
    },
    async deleteById(id) {
      try {
        await axios.delete("http://localhost:3000/todos/" + id);
        this.loadAll();
      } catch (e) {
        console.log("deleteById" + e);
      }
    },
  }
};
</script>
<style>
</style>
