<template>
    <AuthComponent msg="Welcome to Your Vue.js App"/>
</template>

<script>
// @ is an alias to /src
import AuthComponent from '@/components/AuthComponent.vue'

export default {
  name: 'Auth',
  components: {
    AuthComponent
  }
}
</script>
