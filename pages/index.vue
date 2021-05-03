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
              <div>
                {{ task.description }}
              </div>
            </el-badge>
            <el-button
              v-if="task.description === 'To do'"
              class="right"
              type="primary"
              size="mini"
              icon="el-icon-plus"
            >
              Add item
            </el-button>
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
    asyncData: ({ $axios, app}) =>
      {data:tasks} = await $axios.get('tasks')
      return {
        tasks
      }
    data: ->
      loading: false
    methods:
      updateTasks: ->
        @loading = true
        {data:@tasks} = await @$axios.get('tasks')
        @loading = false


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
