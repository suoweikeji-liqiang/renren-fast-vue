<template>

  <div class="mod-agent">

    <el-select v-model="value1" clearable placeholder="代理商">
      <el-option
        v-for="item in options"
        :key="item.value1"
        :label="item.label"
        :value1="item.value1">
      </el-option>
    </el-select>

    <el-select v-model="value2" clearable placeholder="客户">
      <el-option
        v-for="item in options"
        :key="item.value2"
        :label="item.label"
        :value2="item.value2">
      </el-option>
    </el-select>

    <el-select v-model="value3" clearable placeholder="地点">
      <el-option
        v-for="item in options"
        :key="item.value3"
        :label="item.label"
        :value3="item.value3">
      </el-option>
    </el-select>

    <el-date-picker
      v-model="value4"
      type="daterange"
      align="right"
      unlink-panels
     
      start-placeholder="开始日期"
      end-placeholder="结束日期"
      :picker-options="pickerOptions2">
    </el-date-picker>

    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">

      <el-form-item>
        <el-input v-model="dataForm.userName" placeholder="编码或名称" clearable></el-input>
      </el-form-item>

      <el-form-item>
        <el-button @click="getDataList()" type="primary" icon="el-icon-search">搜索</el-button>
        <el-button  type="primary" icon="el-icon-refresh">重置</el-button>
        <el-button  type="primary" @click="exportHandle()">导出Excel表</el-button>
        <el-checkbox v-model="checked" size="medium" >自动刷新</el-checkbox>
      </el-form-item>

    </el-form>

    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      
      <el-table-column
        prop="datetime"
        header-align="center"
        align="center"
        label="日期">
      </el-table-column>

      <el-table-column
        prop="elecconsum"
        header-align="center"
        align="center"
        label="日用电量Kwh">
      </el-table-column>

      <el-table-column
        prop="hightemp"
        header-align="center"
        align="center"
        label="当日最高气温℃">
      </el-table-column>

      <el-table-column
        prop="lowtemp"
        header-align="center"
        align="center"
        label="当日最低气温℃">
        width="100"
      </el-table-column>

      <el-table-column
        prop="equipelec"
        header-align="center"
        align="center"
        width="180"
        label="当日设备用电量Kwh">
      </el-table-column>

    </el-table>

    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>

    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
 
  </div>

</template>

<script>
  import AddOrUpdate from './user-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          userName: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,

         pickerOptions2: {
          shortcuts: [{
            text: '最近一周',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近一个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近三个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit('pick', [start, end]);
            }
          }]
        },
        value4: ''

      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/Internetofthings/StatisticalTemperature/showAllStatisticalTemperature'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'hotelcode': this.dataForm.hotelcode
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.content
            this.totalPage = data.ext.size
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
      },

//导出
      exportHandle(){
          var pash=""
          this.$http({
            url: this.$http.adornUrl('/Internetofthings/StatisticalTemperature/exportStatisticalTemperature'),
            method: 'post',
            data: this.$http.adornData({
                'userId': this.dataForm.id || undefined,
                'hightemp': this.dataForm.hightemp,
                'lowtemp': this.dataForm.lowtemp,
                'datetime': this.dataForm.datetime,
                'elecconsum': this.dataForm.elecconsum,
                'equipelec': this.dataForm.equipelec,
                'remarks': this.dataForm.remarks,
                'password': this.dataForm.password,
                'salt': this.dataForm.salt,
                'email': this.dataForm.email,
                'start': this.dataForm.start,
                'roleIdList': this.dataForm.roleIdList
              }) 
          }).then(({data}) => {
            if (data && data.code === 0) {
              pash = data.content;
              window.open(pash);
            } else {
              this.$message.error(data.msg)
            }
          }).then(() => {
          console.log('测试');
        }).catch(() => {})
      }

    }
  }
</script>
