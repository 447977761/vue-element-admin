<template>
  <div class="table">
    <el-tabs :tab-position="tabPosition" @tab-click="changeBuildingTabs">
      <el-tab-pane label="全部">
        <el-table v-loading="loadingData" :data="tableData" border style="width: 100%" element-loading-text="加载中..." highlight-current-row>
          <el-table-column prop="classroomName" label="教室名称" align="center"/>
          <el-table-column prop="classroomCode" label="教室编码" align="center"/>
          <el-table-column :formatter="isFlagFormatter" prop="isProjector" label="是否有投影仪" align="center"/>
          <el-table-column :formatter="isFlagFormatter" prop="isComputer" label="是否有电脑" align="center"/>
          <el-table-column prop="buildingName" label="所属大楼" align="center" min-width="130"/>
          <el-table-column :formatter="floorFormatter" prop="floor" label="所属楼层" align="center"/>
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
      <el-tab-pane v-for="building in buildingInfos" :label="building.secondName" :key="building.buildingCode">
        <el-tabs :tab-position="chilfrentabPosition" @tab-click="changeFloorTabs">
          <el-tab-pane v-for="item in buildingEveryFloors" :label="item" :key="item">
            <el-table v-loading="loadingData" :data="floorTableData" border style="width: 100%" element-loading-text="加载中..." highlight-current-row>
              <el-table-column prop="classroomName" label="教室名称" align="center"/>
              <el-table-column prop="classroomCode" label="教室编码" align="center"/>
              <el-table-column :formatter="isFlagFormatter" prop="isProjector" label="是否有投影仪" align="center"/>
              <el-table-column :formatter="isFlagFormatter" prop="isComputer" label="是否有电脑" align="center"/>
              <el-table-column prop="buildingName" label="所属大楼" align="center" min-width="130"/>
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
        </el-tabs>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import consts from '@/views/const/consts.js'
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
      buildingInfos: [],
      buildingEveryFloors: [],
      floorTableData: [],
      getAllClassroomListUrl: '/shiep/crm/allClassroomList',
      getAllBuildingListUrl: '/shiep/crm/allBuildingList'
    }
  },
  created() {

  },
  mounted() {
    this.getAllClassroomList()
    this.getAllBuildingList()
    this.jdugeFloor(this.buildingInfos['floors'])
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
    // 查询所有楼幢
    getAllBuildingList() {
      const self = this
      self.loadingData = true
      self.$axios.post(self.getAllBuildingListUrl).then((res) => {
        self.loadingData = false
        if (res.data.success) {
          self.buildingInfos = res.data.result
        } else {
          if (res.data.message || res.data.msg) {
            self.buildingInfos = []
            self.$message.error(res.data.message || res.data.msg)
          }
        }
      })
    },
    // 列处理
    isFlagFormatter: function(row, column) {
      var isProjector = row[column.property]
      if (isProjector) {
        var types = consts.isFlag.filter(function(v, i, arr) {
          if (v.type === isProjector) {
            return true
          }
        })
        if (types[0]) {
          return types[0].label
        }
        return isProjector
      } else {
        return ''
      }
    },
    // 楼层列处理
    floorFormatter: function(row, column) {
      var floor = row[column.property]
      if (floor) {
        var types = consts.floors.filter(function(v, i, arr) {
          if (v.type === floor) {
            return true
          }
        })
        if (types[0]) {
          return types[0].label
        }
        return floor
      } else {
        return ''
      }
    },
    jdugeFloor(item) {
      console.log(item)
      if (item) {
        for (let j = 1; j <= item; j++) {
          if (j) {
            var types = consts.floors.filter(function(v, i, arr) {
              if (v.type === j.toString()) {
                return true
              }
            })
            if (types[0]) {
              this.buildingEveryFloors.push(types[0].label)
            }
          } else {
            return ''
          }
        }
      }
    },
    // 处理每幢楼
    changeBuildingTabs: function(tab, event) {
      this.buildingEveryFloors = []
      for (let i = 0; i < this.buildingInfos.length; i++) {
        if (event.target.getAttribute('id') === this.buildingInfos[i].tabId) {
          this.jdugeFloor(this.buildingInfos[i].floors)
        }
      }
    },
    // 切换楼层获取数据
    changeFloorTabs: function(tab, event) {
      var str = event.target.getAttribute('id')
      var strArr = str.split('-')
      var res = (parseInt(strArr[1]) + 1).toString()
      console.log(res)
    },
    // 获取对应楼下楼层教室  todo 做个表监视器用来监视哪些楼层可用
    getBuildingClassroom(item) {
      const self = this
      const postDate = {
        tabId: item
      }
      self.loadingData = true
      self.$axios.post(self.getAllBuildingListUrl, postDate).then((res) => {
        self.loadingData = false
        if (res.data.success) {
          self.buildingInfos = res.data.result
        } else {
          if (res.data.message || res.data.msg) {
            self.buildingInfos = []
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
