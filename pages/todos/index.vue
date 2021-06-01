<template>
  <div class="todos">
    <h1 class="todos__title">TODOリスト</h1>
    <table class="todos-list">
      <thead class="todos-list__header">
        <tr>
          <th class="todos-list__id">ID</th>
          <th class="todos-list__title">タイトル</th>
          <th class="todos-list__created">作成日</th>
          <th class="todos-list__action"></th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="todo in todos"
          :key="todo.id"
          class="todos-list-data-wrapper"
        >
          <td class="todos-list-data-wrapper__id">
            <NuxtLink :to="{ name: 'todos-id', params: { id: todo.id } }">
              {{ todo.id }}
            </NuxtLink>
          </td>
          <td class="todos-list-data-wrapper__title">{{ todo.title }}</td>
          <td class="todos-list-data-wrapper__created">
            {{ parseDate(todo.created_at) }}
          </td>
          <td class="todos-list-data-wrapper-action">
            <div class="todos-list-data-wrapper-action-button-area">
              <button
                class="todos-list-data-wrapper-action-button-area__update"
                @click="moveUpdateToDo(todo.id)"
              >
                編集
              </button>
              <button
                class="todos-list-data-wrapper-action-button-area__delete"
                @click="deleteToDo(todo.id)"
              >
                削除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <button class="todos__create" @click="moveCreateToDo">新規登録</button>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

/* eslint-disable */
type ToDoData = {
  id: number
  title: string
  detail: string
  created_at: string
  updated_at: string
}
/* eslint-enable */

type ResponseToDoData = {
  data: ToDoData[]
}

type DataType = {
  todos: ToDoData[] | null
}

export default Vue.extend({
  data(): DataType {
    return {
      todos: null,
    }
  },
  async fetch(): Promise<void> {
    const todoResponse: ResponseToDoData = await this.$axios.get(
      '/api/v1/todos'
    )
    this.todos = todoResponse.data
  },
  methods: {
    parseDate(data: string): string {
      const date: Date = new Date(data)
      const dayList: string[] = ['日', '月', '火', '水', '木', '金', '土']
      return `${date.getFullYear()}/${date.getMonth() + 1}/${date.getDate()}(${
        dayList[date.getDay()]
      }) ${date.getHours()}:${date.getMinutes()}`
    },
    moveCreateToDo(): void {
      this.$router.push('/todos/new')
    },
    moveUpdateToDo(id: number): void {
      this.$router.push(`/todos/update/${id}`)
    },
    async deleteToDo(id: number): Promise<void> {
      await this.$axios.delete(`/api/v1/todos/${id}`)
      alert('削除しました')
      this.$fetch()
    },
  },
})
</script>

<style lang="scss" scoped>
.todos {
  width: 80%;
  margin: auto;

  &__title {
    text-align: center;
  }

  &__create {
    margin: 20px auto;
    height: 60px;
    width: 80px;
    display: block;
  }

  &-list {
    width: 100%;

    &__header {
      color: white;
      background-color: #303030;
    }

    &-data-wrapper {
      height: 50px;

      &__id {
        text-align: right;
      }

      &:nth-child(odd) {
        background-color: #efefef;
      }

      &-action-button-area {
        display: flex;
        gap: 5px;

        &__update,
        &__delete {
          width: 50%;
        }
      }
    }

    th,
    td {
      padding: 0 10px;
      line-height: 50px;
    }

    &__id {
      width: 5%;
    }

    &__title {
      width: 65%;
    }

    &__created {
      width: 20%;
    }

    &__action {
      width: 10%;
    }
  }
}
</style>
