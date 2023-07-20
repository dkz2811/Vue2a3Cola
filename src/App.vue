<script setup>
import HeaderComp from './components/HeaderComp.vue'
import RegisterComp from './components/RegisterComp.vue'
</script>

<template>
  <div>
    <header>
      <div class="wrapper">
        <HeaderComp titulo="Forms con Vue3" />
      </div>
    </header>
    <main>
      <RegisterComp/>
    </main>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }
  
  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
