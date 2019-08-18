<template>
  <v-layout>
    <v-flex xs12 sm8>
      <div class="text-center">
        <h1>
          店舗詳細
        </h1>
        <v-form>
          <v-container grid-list-xl>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field
                  v-model="name"
                  label="name"
                  required
                ></v-text-field>
              </v-flex>
              <div class="width-100">
                <p>作成日: {{ shop.created_at }}</p>
                <p>更新日: {{ shop.updated_at }}</p>
              </div>
              <div class="shop-product width-100">
                <h2>商品一覧</h2>
              </div>
              <div
                v-for="item in shop.items"
                :key="item.id"
                class="shop-product width-100"
              >
                <p>説明: {{ item.description }}</p>
                <p>値段: {{ item.price }}</p>
                <p>作成: {{ item.created_at }}</p>
                <p>更新: {{ item.updated_at }}</p>
              </div>
            </v-layout>
          </v-container>
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
      shop: '',
      name: ''
    }
  },
  async asyncData({ params, app }) {
    const response = await app.$axios
      .$get(`/shops/${params.id}`)
      .catch((error) => {
        console.log('response error', error)
        return false
      })
    return {
      shop: response.data.shop,
      name: response.data.shop.name
    }
  }
}
</script>
<style scoped>
.shop-product {
  margin: 30px 0;
}
.width-100 {
  width: 100%;
}
</style>
