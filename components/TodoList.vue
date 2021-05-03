<template>
  <el-row
    :gutter="20"
    class="mb-2"
  >
    <el-col
      :span="2"
    >
      <el-checkbox
        @change="change"
        border
        v-model="todo.is_completed"
      />
    </el-col>
    <el-col
      :span="17"
    >
      <el-popover
        placement="top-start"
        :title="item.description"
        width="240"
        trigger="hover"
        :content="`Created by: ${item.user.name}
          Completed at: ${item.completed_at ? item.completed_at : '---'}`"
      >
        <el-input
          @input="input"
          v-model="todo.description"
          slot="reference"
        />
      </el-popover>
    </el-col>
    <el-col
      :span="5"
    >
      <el-button
        icon="el-icon-edit"
        circle
        :disabled="disabled"
        @click="update"
      />
      <el-button
        type="danger"
        icon="el-icon-delete"
        circle
        @click="destroy"
      />
    </el-col>
  </el-row>
</template>

<script lang="coffee">
  export default
    name: 'todo-list-component'
    props:
      item:
        type: Object
        default: {}
      taskId:
        type: Number
        default: 0
    data: ->
      disabled: true
      todo: {}
    methods:
      input: (value) ->
        if @todo.description is @item.description
          @disabled = true
        else
          @disabled = false

        if value is "" or value is null
          @disabled = true
        else
          @disabled = false

      change: ->
        if @todo.is_completed
          @todo.is_completed = true
        else
          @todo.is_completed = false
          @todo.completed_at = null
        @update()
      update: ->
        {data:response} = await @$axios.put('todos/'+ @item.id, @todo)

        if !response.error
          @$emit 'update-item', response.item
          @disabled = true

        @$message
          message: response.message
          showClose: true
          type: if response.error then 'error' else 'success'
      destroy: ->
        {data:response} = await @$axios.delete('todos/'+ @item.id, @todo)

        if !response.error
          @$emit 'destroy-item', @item

        @$message
          message: response.message
          showClose: true
          type: if response.error then 'error' else 'success'
    mounted: ->
      @todo = Object.assign {}, {@item...}

</script>

<style>
  .mb-2 {
    margin-bottom: 8px;
  }

</style>
