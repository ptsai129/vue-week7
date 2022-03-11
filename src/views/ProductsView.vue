<template>
  <h2>後台產品</h2>
  <div class="container">
    <div class="text-end mt-4">
      <!-- 用帶入的字串參數判斷開啟modal類別 -->
      <button class="btn btn-primary" type="button" @click="openModal('new')">
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
              <button
                type="button"
                class="btn btn-outline-primary btn-sm"
                @click="openModal('edit', item)"
              >
                編輯
              </button>
              <button
                type="button"
                class="btn btn-outline-danger btn-sm"
                @click="openModal('delete', item)"
              >
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- 頁數元件 前內後外  -->
    <Pagination
      :pages="pagination"
      @get-productlist="getProductsList"
    ></Pagination>
  </div>
  <!-- 新增/編輯Modal -->
  <div
    id="productModal"
    ref="productModal"
    class="modal fade"
    tabindex="-1"
    aria-labelledby="productModalLabel"
    aria-hidden="true"
  >
    <!-- 新增&編輯產品元件 -->
    <ProductModal
      :temp="temp"
      :is-new="isNew"
      @update="getProductsList"
      :current-page="pagination.current_page"
    ></ProductModal>
  </div>
  <!--刪除Modal -->
  <div
    id="delProductModal"
    ref="delProductModal"
    class="modal fade"
    tabindex="-1"
    aria-labelledby="delProductModalLabel"
    aria-hidden="true"
  >
    <DeleteModal :item="temp" @update="getProductsList"></DeleteModal>
  </div>
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
      isNew: false, // 編輯or新增產品modal標題判斷用
      temp: {
        imagesUrl: []
      },
      pagination: {}
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
    // 開啟modal
    openModal (type, product) {
      if (type === 'new') {
        // 如果判斷是新增產品 temp內的資料會被清空
        this.temp = {
          imagesUrl: []
        }
        // modal標題顯示新增產品
        this.isNew = true
        // 打開modal
        ProductModal.show()
      } else if (type === 'edit') {
        // 如果判斷是編輯產品
        this.temp = JSON.parse(JSON.stringify(product)) // 改成深拷貝避免多圖新增、修改、刪除會傳參考修改到 product.imagesUrl
        // 讓新增圖片按鈕可以被渲染
        if (!this.temp.imagesUrl) {
          this.temp.imagesUrl = []
        }
        // modal標題顯示編輯產品
        this.isNew = false
        ProductModal.show()
      } else if (type === 'delete') {
        this.temp = { ...product } // 顯示modal
        DeleteModal.show()
      }
    }
  },
  mounted () {
    this.getProductsList()
  }
}
</script>
