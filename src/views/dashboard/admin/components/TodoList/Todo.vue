<template>
  <li
    :class="{completed: todo.done, editing: editing}"
    class="todo"
  >
    <div class="view">
      <input
        :checked="todo.done"
        class="toggle"
        type="checkbox"
        @change="toggleTodo( todo)"
      >
      <label
        @dblclick="editing = true"
        v-text="todo.text"
      />
      <button
        class="destroy"
        @click="deleteTodo( todo )"
      />
    </div>
    <input
      v-show="editing"
      v-focus="editing"
      :value="todo.text"
      class="edit"
      @keyup.enter="doneEdit"
      @keyup.esc="cancelEdit"
      @blur="doneEdit"
    >
  </li>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'

export interface ITodo {
  text: string
  done: boolean
}

@Component({
  name: 'TodoDemo',
  directives: {
    focus: (el, { value }, { context }) => {
      if (value) {
        if (context) {
          context.$nextTick(() => {
            el.focus()
          })
        }
      }
    }
  }
})
export default class extends Vue {
  @Prop({ default: { text: '', done: false } }) public todo!: ITodo

  public editing = false

  public deleteTodo(todo: ITodo) {
    this.$emit('delete-todo', todo)
  }

  public editTodo({ todo, value }: { todo: ITodo, value: string }) {
    this.$emit('edit-todo', { todo, value })
  }

  public toggleTodo(todo: ITodo) {
    this.$emit('toggle-todo', todo)
  }

  public doneEdit(e: KeyboardEvent) {
    const value = (e.target as HTMLInputElement).value.trim()
    const { todo } = this
    if (!value) {
      this.deleteTodo(todo)
    } else if (this.editing) {
      this.editTodo({
        todo,
        value
      })
      this.editing = false
    }
  }

  public cancelEdit(e: KeyboardEvent) {
    (e.target as HTMLInputElement).value = this.todo.text
    this.editing = false
  }
}
</script>
