<template>
  <div>
    <h1>(User)產品列表頁面</h1>
    <VueLoading :active="isLoading" />
    <table class="table align-middle">
      <thead>
        <tr>
          <th>圖片</th>
          <th>商品名稱</th>
          <th>價格</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td style="width: 200px">
            <div style="
                height: 100px;
                background-size: cover;
                background-position: center;
              " :style="{ backgroundImage: `url(${item.imageUrl})` }"></div>
          </td>
          <td>
            {{ item.title }}
          </td>
          <td>
            <div class="h5" v-if="!item.price">{{ item.origin_price }} 元</div>
            <del class="h6" v-if="item.price">原價 ${{ item.origin_price }} 元</del>
            <div class="h5" v-if="item.price">現在只要 ${{ item.price }} 元</div>
          </td>
          <td>
            <div class="btn-group btn-group-sm">
              <button type="button" class="btn btn-outline-secondary" @click="getProduct(item.id)"
                :disabled="loadingStatus.loadingItem === item.id">
                <i class="fas fa-spinner fa-pulse" v-if="loadingStatus.loadingItem === item.id"></i>
                產品細項
              </button>
              <button type="button" class="btn btn-outline-danger" @click="addToCart(item.id)"
                :disabled="loadingStatus.loadingItem === item.id">
                <i class="fas fa-spinner fa-pulse" v-if="loadingStatus.loadingItem === item.id"></i>
                加到購物車
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <UserProductModal ref="userProductModal" :product="product" @add-to-cart="addToCart" />
  </div>
</template>

<script>
import UserProductModal from "@/components/UserProductModal.vue";
export default {
  data() {
    return {
      products: [],
      loadingStatus: {
        loadingItem: "",
      },
      isLoading: false,
      product: {},
    };
  },
  components: {
    UserProductModal,
  },
  mounted() {
    this.getProducts();
  },
  methods: {
    addToCart(id, qty = 1) {
      this.isLoading = true;
      const url = `${import.meta.env.VITE_API}api/${import.meta.env.VITE_PATH}/cart`;
      this.loadingStatus.loadingItem = id;
      const cart = {
        product_id: id,
        qty,
      };
      this.$http
        .post(url, { data: cart })
        .then((response) => {
          alert(response.data.message);
          this.loadingStatus.loadingItem = "";
          this.$refs.userProductModal.hideModal();
          this.isLoading = false;
        })
        .catch((err) => {
          alert(err.response.data.message);
        });
    },
    getProducts() {
      this.isLoading = true;
      const url = `${import.meta.env.VITE_API}api/${import.meta.env.VITE_PATH}/products`;
      this.$http
        .get(url)
        .then((response) => {
          this.products = response.data.products;
          this.isLoading = false;
        })
        .catch((err) => {
          alert(err.response.data.message);
        });
    },
    getProduct(id) {
      this.isLoading = true;
      const url = `${import.meta.env.VITE_API}api/${import.meta.env.VITE_PATH}/product/${id}`;
      this.loadingStatus.loadingItem = id;
      this.$http
        .get(url)
        .then((response) => {
          this.loadingStatus.loadingItem = "";
          this.product = response.data.product;
          this.isLoading = false;
          this.$refs.userProductModal.openModal();
        })
        .catch((err) => {
          alert(err.response.data.message);
        });
    },
  },
};
</script>