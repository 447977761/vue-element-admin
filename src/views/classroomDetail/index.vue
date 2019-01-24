<template>
  <div>
    <div class="table">
      <p>
        <strong style="font-size: 20px;color: #20a0ff">{{ classroomName }}</strong>
        <span style="font-size: 14px;margin-left: 5px">基本信息</span>
        <el-tooltip class="item" effect="dark" content="教室状态正常" placement="right">
          <i style="color: greenyellow" class="el-icon-success"/>
        </el-tooltip>
      </p>
      <el-row>
        <el-col :span="8">111</el-col>
        <el-col :span="8">222</el-col>
        <el-col :span="8">333</el-col>
      </el-row>
      <el-button @click="test">测试</el-button>
    </div>
    <div class="table bt-table">
      下方详细
    </div>
  </div>
</template>

<script>

export default {

  data() {
    const params = this.$route.query
    return {
      id: params.id,
      classroomCode: params.classroomCode,
      classroomName: params.classroomName
    }
  },
  created() {

  },
  mounted() {

  },
  methods: {
    test() {
      alert(this.id)
    }
  }
}
</script>

<style>
.bt-table{
  margin-top: 10px;
}

</style>
