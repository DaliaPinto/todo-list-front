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
        width="300"
        trigger="hover"
      >
        <div>
          <strong>Created by: </strong>{{ item.user.name }}
          <br>
          <strong>Completed at: </strong>{{ item.completed_at || '---' }}
        </div>

        <el-input
          @input="input"
          v-model="todo.description"
          placeholder="Type description item here"
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

    data: ->
      disabled: true
      todo: {}
      
    methods:
      #Validations form
      input: (value) ->
        if @todo.description is @item.description
          @disabled = true
        else
          @disabled = false

        if value is "" or value is null
          @disabled = true
        else
          @disabled = false

      #updating if is_completed is true
      change: ->
        if @todo.is_completed
          @todo.is_completed = true
        else
          @todo.is_completed = false
          @todo.completed_at = null
        @update()

      #update and emit an event
      update: ->
        {data:response} = await @$axios.put('todos/'+ @item.id, @todo)

        if !response.error
          @$emit 'update-item', response.item
          @disabled = true

        @$message
          message: response.message
          showClose: true
          type: if response.error then 'error' else 'success'

      #destrog and emit an event
      destroy: ->
        @$confirm "Are you sure to delete this item?", 'Warning',
            confirmButtonText: "Accept",
            showCancelButton: true,
            type: 'warning'
        .then =>
          {data:response} = await @$axios.delete('todos/'+ @item.id, @todo)

          if !response.error
            @$emit 'destroy-item', @item

          @$message
            message: response.message
            showClose: true
            type: if response.error then 'error' else 'success'
        .catch ->

    #cloning principal prop
    mounted: ->
      @todo = Object.assign {}, {@item...}

</script>

<style>
  .mb-2 {
    margin-bottom: 8px;
  }

</style>
