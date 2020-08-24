<template>

</template>

<script>
  export default {
    name: 'logout',
    created() {
      this.$store.dispatch('clearTodos')
      this.$store.dispatch('destroyToken')
        .then(response => {
          this.$router.push({ name: 'home' })
        })
    }
  }
</script>
