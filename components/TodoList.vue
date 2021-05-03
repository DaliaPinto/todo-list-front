<template>
  <el-row
    :gutter="20"
  >
    <el-col
      :span="2"
    >
      <el-checkbox
        @click="update"
        border
        v-model="item.is_completed"
      />
    </el-col>
    <el-col
      :span="17"
    >
      <el-popover
        placement="top-start"
        title="Item details"
        width="300"
        trigger="hover"
        content="this is content, this is content, this is content"
      >
        <el-input
          v-model="item.description"
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
      is_completed: false
      description: ''
    methods:
      update: ->
        console.log 'HOLA'
      destroy: ->
        @$axios.delete('todos/'+item.id, item)
        .then ({ success, item, message, error}) =>
          console.log error
    mounted: ->
      @is_completed = @item.is_completed
      @description = @item.description
</script>
