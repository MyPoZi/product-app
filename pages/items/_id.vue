<template>
  <v-layout>
    <v-flex xs12 sm8 md6>
      <div class="text-center">
        <h1>
          商品一覧
        </h1>
        <div>
          <p>タイトル: {{ item.title }}</p>
          <p>説明: {{ item.description }}</p>
          <p>値段: {{ item.price }}</p>
        </div>
      </div>
    </v-flex>
  </v-layout>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      title: '',
      description: '',
      price: 0,
      item: '',
      uploadedImage: ''
    }
  },
  computed: {
    // 配列の要素順番を逆順にする
    reverseItems() {
      return this.items.slice().reverse()
    }
  },
  async asyncData({ params, app }) {
    const response = await app.$axios
      .$get(`/items/${params.id}`)
      .catch((error) => {
        console.log('response error', error)
        return false
      })
    console.log(response.data)
    console.log(response.data.item)
    return { item: response.data.item }
  },
  methods: {
    async submit() {
      await this.$axios
        .$post('/items', {
          item: {
            title: this.title,
            description: this.description,
            price: parseInt(this.price, 10),
            image: this.uploadedImage
          }
        })
        .then((response) => {
          console.log('response data', response)
          this.items.push(response.data)
        })
        .catch((error) => {
          console.log('response error', error)
        })
    },
    onFileChange(e) {
      const files = e.target.files || e.dataTransfer.files
      this.createImage(files[0])
    },
    // アップロードした画像を表示
    createImage(file) {
      const reader = new FileReader()
      reader.onload = (e) => {
        this.uploadedImage = e.target.result
      }
      reader.readAsDataURL(file)
    }
  }
}
</script>
