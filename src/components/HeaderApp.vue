<template>
  <section class="">
  <div class="hero-body">
    <h1 class="title">{{ msg }}</h1>
    <p class="subtitle">
      PC 🇮🇹 | Informatica e assistenza
    </p>
  </div>
</section>
</template>

<script>
export default {
  name: 'HeaderApp',
  props: {
    msg: String
  }
}
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #00d1b2;
}
</style>
