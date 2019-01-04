<template>
  <div class="dashboard-container">
    <el-button type="primary" @click="getDate">测试链接</el-button>
  </div>
</template>

<script>

export default {

  data() {
    return {
      testConnectUrl: '/shiep/crm/test'
    }
  },
  created() {
  },
  methods: {
    getDate() {
      const self = this
      const postDate = {
        testWord: 'testConnect'
      }
      self.$axios.post(self.testConnectUrl, postDate).then((res) => {
        if (res.data.success) {
          self.$message.success(res.data.message)
        } else {
          if (res.data.message || res.data.msg) {
            self.$message.error(res.data.message || res.data.msg)
          }
        }
      })
    }
  }
}
</script>
