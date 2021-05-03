<template>
  <div
    v-loading="loading"
  >
    <el-row
      :gutter="20"
      class="mt-3 px-3"
    >
      <el-col
        v-for="task in tasks"
        :key="task.id"
        :span="12"
      >
        <el-card
          shadow="none"
        >
          <div
            slot="header"
          >
            <el-badge
              :value="task.todos.length"
              :type="task.todos.length > 0 ? 'success' : 'danger'"
              class="badge"
            >
              <h3>
                {{ task.description }}
              </h3>
            </el-badge>
            <el-tooltip
              effect="dark"
              content="Add item"
              placement="top"
            >
              <el-button
                v-if="task.description === 'To do'"
                class="right"
                type="primary"
                icon="el-icon-plus"
                circle
                @click="show_input = true"
              />
            </el-tooltip>

            <div
              v-if="show_input && task.description === 'To do'"
            >
              <el-divider></el-divider>
              <el-row
                :gutter="20"
              >
                <el-col
                  :span="18"
                >
                  <el-input
                    @input="input"
                    v-model="todo.description"
                    placeholder="Type description item here"
                  />
                </el-col>

                <el-col
                  :span="6"
                >
                  <el-button
                    type="primary"
                    icon="el-icon-check"
                    :disabled="disabled"
                    @click="store"
                    circle
                  />
                  <el-button
                    icon="el-icon-remove-outline"
                    circle
                    @click="removeTask"
                  />
                </el-col>
              </el-row>
            </div>
          </div>
          <div
            v-for="todo in task.todos"
            :key="todo.id"
          >
            <TodoList
              :item="todo"
              @destroy-item="updateTasks"
              @update-item="updateTasks"
            />
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script lang="coffee">
  import TodoList from '~/components/TodoList'
  export default
    name: 'todo-list-item'
    components: {
      TodoList
    }

    #get taks with theirs todos
    asyncData: ({ $axios, app}) =>
      {data:tasks} = await $axios.get('tasks')
      return {
        tasks
      }
    data: ->
      todo: {
        description: null
      }
      disabled: true
      show_input: false
      loading: false
    methods:
      #Validations form
      input: (value) ->
        if value is "" or value is null
          @disabled = true
        else
          @disabled = false

      #if remove form, clean input
      removeTask: ->
        @disabled = true
        @show_input = false
        @todo.description = ''

      #update taks
      updateTasks: ->
        @loading = true
        {data:@tasks} = await @$axios.get('tasks')
        @loading = false

      #save new item
      store: ->
        {data:response} = await @$axios.post('todos', @todo)
        if !response.error
          @removeTask()

        @$message
          message: response.message
          showClose: true
          type: if response.error then 'error' else 'success'

        @updateTasks()
</script>

<style>
  #__layout, #__nuxt {
    height: inherit;
    min-height: 100%;
  }
  html {
    height: 100%;
    width: 100%;
  }

  body {
    margin: 0px;
    padding: 0px;
    font-size: 14px;
    font-family: 'Segoe UI', sans-serif;
    min-height: 100%;
    color: #606266;
    background: linear-gradient(110deg, #F2F6FC 60%, #E7EEF8 60%);
  }
  .mt-3 {
    margin-top: 12px;
  }

  .px-3 {
    padding-left: 12px;
    padding-right: 12px;
  }

  .right {
    float: right !important;
  }

  .left {
    float: left !important;
  }

  .badge {
    margin-top: 10px;
    font-weight: 500;
  }
</style>
