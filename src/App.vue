<template>
  <div id="app">
    <div class="container">
      <TodoBlock />
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
    TodoBlock: () => ({
      component: import('@/components/todo')
    })
  }
}
</script>

<style>
</style>
