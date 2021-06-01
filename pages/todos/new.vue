<template>
  <div class="todos">
    <h1 class="todos__header">
      <NuxtLink to="/todos">TODOリスト</NuxtLink>
      / 新規作成
    </h1>
    <div class="todos-data">
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
        <button @click="postToDo" class="todos-data-row__submit">登録</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

/* eslint-disable */
type ToDoData = {
  title: string
  detail: string
  start_date: string | undefined
  end_date: string | undefined
}
/* eslint-enable */

type DataType = {
  todo: ToDoData
  datePickerFormat: string
}

export default Vue.extend({
  data(): DataType {
    return {
      todo: {
        title: '',
        detail: '',
        start_date: undefined,
        end_date: undefined,
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
    async postToDo(): Promise<void> {
      if (this.todo.title === '') return

      await this.$axios.post('/api/v1/todos', this.todo)

      alert('登録しました')
      this.todo = {
        title: '',
        detail: '',
        start_date: undefined,
        end_date: undefined,
      }
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

      &__submit {
        margin: auto;
        height: 40px;
        width: 60px;
      }
    }
  }
}
</style>
