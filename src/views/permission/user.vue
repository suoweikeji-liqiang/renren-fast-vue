<template>
  <div class="mod-demo-echarts">

  <el-select v-model="value1" clearable placeholder="所属公司">
      <el-option
        v-for="item in options"
        :key="item.value"
        :label="item.label"
        :value="item.value">
      </el-option>
    </el-select>

    <el-select v-model="value1" clearable placeholder="角色">
      <el-option
        v-for="item in options"
        :key="item.value"
        :label="item.label"
        :value="item.value">
      </el-option>
    </el-select>

    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">

      <el-form-item>
        <el-input v-model="dataForm.userName" placeholder="代理商" clearable></el-input>
      </el-form-item>

      <el-form-item>
        <el-button @click="getDataList()" type="primary" icon="el-icon-search">搜索</el-button>
        <el-button  type="primary" icon="el-icon-refresh">重置</el-button>
        <el-button v-if="isAuth('sys:user:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('sys:user:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>

    </el-form>
   
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">

      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>

      <el-table-column
        fixed="left"
        header-align="center"
        align="center"
        width="150"
        label="操作">

        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.userId)">修改</el-button>
          <el-button v-if="isAuth('sys:user:delete')" type="text" size="small" @click="deleteHandle(scope.row.userId)">删除</el-button>
        </template>

      </el-table-column>

      <el-table-column
        prop="userId"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>

      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="姓名">
      </el-table-column>

      <el-table-column
        prop="email"
        header-align="center"
        align="center"
        label="昵称">
      </el-table-column>

      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="用户名">
      </el-table-column>

      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="所属公司">
        width="100"
      </el-table-column>

      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="180"
        label="冷冻泵总功率">
      </el-table-column>

      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="180"
        label="冷却塔总功率">
      </el-table-column>

      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="180"
        label="冷却泵总功率">
      </el-table-column>

    </el-table>

  </div>

</template>

<script>
  import echarts from 'echarts'
  import AddOrUpdate from './user-add-or-update'
  export default {

    data () {
      return {
        chartLine1: null,
        dataForm: {
          userName: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },

    mounted () {
      this.initchartLine1()
      
    },

    components: {
      AddOrUpdate
    },

    activated () {

      this.getDataList()
      // 由于给echart添加了resize事件, 在组件激活时需要重新resize绘画一次, 否则出现空白bug
      if (this.chartLine1) {
        this.chartLine1.resize()
      }
    },

    methods: {

      // 折线图1
      initchartLine1() {
        var option = {
          'title': {
            'text': '功率（KW）'
          },
          'tooltip': {
            'trigger': 'axis'
          },
          'legend': {
            'data': [ '冷凝器出水溫度', '冷凝器进水温度', '视频广告', '直接访问', '搜索引擎' ]
          },
          'grid': {
            'left': '3%',
            'right': '4%',
            'bottom': '3%',
            'containLabel': true
          },
          'toolbox': {
            'feature': {
              'saveAsImage': { }
            }
          },
          'xAxis': {
            'type': 'category',
            'boundaryGap': false,
            'data': [ '20:00', '00:00', '04:00', '08:00', '12:00', '16:00', '18:00' ]
          },
          'yAxis': {
            'type': 'value'
          },
          'series': [
            {
              'name': '邮件营销',
              'type': 'line',
              'stack': '总量',
              'data': [ 120, 132, 101, 134, 90, 230, 210 ]
            },
            {
              'name': '联盟广告',
              'type': 'line',
              'stack': '总量',
              'data': [ 220, 182, 191, 234, 290, 330, 310 ]
            },
            {
              'name': '视频广告',
              'type': 'line',
              'stack': '总量',
              'data': [ 150, 232, 201, 154, 190, 330, 410 ]
            },
            {
              'name': '直接访问',
              'type': 'line',
              'stack': '总量',
              'data': [ 320, 332, 301, 334, 390, 330, 320 ]
            },
            {
              'name': '搜索引擎',
              'type': 'line',
              'stack': '总量',
              'data': [ 820, 932, 901, 934, 1290, 1330, 1320 ]
            }
          ]
        }
        this.chartLine1 = echarts.init(document.getElementById('J_chartLineBox'))
        this.chartLine1.setOption(option)
        window.addEventListener('resize', () => {
          this.chartLine1.resize()
        })
      },
       // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/sys/user/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'username': this.dataForm.userName
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var userIds = id ? [id] : this.dataListSelections.map(item => {
          return item.userId
        })
        this.$confirm(`确定对[id=${userIds.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/sys/user/delete'),
            method: 'post',
            data: this.$http.adornData(userIds, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {})
      }
    }
  }
</script>

<style lang="scss">
  .mod-demo-echarts {
    > .el-alert {
      margin-bottom: 10px;
    }
    > .el-row {
      margin-top: -10px;
      margin-bottom: -10px;
      .el-col {
        padding-top: 10px;
        padding-bottom: 10px;
      }
    }
    .chart-box {
      min-height: 400px;
    }
  }
</style>
