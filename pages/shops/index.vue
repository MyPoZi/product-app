<template>
  <v-layout>
    <v-flex xs12 sm8 md6>
      <div class="text-center">
        <h2>
          <nuxt-link to="/">
            商品一覧へ
          </nuxt-link>
        </h2>
        <h1>
          店舗追加
        </h1>
        <v-form ref="form" v-model="valid" class="item-add">
          <v-container grid-list-xl>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field
                  v-model="name"
                  label="name"
                  :rules="nameRules"
                  required
                ></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
          <v-btn class="mr-4" @click="submit">追加</v-btn>
        </v-form>
        <h2>
          店舗一覧
        </h2>
        <div v-for="shop in reverseShops" :key="shop.id">
          <nuxt-link :to="`/shops/${shop.id}`">
            <p>店舗名: {{ shop.name }}</p>
          </nuxt-link>
          <p>作成: {{ shop.created_at }}</p>
          <p>更新: {{ shop.updated_at }}</p>
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
      name: '',
      shops: '',
      nameRules: [
        (v) => !!v || 'Name is required',
        (v) => v.length <= 100 || 'Name must be less than 100 characters'
      ]
    }
  },
  computed: {
    // 配列の要素順番を逆順にする
    reverseShops() {
      if (Object.keys(this.shops).length) {
        return this.shops.slice().reverse()
      }
      return false
    }
  },
  async asyncData({ app }) {
    const response = await app.$axios.$get('/shops').catch((error) => {
      console.log('response error', error)
      return false
    })
    console.log(response)
    return { shops: response.data.shops }
  },
  methods: {
    async submit() {
      if (!this.$refs.form.validate()) {
        return 0
      }
      await this.$axios
        .$post('/shops', {
          shop: {
            name: this.name
          }
        })
        .then((response) => {
          console.log('response data', response)
          this.shops.push(response.data.shop)
        })
        .catch((error) => {
          console.log('response error', error)
        })
    }
  }
}
</script>
<style scoped>
.item-add {
  margin: 50px 0;
}
</style>
