<template>
  <div class="dashboard-container">
    这是首页
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'Dashboard',

  data() {
    return {

    }
  },
  computed: {
    ...mapGetters([
      'name',
      'roles'
    ])
  },
  created() {

  }
}
</script>
