<template>
  <div class="about">
    <h1>單一產品頁面(產品細項)</h1>
    {{ product.title }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      product: {},
    };
  },
  mounted() {
    const { id } = this.$route.params;
    const url = `${import.meta.env.VITE_API}api/${import.meta.env.VITE_PATH}/product/${id}`;
    this.$http
      .get(url)
      .then((response) => {
        this.product = response.data.product;
      })
      .catch((err) => {
        alert(err.response.data.message);
      });
  },
};
</script>