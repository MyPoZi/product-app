<template>
  <v-layout>
    <v-flex xs12 sm8 md6>
      <div class="text-center">
        <h1>
          商品追加
        </h1>
        <v-form class="item-add">
          <v-container grid-list-xl>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field
                  v-model="title"
                  label="title"
                  required
                ></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field
                  v-model="description"
                  label="description"
                ></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field
                  v-model="price"
                  type="number"
                  label="price"
                ></v-text-field>
              </v-flex>
              <v-flex xs12>
                <p>画像</p>
                <input type="file" @change="onFileChange" />
                <img v-show="uploadedImage" :src="uploadedImage" />
              </v-flex>
            </v-layout>
          </v-container>
          <v-btn class="mr-4" @click="submit">追加</v-btn>
        </v-form>
        <h2>
          商品一覧
        </h2>
        <div v-for="item in reverseItems" :key="item.id">
          <nuxt-link :to="{ name: 'items-id', params: { id: item.id } }"
            ><p>タイトル: {{ item.title }}</p></nuxt-link
          >
          <p>説明: {{ item.description }}</p>
          <p>値段: {{ item.price }}</p>
          <p>値段: {{ item.created_at }}</p>
          <p>値段: {{ item.updated_at }}</p>
          <img :src="item.image" />
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
      items: '',
      uploadedImage: ''
    }
  },
  computed: {
    // 配列の要素順番を逆順にする
    reverseItems() {
      return this.items.slice().reverse()
    }
  },
  async asyncData({ app }) {
    const response = await app.$axios.$get('/items').catch((error) => {
      console.log('response error', error)
      return false
    })
    return { items: response.data.items }
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
          this.items.push(response.data.item)
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
<style scoped>
.item-add {
  margin: 50px 0;
}
</style>
