<template>
  <v-layout>
    <v-flex xs12 sm8 md6>
      <div class="text-center">
        <h1>
          商品追加
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
            </v-layout>
          </v-container>
          <v-btn class="mr-4" @click="submit">submit</v-btn>
        </v-form>
        <h1>
          商品一覧
        </h1>
        <div v-for="item in reverseItems" :key="item.id">
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
      items: ''
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
    return { items: response.data }
  },
  methods: {
    async submit() {
      await this.$axios
        .$post('/items', {
          item: {
            title: this.title,
            description: this.description,
            price: parseInt(this.price, 10)
          }
        })
        .then((response) => {
          console.log('response data', response)
          this.items.push(response.data)
        })
        .catch((error) => {
          console.log('response error', error)
        })
      // this.$set(this.items, response)
      // if (typeof response !== 'undefined') {
      //
      // }
    }
  }
}
</script>
