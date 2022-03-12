<template>
  <h2>後台產品</h2>
  <div class="container">
    <div class="text-end mt-4">
      <!-- 用帶入的字串參數判斷開啟modal類別 -->
      <button class="btn btn-primary" type="button" >
        建立新的產品
      </button>
    </div>
    <table class="table mt-4">
      <thead>
        <tr>
          <th width="120">分類</th>
          <th>產品名稱</th>
          <th width="120">原價</th>
          <th width="120">售價</th>
          <th width="100">是否啟用</th>
          <th width="120">編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td>{{ item.category }}</td>
          <td>{{ item.title }}</td>
          <td>{{ item.origin_price }}</td>
          <td>{{ item.price }}</td>
          <td>
            <span class="text-success" v-if="item.is_enabled">啟用</span>
            <span v-else>未啟用</span>
          </td>
          <td>
            <div class="btn-group">
              <button type="button" class="btn btn-outline-primary btn-sm" @click="openProductModal(item.id)" >
                編輯
              </button>
              <button type="button" class="btn btn-outline-danger btn-sm">
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- 頁數元件 前內(get-productlist)後外(getProductsList)  -->
    <Pagination
      :pages="pagination"
      @get-productlist="getProductsList"
    ></Pagination>
  </div>
  <!-- 新增&編輯產品元件 -->
  <product-modal
    :id="productId"
    ref="productModal"
    :current-page="pagination.current_page"
  ></product-modal>
  <!--刪除Modal -->
  <DeleteModal
    :item="temp"
    ref="delProductModal"
  ></DeleteModal>
</template>

<script>
import DeleteModal from '@/components/DelModal.vue'
import Pagination from '@/components/Pagination.vue'
import ProductModal from '@/components/ProductModal.vue'
export default {
  components: {
    Pagination,
    ProductModal,
    DeleteModal
  },
  data () {
    return {
      products: [],
      // 傳到內層的id值
      productId: '',
      pagination: {},
      temp: {}
    }
  },
  methods: {
    // 取得產品列表  //參數預設值
    getProductsList (page = 1) {
      const getProductsUrl = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/admin/products/?page=${page}`
      this.$http.get(getProductsUrl).then((res) => {
        this.products = res.data.products
        this.pagination = res.data.pagination
      })
    },
    openProductModal (id) {
      this.productId = id
      this.$refs.productModal.showModal()
    }
  },
  mounted () {
    this.getProductsList()
  }
}
</script>
