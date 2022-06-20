<template>
  <div class="home">

    <ApiClient msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import ApiClient from '@/components/ApiClient.vue'

export default {
  name: 'HomeView',
  components: {
    ApiClient
  }
}
</script>
