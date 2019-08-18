<template>
  <v-layout>
    <v-flex xs12 sm8 md6>
      <div class="text-center">
        <h2>
          <nuxt-link to="/shops">
            店舗管理へ
          </nuxt-link>
        </h2>
        <h1>
          商品追加
        </h1>
        <h2>
          {{ errors }}
        </h2>
        <v-form ref="form" class="item-add">
          <v-container grid-list-xl>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field
                  v-model="title"
                  label="title"
                  :rules="nameRules"
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
                <v-select
                  v-model="selectShops"
                  :items="shops"
                  label="店舗名"
                  :rules="shopRules"
                ></v-select>
              </v-flex>
              <v-flex xs12>
                <p>画像</p>
                <input type="file" @change="onFileChange" />
                <img v-show="uploadedImage" :src="uploadedImage" />
              </v-flex>
            </v-layout>
          </v-container>
          <v-btn class="mr-4" @click="submit">追加</v-btn>
          <v-snackbar v-model="snackbar" :timeout="timeout">
            {{ update_text }}
            <v-btn color="blue" text @click="snackbar = false">
              Close
            </v-btn>
          </v-snackbar>
        </v-form>
        <h1>
          検索
        </h1>
        <v-form @submit.prevent="search">
          <v-text-field v-model="word" label="検索"></v-text-field>
          <div class="text-center">
            <v-btn type="submit">検索</v-btn>
          </div>
        </v-form>
        <h2>
          商品一覧
        </h2>
        <div v-for="item in reverseItems" :key="item.id">
          <nuxt-link :to="{ name: 'items-id', params: { id: item.id } }"
            ><p>商品名: {{ item.title }}</p></nuxt-link
          >
          <p v-if="item.shop">店舗名: {{ item.shop.name }}</p>
          <p>説明: {{ item.description }}</p>
          <p>値段: {{ item.price }}</p>
          <p>作成: {{ item.created_at }}</p>
          <p>更新: {{ item.updated_at }}</p>
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
      valid: true,
      title: '',
      description: '',
      price: 0,
      items: '',
      uploadedImage: '',
      word: '',
      shops: '',
      selectShops: '',
      snackbar: false,
      update_text: '作成しました。',
      timeout: 2000,
      nameRules: [
        (v) => !!v || 'Name is required',
        (v) => v.length <= 100 || 'Name must be less than 100 characters'
      ],
      shopRules: [(v) => !!v || 'Shop is required'],
      errors: ''
    }
  },
  computed: {
    // 配列の要素順番を逆順にする
    reverseItems() {
      if (Object.keys(this.items).length) {
        return this.items.slice().reverse()
      }
      return false
    }
  },
  async asyncData({ app }) {
    const response = await app.$axios.$get('/items').catch((error) => {
      console.log('response error', error)
      return false
    })
    const responseShop = await app.$axios
      .$get('/shops/names')
      .catch((error) => {
        console.log('response error', error)
        return false
      })
    return { items: response.data.items, shops: responseShop.data.shop_names }
  },
  methods: {
    async submit() {
      if (!this.$refs.form.validate()) {
        return 0
      }
      await this.$axios
        .$post('/items', {
          item: {
            title: this.title,
            description: this.description,
            price: parseInt(this.price, 10),
            image: this.uploadedImage,
            shop_id: this.selectShops
          }
        })
        .then((response) => {
          console.log('response data', response)
          this.items.push(response.data.item)
          this.snackbar = true
          this.clearItemForm()
          this.$refs.form.resetValidation()
          this.errors = ''
        })
        .catch((error) => {
          console.log('response error', error)
          this.errors = error
        })
    },
    async search() {
      await this.$axios
        .$get('/items/search', {
          params: {
            word: this.word
          }
        })
        .then((response) => {
          console.log('response data', response)
          this.items = {}
          if (Object.keys(response).length) {
            this.items = response.data.items
          }
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
    },
    clearItemForm() {
      this.title = ''
      this.description = ''
      this.price = 0
      this.uploadedImage = ''
      this.selectShops = ''
    }
  }
}
</script>
<style scoped>
.item-add {
  margin: 50px 0;
}
</style>
