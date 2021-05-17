<template>
  <footer class="fixed-footer color-bg">
    <p>UVM &copy; {{ fecha }} - Frontend II</p>
  </footer>
</template>

<script>
export default {
  name: "Footer",
  computed: {
    fecha: function () {
      return new Date().getFullYear();
    },
  },
};
</script>

<style scoped>
.fixed-footer {
  font-weight: bold;
  color: #fff;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  font-size: 1.5em;
  text-align: center;
  padding: 5px;
  background: #2a8e68;
}
</style>