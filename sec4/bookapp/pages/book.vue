<template>
  <v-container>
    <NuxtChild
      :books="books"
      @add-book-list="addBook"
      @update-book-info="updateBookInfo"
      @delete-local-storage="deleteLocalStorage"
    />
  </v-container>
</template>

<script>
const STORAGE_KEY = 'books'

export default {
  data() {
    return {
      books: [],
      newBook: null,
    }
  },
  created() {
    if (localStorage.getItem(STORAGE_KEY)) {
      try {
        this.books = JSON.parse(localStorage.getItem(STORAGE_KEY))
      } catch (e) {
        // localStorage.removeItem(STORAGE_KEY)
      }
    }
  },
  methods: {
    addBook(e) {
      // 実際に何かしたことを入力する
      this.books.push({
        id: this.books.length,
        title: e.title,
        img: e.img,
        description: e.description,
        readDate: '',
        memo: '',
      })
      // this.newBook = ''
      this.saveBooks()
      // booksの最後の要素のidを引数として渡す
      this.goToEditPage(this.books.slice(-1)[0].id)
    },

    removeBook(x) {
      this.books.splice(x, 1)
      this.saveBooks()
    },

    saveBooks() {
      // localStorageに保存
      // JSON.stringifyでオブジェクトを文字列に変換
      const parsed = JSON.stringify(this.books)
      // localStorage.setItem(保存する名前, 保存する値)
      localStorage.setItem(STORAGE_KEY, parsed)
    },

    updateBookInfo(e) {
      const updateInfo = {
        id: e.id,
        readDate: e.date,
        memo: e.memo,
        title: this.books[e.id].title,
        image: this.books[e.id].image,
        description: this.books[e.id].description,
      }
      this.books.splice(e.id, 1, updateInfo) // idのオブジェクトを置換
      this.saveBooks()
      this.$router.push('/book') // 保存したらindexに戻る
    },

    goToEditPage(id) {
      // $router.pushでページ遷移
      // /book/1のようにidを渡す
      this.$router.push(`/book/edit/${id}`)
    },

    deleteLocalStorage() {
      const isDeleted = '本当に削除してもよろしいですか？'
      if (window.confirm(isDeleted)) {
        localStorage.setItem(STORAGE_KEY, '')
        localStorage.removeItem(STORAGE_KEY)
        this.books = []
        window.location.reload()
      }
    },
  },
}
</script>

<style></style>
