<template>
  <nav class="p-2 text-white color-bg">
    <div class="d-flex justify-content-center">
      <h3 class="font-weight-bold text-uppercase">Actividad I - Crud Vue</h3>
    </div>
  </nav>
</template>

<script>
export default {
  name: "Navbar",
};
</script>
<style  scoped>
.color-bg {
  background: #2a8e68;
}
@media screen and (max-width: 380px) {
  nav {
    width: 147%;
  }
}
</style>
