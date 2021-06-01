<template>
  <div class="todos">
    <h1 class="todos__header">
      <NuxtLink to="/todos">TODOリスト</NuxtLink>
      / 詳細
    </h1>
    <div class="todos-data">
      <div class="todos-data-row">
        <div class="todos-data-row__header">ID</div>
        :
        <div class="todos-data-row__data">
          {{ todo.id }}
        </div>
      </div>
      <div class="todos-data-row">
        <div class="todos-data-row__header">タイトル</div>
        :
        <div class="todos-data-row__data">
          {{ todo.title }}
        </div>
      </div>
      <div class="todos-data-row">
        <div class="todos-data-row__header">詳細</div>
        :
        <div class="todos-data-row__data">
          {{ todo.detail }}
        </div>
      </div>
      <div class="todos-data-row">
        <div class="todos-data-row__header">開始日</div>
        :
        <div class="todos-data-row__data">
          {{ parseDate(todo.start_date) }}
        </div>
      </div>
      <div class="todos-data-row">
        <div class="todos-data-row__header">終了日</div>
        :
        <div class="todos-data-row__data">
          {{ parseDate(todo.end_date) }}
        </div>
      </div>
      <div class="todos-data-row">
        <button class="todos-data-row__update" @click="moveUpdateToDo(todo.id)">
          編集
        </button>
        <button class="todos-data-row__delete" @click="deleteToDo(todo.id)">
          削除
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { Context } from '@nuxt/types'

/* eslint-disable */
type ToDoData = {
  id: number
  title: string
  detail: string
  start_date: string
  end_date: string | null
  created_at: string
  updated_at: string
}
/* eslint-enable */

type DataType = {
  todo: ToDoData | null
}

type ResponseToDoData = {
  data: ToDoData
}

type asyncDataType = {
  todo: ToDoData
}

export default Vue.extend({
  async asyncData(ctx: Context): Promise<asyncDataType> {
    const todoResponse: ResponseToDoData = await ctx.$axios.get(
      `/api/v1/todos/${ctx.params.id}`
    )
    return {
      todo: todoResponse.data,
    }
  },
  data(): DataType {
    return {
      todo: null,
    }
  },
  methods: {
    parseDate(data: string): string {
      if (data === null) return ''

      const date: Date = new Date(data)
      const dayList: string[] = ['日', '月', '火', '水', '木', '金', '土']
      return `${date.getFullYear()}/${date.getMonth() + 1}/${date.getDate()}(${
        dayList[date.getDay()]
      }) ${('0' + date.getHours()).slice(-2)}:${('0' + date.getMinutes()).slice(
        -2
      )}`
    },
    moveUpdateToDo(id: number): void {
      this.$router.push(`/todos/update/${id}`)
    },
    async deleteToDo(id: number): Promise<void> {
      await this.$axios.delete(`/api/v1/todos/${id}`)
      alert('削除しました')
      this.$router.push('/todos')
    },
  },
})
</script>

<style lang="scss" scoped>
.todos {
  width: 80%;
  margin: auto;

  &__header {
    text-align: center;
  }

  &-data {
    width: 100%;
    padding: 20px 0;
    font-size: 24pt;

    &-row {
      display: flex;

      &__header,
      &__data {
        padding: 0 10px;
      }

      &__header {
        width: 20%;
      }

      &__data {
        width: 80%;
      }

      &__update,
      &__delete {
        height: 40px;
        width: 60px;
      }

      &__update {
        margin-right: 5px;
        margin-left: auto;
      }

      &__delete {
        margin-right: auto;
        margin-left: 5px;
      }
    }
  }
}
</style>
