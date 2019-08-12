<template>
  <v-layout>
    <v-flex xs12 sm8 md6>
      <div class="text-center">
        <h1>
          商品詳細
        </h1>
        <v-form>
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
              <div>
                <p>作成日: {{ item.created_at }}</p>
                <p>更新日: {{ item.updated_at }}</p>
                <img :src="item.image" />
              </div>
            </v-layout>
          </v-container>
          <v-btn class="mr-4" @click="submit">更新</v-btn>
        </v-form>
      </div>
    </v-flex>
  </v-layout>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      item: '',
      title: '',
      description: '',
      price: 0,
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
    return {
      item: response.data.item,
      title: response.data.item.title,
      description: response.data.item.description,
      price: response.data.item.price
    }
  },
  methods: {
    async submit() {
      await this.$axios
        .$patch(`/items/${this.$route.params.id}`, {
          item: {
            title: this.title,
            description: this.description,
            price: parseInt(this.price, 10)
          }
        })
        .then((response) => {
          console.log('response data', response.data.item)
          this.item = response.data.item
          this.title = response.data.item.title
          this.description = response.data.item.description
          this.price = response.data.item.price
        })
        .catch((error) => {
          console.log('response error', error)
        })
    }
  }
}
</script>
