<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col cols="3">
            <v-card shaped>
              <v-app-bar :color="config.color" dark>
                <v-toolbar-title>
                  <span class="application-subname">Coins</span>
                </v-toolbar-title>
              </v-app-bar>

              <v-card-text>
                <div style="width: 76px; display: inline-block">
                  <v-img src="https://www.nicepng.com/png/full/164-1648732_8-bit-mario-coin-mario-coin-pixel.png" style="width: 67px; height: 76px;"></v-img>
                </div>
                
                <span class="application-coin-count">:{{ coinCount }}</span>
              </v-card-text>
            </v-card>
          </v-col>

          <v-col cols="6">
            <v-card shaped>
              <v-app-bar :color="config.color" dark>
                <v-toolbar-title >
                  <span class="application-name">Todo List</span>
                </v-toolbar-title>
                
                <v-spacer></v-spacer>
                
                <v-btn 
                  rounded 
                  dark icon 
                  @click="openDialogForm">
                    <v-icon>mdi-plus-thick</v-icon>
                </v-btn>
                
                <v-btn 
                  rounded 
                  dark 
                  icon 
                  @click="showConfigDialog = !showConfigDialog">
                    <v-icon>mdi-cog</v-icon>
                </v-btn>
              </v-app-bar>

              <v-card-text>
                <v-list>
                  <v-list-item v-if="todoList.length == 0">
                    No tasks found
                  </v-list-item>

                  <v-list-item dense v-for="todoItem in todoList" :key="todoItem.id" @click="openTask(todoItem.id)">
                    <h2 style="margin-left: 10px">
                      <span class="application-text">{{ todoItem.title }}</span>
                    </h2>
                    
                    <v-spacer></v-spacer>

                    <span v-if="todoItem.dateToComplete != ''" style="margin-right: 10px;">
                      Finish Until: {{ todoItem.dateToComplete }}
                    </span>


                  </v-list-item>
                </v-list>
              </v-card-text>

            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <!-- Form new todo item dialog -->
    <v-dialog v-model="showNewTodoItemFormDialog" width="500">
      <v-card>
          <v-toolbar :color="config.color" dark>
            <v-toolbar-title>
              <span class="application-subname">New Task</span>
            </v-toolbar-title>
          </v-toolbar>

          <v-card-text>
            <br>
            <v-text-field label="Task:" :color="config.color" v-model="formNewTodoItem.title"></v-text-field>
            <v-text-field type="number" label="Reward:" :color="config.color" v-model="formNewTodoItem.reward"></v-text-field>
            <v-text-field type="date" label="Max date to complete task:" :color="config.color" v-model="formNewTodoItem.dateToComplete"></v-text-field>
          </v-card-text>

          <v-divider></v-divider>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn 
              :color="config.color" 
              v-if="updateTask"
              dark
              @click="finishTask">
              Complete!
            </v-btn>

            <v-btn 
              :color="config.color"
              dark 
              @click="addNewTask" 
              v-if="!updateTask">
              Add
            </v-btn>

            <v-btn 
              color="red" 
              dark
              @click="showNewTodoItemFormDialog = false; updateTask = false;">
              Cancel
            </v-btn>
          </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Config Dialog -->
    <v-dialog v-model="showConfigDialog" width="500">
      <v-card>
          <v-toolbar :color="config.color" dark>
            <v-toolbar-title>
              <span class="application-subname">Configuration</span>
            </v-toolbar-title>
          </v-toolbar>

          <v-card-text>
            <div style="margin-top: 20px">
              Application Color Picker:
              <br><br>
              <input type="color" value="#10c992" v-model="config.color">
            </div>
            
            <div style="margin-top: 20px">
              Dark Theme:
              <v-switch v-model="$vuetify.theme.dark"></v-switch>
            </div>
          </v-card-text>

          <v-divider></v-divider>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn 
              :color="config.color" 
              @click="showConfigDialog = !showConfigDialog">
                Ok
            </v-btn>
          </v-card-actions>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<script>
  export default {
    name: 'App',

    data: () => ({
      showConfigDialog: false, 
      showNewTodoItemFormDialog: false,
      updateTask: false,

      todoList: [
        { id: 1, title: 'Finish this product', dateToComplete: '21/10/2020', completed: false, reward: 10 },
        { id: 2, title: 'Upload to the github', dateToComplete: '', completed: false, reward: 5 },
        { id: 3, title: 'Some bug fix', dateToComplete: '21/10/2020', completed: false, reward: 2 },
        { id: 4, title: 'Drink some coffe', dateToComplete: '21/10/2020', completed: false, reward: -1 },
      ],

      coinCount: 0,

      formNewTodoItem: {
        title: '',
        dateToComplete: '',
        reward: 0
      },

      selectedTaskId: undefined,

      config: {
        color: "#10c992",
      },
    }),

    methods: {
      openDialogForm() {
        this.formNewTodoItem.title = '';
        this.formNewTodoItem.dateToComplete = '';
        this.formNewTodoItem.reward = 0;
        this.showNewTodoItemFormDialog = true;
      },

      addNewTask() {
        this.todoList.push({ 
          id: Date.now(), 
          title: this.formNewTodoItem.title, 
          dateToComplete: this.formNewTodoItem.dateToComplete,
          completed: false,
          reward: this.formNewTodoItem.reward,
        });

        this.formNewTodoItem.title = '';
        this.formNewTodoItem.dateToComplete = '';
        this.reward = 0;

        this.showNewTodoItemFormDialog = false;
      },

      openTask(taskId) {
        this.todoList.forEach(element => {
          if(element.id == taskId) {
            this.updateTask = true;
            this.selectedTaskId = taskId;
            this.formNewTodoItem.title = element.title;
            this.formNewTodoItem.dateToComplete = element.dateToComplete;
            this.formNewTodoItem.reward = element.reward;
            this.showNewTodoItemFormDialog = true;
            return true;
          }
        })
      },

      finishTask() {
        this.todoList = this.todoList.filter(element => {
          if(element.id == this.selectedTaskId) {
            this.coinCount += parseInt(element.reward);
          } else {
            return element;
          }
        }); 

        this.showNewTodoItemFormDialog = false;
        this.updateTask = false;
      }
    },

    created: function() {
      this.$vuetify.theme.dark = true;
    }
  };
</script>

<style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Dancing+Script&family=Special+Elite&display=swap');
  @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

  .application-name {
    font-family: 'Dancing Script', cursive;
    font-size: 42px;
  }

  .application-subname {
    font-family: 'Dancing Script', cursive;
    font-size: 32px;
  }

  .application-text {
    font-family: 'Special Elite', cursive;
  }

  .application-coin-count {
    font-family: 'Press Start 2P', cursive;
    font-size: 50px;
  }
</style>