<template>
  <div>
    <h1>你現在在後台頁面</h1>
    <div id="nav">
      <RouterLink to="/">回到前台</RouterLink> |
      <RouterLink to="/admin/products">後台產品列表</RouterLink> |
      <RouterLink to="/admin/orders">後台訂單</RouterLink> |
      <a href="#" @click.prevent="signout">登出</a>
    </div>
    <RouterView v-if="checkSuccess" />
  </div>
</template>

<script>
// 驗證可以寫這邊
export default {
  data() {
    return {
      checkSuccess: false,
    };
  },
  mounted() {
    this.checkLogin();
  },
  methods: {
    checkLogin() {
      const token = document.cookie.replace(
        /(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/,
        "$1"
      );
      if (token) {
        // Axios 預設值
        this.$http.defaults.headers.common.Authorization = `${token}`;
        const api = `${import.meta.env.VITE_API}api/user/check`;
        this.$http
          .post(api, { api_token: this.token })
          .then(() => {
            this.checkSuccess = true;
          })
          .catch((err) => {
            alert(err.response.data.message);
            this.$router.push("/login");
          });
      } else {
        alert("您尚未登入。");
        this.$router.push("/login");
      }
    },
    signout() {
      document.cookie = "hexToken=;expires=;";
      alert("token 已清除");
      this.$router.push("/login");
    },
  },
};
</script>