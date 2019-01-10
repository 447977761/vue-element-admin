<template>
  <div class="table">
    <el-tabs :tab-position="tabPosition">
      <el-tab-pane label="全部">
        <el-table v-loading="loadingData" :data="tableData" border style="width: 100%" element-loading-text="加载中..." highlight-current-row>
          <el-table-column prop="classroomName" label="教室名称" align="center"/>
          <el-table-column prop="classroomCode" label="教室编码" align="center"/>
          <el-table-column prop="isProjector" label="是否有投影仪" align="center"/>
          <el-table-column prop="isComputer" label="是否有电脑" align="center"/>
          <el-table-column prop="buildingCode" label="所属大楼" align="center"/>
          <el-table-column prop="floor" label="所属楼层" align="center"/>
          <el-table-column prop="oderCondition" label="预约情况" align="center"/>
          <el-table-column prop="classroomStatus" label="教室状态" align="center"/>
          <el-table-column prop="master" label="负责人" align="center"/>
          <el-table-column prop="latestOder" label="最新预约情况" align="center"/>
          <el-table-column label="操作" width="150" align="center">
            <template slot-scope="scope">
              <el-button
                type="grey"
                size="small"
                @click="handleEdit(scope.$index, scope.row)">详情</el-button>
              <el-button
                type="danger"
                size="small"
                @click="handleDelete(scope.$index, scope.row)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <div class="pagination">
          <el-pagination
            :total="total"
            :current-page="filter.page"
            :page-sizes="[10, 20, 50, 100]"
            :page-size="filter.per_page"
            background
            layout="total, sizes, prev, pager, next, jumper"
            @size-change="pageSizeChange"
            @current-change="pageCurrentChange"/>
        </div>
      </el-tab-pane>
      <el-tab-pane label="经外楼">
        <el-tabs :tab-position="chilfrentabPosition">
          <el-tab-pane label="一楼">
            <el-table v-loading="loadingData" :data="tableData" border style="width: 100%" element-loading-text="加载中..." highlight-current-row>
              <el-table-column prop="classroomName" label="教室名称" align="center"/>
              <el-table-column prop="classroomCode" label="教室编码" align="center"/>
              <el-table-column prop="isProjector" label="是否有投影仪" align="center"/>
              <el-table-column prop="isComputer" label="是否有电脑" align="center"/>
              <el-table-column prop="floor" label="所属楼层" align="center"/>
              <el-table-column prop="oderCondition" label="预约情况" align="center"/>
              <el-table-column prop="master" label="负责人" align="center"/>
              <el-table-column prop="latestOder" label="最新预约情况" align="center"/>
              <el-table-column label="操作" width="150" align="center">
                <template slot-scope="scope">
                  <el-button
                    type="grey"
                    size="small"
                    @click="handleEdit(scope.$index, scope.row)">详情</el-button>
                  <el-button
                    type="danger"
                    size="small"
                    @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="pagination">
              <el-pagination
                :total="total"
                :current-page="filter.page"
                :page-sizes="[10, 20, 50, 100]"
                :page-size="filter.per_page"
                background
                layout="total, sizes, prev, pager, next, jumper"
                @size-change="pageSizeChange"
                @current-change="pageCurrentChange"/>
            </div>
          </el-tab-pane>
          <el-tab-pane label="二楼">二楼</el-tab-pane>
          <el-tab-pane label="三楼">三楼</el-tab-pane>
          <el-tab-pane label="四楼">四楼</el-tab-pane>
        </el-tabs>
      </el-tab-pane>
      <el-tab-pane label="配置管理">配置管理</el-tab-pane>
      <el-tab-pane label="角色管理">角色管理</el-tab-pane>
      <el-tab-pane label="定时任务补偿">定时任务补偿</el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>

export default {

  data() {
    return {
      testConnectUrl: '/shiep/crm/test',
      tabPosition: 'top',
      chilfrentabPosition: 'left',
      filter: {
        per_page: 10, // 页大小
        page: 1 // 当前页
      },
      total: 0,
      loadingData: false,
      tableData: [],
      JWtableData: [],
      getAllClassroomListUrl: '/shiep/crm/allClassroomList'
    }
  },
  created() {
    this.getAllClassroomList()
  },
  methods: {
    // 查询所有教室
    getAllClassroomList() {
      const self = this
      const postDate = {
        pageNum: self.filter.page,
        pageSize: self.filter.per_page
      }
      self.loadingData = true
      self.$axios.post(self.getAllClassroomListUrl, postDate).then((res) => {
        self.loadingData = false
        if (res.data.success) {
          self.tableData = res.data.result.list
          self.total = res.data.result.total
        } else {
          if (res.data.message || res.data.msg) {
            self.tableData = []
            self.$message.error(res.data.message || res.data.msg)
          }
        }
      })
    },
    pageSizeChange(val) {
      console.log(`每页 ${val} 条`)
      this.filter.per_page = val
    },
    pageCurrentChange(val) {
      console.log(`当前页: ${val}`)
      this.filter.page = val
    }
  }
}
</script>

<style>

</style>
