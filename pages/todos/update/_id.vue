<template>
  <div class="todos">
    <h1 class="todos__header">
      <NuxtLink to="/todos">TODOリスト</NuxtLink>
      / 編集
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
          <input v-model="todo.title" type="text" />
        </div>
      </div>
      <div class="todos-data-row">
        <div class="todos-data-row__header">詳細</div>
        :
        <div class="todos-data-row__data">
          <textarea v-model="todo.detail" cols="100" rows="10" />
        </div>
      </div>
      <div class="todos-data-row">
        <div class="todos-data-row__header">開始日</div>
        :
        <div class="todos-data-row__data">
          <VueCtkDateTimePicker
            v-model="todo.start_date"
            format="YYYY-MM-DD hh:mm"
            time-zone="ja"
          />
        </div>
      </div>
      <div class="todos-data-row">
        <div class="todos-data-row__header">終了日</div>
        :
        <div class="todos-data-row__data">
          <VueCtkDateTimePicker
            v-model="todo.end_date"
            format="YYYY-MM-DD hh:mm"
            time-zone="ja"
          />
        </div>
      </div>
      <div class="todos-data-row">
        <button class="todos-data-row__submit" @click="updateToDo">更新</button>
        <button class="todos-data-row__delete" @click="deleteToDo">削除</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { Context } from '@nuxt/types'

/* eslint-disable */
type ToDoData = {
  id: number | null
  title: string
  detail: string
  start_date: string | null
  end_date: string | null
}
/* eslint-enable */

type DataType = {
  todo: ToDoData
  datePickerFormat: string
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
      todo: {
        id: null,
        title: '',
        detail: '',
        start_date: null,
        end_date: null,
      },
      datePickerFormat: 'yyyy-MM-dd h:mm:ss',
    }
  },
  methods: {
    parseDate(data: string): string {
      const date: Date = new Date(data)
      const dayList: string[] = ['日', '月', '火', '水', '木', '金', '土']
      return `${date.getFullYear()}/${date.getMonth() + 1}/${date.getDate()}(${
        dayList[date.getDay()]
      }) ${('0' + date.getHours()).slice(-2)}:${('0' + date.getMinutes()).slice(
        -2
      )}`
    },
    async updateToDo(): Promise<void> {
      if (this.todo.title === '') return

      await this.$axios.patch(`/api/v1/todos/${this.todo.id}`, this.todo)

      alert('更新しました')
    },
    async deleteToDo(): Promise<void> {
      await this.$axios.delete(`/api/v1/todos/${this.todo.id}`)
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

      &__submit,
      &__delete {
        height: 40px;
        width: 60px;
      }

      &__submit {
        margin-left: auto;
        margin-right: 5px;
      }

      &__delete {
        margin-left: 5px;
        margin-right: auto;
      }
    }
  }
}
</style>
