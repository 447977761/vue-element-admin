<template>
  <div class="table">
    <el-tabs :tab-position="tabPosition" @tab-click="changeBuildingTabs">
      <el-tab-pane label="全部">
        <el-form :inline="true" :model="formInline">
          <el-form-item label="教室名称" prop="classroomName">
            <el-input v-model="formInline.classroomName" prefix-icon="el-icon-search" clearable placeholder="请输入教室名称"/>
          </el-form-item>
          &nbsp;&nbsp;&nbsp;
          <el-form-item label="是否存在投影仪" prop="isProjector">
            <el-select v-model="formInline.isProjector" clearable placeholder="请选择是否存在投影仪" >
              <el-option
                v-for="item in isFlag"
                :key="item.type"
                :label="item.label"
                :value="item.type"/>
            </el-select>
          </el-form-item>
          &nbsp;&nbsp;&nbsp;
          <el-form-item label="是否有电脑" prop="isComputer">
            <el-select v-model="formInline.isComputer" clearable placeholder="请选择是否有电脑" >
              <el-option
                v-for="item in isFlag"
                :key="item.type"
                :label="item.label"
                :value="item.type"/>
            </el-select>
          </el-form-item>
          &nbsp;&nbsp;&nbsp;
          <el-form-item label="所属大楼">
            <el-select v-model="formInline.buildingCode" clearable placeholder="请选择所属大楼" style="width: 250px">
              <el-option
                v-for="item in buildingInfos"
                :key="item.buildingCode"
                :label="item.buildingName"
                :value="item.buildingCode"/>
            </el-select>
          </el-form-item>
          &nbsp;&nbsp;&nbsp;
          <el-form-item label="期望预约时间">
            <div class="block">
              <el-date-picker
                :picker-options="pickerOptions"
                v-model="formInline.expectedOderDate"
                type="datetimerange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                align="right"/>
            </div>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">查询</el-button>
          </el-form-item>
        </el-form>
        <el-table v-loading="loadingData" :data="tableData" border style="width: 100%" element-loading-text="加载中..." highlight-current-row>
          <el-table-column prop="classroomName" label="教室名称" align="center" min-width="100"/>
          <el-table-column prop="classroomCode" label="教室编码" align="center" min-width="100">
            <template slot-scope="scope">
              <el-tag size="medium">{{ scope.row.classroomCode }}</el-tag>
            </template>
          </el-table-column>
          <el-table-column :formatter="isFlagFormatter" prop="isProjector" label="是否有投影仪" align="center" min-width="120"/>
          <el-table-column :formatter="isFlagFormatter" prop="isComputer" label="是否有电脑" align="center" min-width="100"/>
          <el-table-column prop="buildingName" label="所属大楼" align="center" min-width="150"/>
          <el-table-column :formatter="floorFormatter" prop="floor" label="所属楼层" align="center" min-width="100"/>
          <el-table-column prop="oderCondition" label="预约情况" align="center" min-width="100"/>
          <el-table-column prop="classroomStatus" label="教室状态" align="center" min-width="100"/>
          <el-table-column prop="master" label="负责人" align="center" min-width="100"/>
          <el-table-column prop="latestOder" label="最新预约情况" align="center" min-width="200"/>
          <el-table-column label="操作" width="150" align="center" fixed="right">
            <template slot-scope="scope">
              <el-button
                type="grey"
                size="small"
                @click="handleDetail(scope.$index, scope.row)">详情</el-button>
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
      <el-tab-pane v-for="building in buildingInfos" :label="building.secondName" :key="building.buildingCode" :name="building.buildingCode">
        <el-tabs :tab-position="chilfrentabPosition" @tab-click="changeFloorTabs">
          <el-tab-pane label="全部">
            <el-form :inline="true" :model="formInline">
              <el-form-item label="教室名称" prop="classroomName">
                <el-input v-model="formInline.classroomName" prefix-icon="el-icon-search" clearable placeholder="请输入教室名称"/>
              </el-form-item>
              &nbsp;&nbsp;&nbsp;
              <el-form-item label="是否存在投影仪" prop="isProjector">
                <el-select v-model="formInline.isProjector" clearable placeholder="请选择是否存在投影仪" >
                  <el-option
                    v-for="item in isFlag"
                    :key="item.type"
                    :label="item.label"
                    :value="item.type"/>
                </el-select>
              </el-form-item>
              &nbsp;&nbsp;&nbsp;
              <el-form-item label="是否有电脑" prop="isComputer">
                <el-select v-model="formInline.isComputer" clearable placeholder="请选择是否有电脑" >
                  <el-option
                    v-for="item in isFlag"
                    :key="item.type"
                    :label="item.label"
                    :value="item.type"/>
                </el-select>
              </el-form-item>
              &nbsp;&nbsp;&nbsp;
              <el-form-item label="所属楼层">
                <el-select v-model="formInline.floor" clearable placeholder="请选择所属楼层">
                  <el-option
                    v-for="item in buildingEveryFloors"
                    :key="item"
                    :label="item"
                    :value="item"/>
                </el-select>
              </el-form-item>
              &nbsp;&nbsp;&nbsp;
              <el-form-item label="期望预约时间">
                <div class="block">
                  <el-date-picker
                    :picker-options="pickerOptions"
                    v-model="formInline.expectedOderDate"
                    type="datetimerange"
                    range-separator="至"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期"
                    align="right"/>
                </div>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="onFloorTotalSubmit">查询</el-button>
              </el-form-item>
            </el-form>
            <el-table v-loading="loadingData" :data="totalTableData" style="width: 100%" element-loading-text="加载中..." highlight-current-row>
              <el-table-column prop="classroomName" label="教室名称" align="center" min-width="100"/>
              <el-table-column prop="classroomCode" label="教室编码" align="center" min-width="100">
                <template slot-scope="scope">
                  <el-tag size="medium">{{ scope.row.classroomCode }}</el-tag>
                </template>
              </el-table-column>
              <el-table-column :formatter="isFlagFormatter" prop="isProjector" label="是否有投影仪" align="center" min-width="120"/>
              <el-table-column :formatter="isFlagFormatter" prop="isComputer" label="是否有电脑" align="center" min-width="100"/>
              <el-table-column :formatter="floorFormatter" prop="floor" label="所属楼层" align="center" min-width="100"/>
              <el-table-column prop="oderCondition" label="预约情况" align="center" min-width="100"/>
              <el-table-column prop="classroomStatus" label="教室状态" align="center" min-width="100"/>
              <el-table-column prop="master" label="负责人" align="center" min-width="100"/>
              <el-table-column prop="latestOder" label="最新预约情况" align="center" min-width="200"/>
              <el-table-column label="操作" width="150" align="center" fixed="right">
                <template slot-scope="scope">
                  <el-button
                    type="grey"
                    size="small"
                    @click="handleDetail(scope.$index, scope.row)">详情</el-button>
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
          <el-tab-pane v-for="item in buildingEveryFloors" :label="item" :key="item" :name="item">
            <el-form :inline="true" :model="formInline">
              <el-form-item label="教室名称" prop="classroomName">
                <el-input v-model="formInline.classroomName" prefix-icon="el-icon-search" clearable placeholder="请输入教室名称"/>
              </el-form-item>
              &nbsp;&nbsp;&nbsp;
              <el-form-item label="是否存在投影仪" prop="isProjector">
                <el-select v-model="formInline.isProjector" clearable placeholder="请选择是否存在投影仪" >
                  <el-option
                    v-for="item in isFlag"
                    :key="item.type"
                    :label="item.label"
                    :value="item.type"/>
                </el-select>
              </el-form-item>
              &nbsp;&nbsp;&nbsp;
              <el-form-item label="是否有电脑" prop="isComputer">
                <el-select v-model="formInline.isComputer" clearable placeholder="请选择是否有电脑" >
                  <el-option
                    v-for="item in isFlag"
                    :key="item.type"
                    :label="item.label"
                    :value="item.type"/>
                </el-select>
              </el-form-item>
              &nbsp;&nbsp;&nbsp;
              <el-form-item label="期望预约时间">
                <div class="block">
                  <el-date-picker
                    :picker-options="pickerOptions"
                    v-model="formInline.expectedOderDate"
                    type="datetimerange"
                    range-separator="至"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期"
                    align="right"/>
                </div>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="onFloorSubmit">查询</el-button>
              </el-form-item>
            </el-form>
            <el-table v-loading="loadingData" :data="floorTableData" style="width: 100%" element-loading-text="加载中..." highlight-current-row>
              <el-table-column prop="classroomName" label="教室名称" align="center" min-width="100"/>
              <el-table-column prop="classroomCode" label="教室编码" align="center" min-width="100">
                <template slot-scope="scope">
                  <el-tag size="medium">{{ scope.row.classroomCode }}</el-tag>
                </template>
              </el-table-column>
              <el-table-column :formatter="isFlagFormatter" prop="isProjector" label="是否有投影仪" align="center" min-width="120"/>
              <el-table-column :formatter="isFlagFormatter" prop="isComputer" label="是否有电脑" align="center" min-width="100"/>
              <el-table-column prop="buildingName" label="所属大楼" align="center" min-width="150"/>
              <el-table-column prop="oderCondition" label="预约情况" align="center" min-width="100"/>
              <el-table-column prop="classroomStatus" label="教室状态" align="center" min-width="100"/>
              <el-table-column prop="master" label="负责人" align="center" min-width="100"/>
              <el-table-column prop="latestOder" label="最新预约情况" align="center" min-width="200"/>
              <el-table-column label="操作" width="150" align="center" fixed="right">
                <template slot-scope="scope">
                  <el-button
                    type="grey"
                    size="small"
                    @click="handleDetail(scope.$index, scope.row)">详情</el-button>
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
      pickerOptions: {
        shortcuts: [{
          text: '最近一周',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近一个月',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近三个月',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90)
            picker.$emit('pick', [start, end])
          }
        }]
      },
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
      totalTableData: [],
      formInline: {
        classroomName: '',
        isProjector: '',
        isComputer: '',
        buildingCode: '',
        expectedOderDate: [],
        floor: ''
      },
      isFlag: [{
        type: '1',
        label: '是'
      }, {
        type: '2',
        label: '否'
      }],
      currentBuildingCode: '',
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
    // 楼层页卡处理
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
      this.formInline = {
        classroomName: '',
        isProjector: '',
        isComputer: '',
        buildingCode: '',
        expectedOderDate: []
      }
      this.buildingEveryFloors = []
      for (let i = 0; i < this.buildingInfos.length; i++) {
        if (tab.name === this.buildingInfos[i].buildingCode) {
          this.jdugeFloor(this.buildingInfos[i].floors)
        }
      }
      this.getBuildingClassroom(tab.name)
    },
    // 切换楼层获取数据
    changeFloorTabs: function(tab, event) {
      console.log(tab.name)
      var res = tab.name
      const self = this
      const postDate = {
        buildingCode: this.currentBuildingCode,
        floor: res,
        pageNum: self.filter.page,
        pageSize: self.filter.per_page
      }
      self.$axios.post(self.getAllClassroomListUrl, postDate).then((res) => {
        self.loadingData = false
        if (res.data.success) {
          self.floorTableData = res.data.result.list
          self.total = res.data.result.total
        } else {
          if (res.data.message || res.data.msg) {
            self.floorTableData = []
            self.$message.error(res.data.message || res.data.msg)
          }
        }
      })
    },
    // 获取对应楼下楼层教室
    getBuildingClassroom(buildingCode) {
      const self = this
      self.loadingData = true
      this.currentBuildingCode = buildingCode
      if (buildingCode === 'undefined') {
        this.getAllClassroomList()
      } else {
        const postDate = {
          buildingCode: buildingCode,
          pageNum: self.filter.page,
          pageSize: self.filter.per_page
        }
        self.$axios.post(self.getAllClassroomListUrl, postDate).then((res) => {
          self.loadingData = false
          if (res.data.success) {
            self.totalTableData = res.data.result.list
            self.total = res.data.result.total
          } else {
            if (res.data.message || res.data.msg) {
              self.totalTableData = []
              self.$message.error(res.data.message || res.data.msg)
            }
          }
        })
      }
    },
    // 全部页面查询页面
    onSubmit() {
      const self = this
      var startDate = null
      var endDate = null
      if (self.formInline.expectedOderDate.length > 0) {
        startDate = self.formInline.expectedOderDate[0].getTime()
        endDate = self.formInline.expectedOderDate[1].getTime()
      }
      const postDate = {
        classroomName: self.formInline.classroomName,
        buildingCode: self.formInline.buildingCode,
        isComputer: self.formInline.isComputer,
        isProjector: self.formInline.isProjector,
        startDate: startDate,
        endDate: endDate,
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
    onFloorTotalSubmit() {
      const self = this
      var startDate = null
      var endDate = null
      console.log(self.formInline.expectedOderDate)
      if (self.formInline.expectedOderDate.length > 0) {
        startDate = self.formInline.expectedOderDate[0].getTime()
        endDate = self.formInline.expectedOderDate[1].getTime()
      }
      const postDate = {
        classroomName: self.formInline.classroomName,
        isComputer: self.formInline.isComputer,
        isProjector: self.formInline.isProjector,
        startDate: startDate,
        endDate: endDate,
        pageNum: self.filter.page,
        pageSize: self.filter.per_page,
        floor: self.formInline.floor
      }
      self.loadingData = true
      self.$axios.post(self.getAllClassroomListUrl, postDate).then((res) => {
        self.loadingData = false
        if (res.data.success) {
          self.totalTableData = res.data.result.list
          self.total = res.data.result.total
        } else {
          if (res.data.message || res.data.msg) {
            self.totalTableData = []
            self.$message.error(res.data.message || res.data.msg)
          }
        }
      })
    },
    onFloorSubmit() {
      const self = this
      var startDate = null
      var endDate = null
      console.log(self.formInline.expectedOderDate)
      if (self.formInline.expectedOderDate.length > 0) {
        startDate = self.formInline.expectedOderDate[0].getTime()
        endDate = self.formInline.expectedOderDate[1].getTime()
      }
      const postDate = {
        classroomName: self.formInline.classroomName,
        isComputer: self.formInline.isComputer,
        isProjector: self.formInline.isProjector,
        startDate: startDate,
        endDate: endDate,
        pageNum: self.filter.page,
        pageSize: self.filter.per_page
      }
      self.loadingData = true
      self.$axios.post(self.getAllClassroomListUrl, postDate).then((res) => {
        self.loadingData = false
        if (res.data.success) {
          self.floorTableData = res.data.result.list
          self.total = res.data.result.total
        } else {
          if (res.data.message || res.data.msg) {
            self.floorTableData = []
            self.$message.error(res.data.message || res.data.msg)
          }
        }
      })
    },
    handleDetail(index, row) {
      var id = row.id
      var classroomCode = row.classroomCode
      var classroomName = row.classroomName
      this.$router.push({
        path: '/ClassroomDetail/classroomDetail',
        // params: row,
        query: { id, classroomCode, classroomName }
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
  .el-tabs--left .el-tabs__header.is-left{
    margin-right: 30px;
    margin-top: 70px;
  }
</style>
