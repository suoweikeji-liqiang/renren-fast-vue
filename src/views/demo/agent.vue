<template>

  <div class="mod-agent">

    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">

      <el-form-item>
        <el-input v-model="dataForm.hotelcode" placeholder="编码" clearable></el-input>
      </el-form-item>

      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('sys:user:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('sys:user:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
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
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>

      <el-table-column
        prop="hotelid"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>

      <el-table-column
        prop="hotelname"
        header-align="center"
        align="center"
        label="酒店名称">
      </el-table-column>

      <el-table-column
        prop="hotelcode"
        header-align="center"
        align="center"
        label="编码">
      </el-table-column>

      <el-table-column
        prop="contact"
        header-align="center"
        align="center"
        label="联系人">
      </el-table-column>

      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="联系人电话">
        width="100"
      </el-table-column>

      <el-table-column
        prop="hoteladdress"
        header-align="center"
        align="center"
        width="180"
        label="地址">
      </el-table-column>

      <el-table-column
        prop="remarks"
        header-align="center"
        align="center"
        width="180"
        label="备注">
      </el-table-column>

      <el-table-column
        prop="start"
        header-align="center"
        align="center"
        label="是否启用">

        <template slot-scope="scope">
          <el-tag v-if="scope.row.start === 0" size="small" type="danger">禁用</el-tag>
          <el-tag v-else size="small">启用</el-tag>
        </template>

      </el-table-column>

      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">

        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.hotelid)">修改</el-button>
          <el-button v-if="isAuth('sys:user:delete')" type="text" size="small" @click="deleteHandle(scope.row.hotelid)">删除</el-button>
        </template>

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
  import AddOrUpdate from './agent-add-or-update'
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
        addOrUpdateVisible: false
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
          url: this.$http.adornUrl('/Internetofthings/Supplier/showAllSupplier'),
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
        var hotelids = id ? [id] : this.dataListSelections.map(item => {
          return item.hotelid
        })
        this.$confirm(`确定对[id=${hotelids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/Internetofthings/Supplier/deleteBysupplier'),
            method: 'post',
            data: this.$http.adornData(hotelids, false)
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
            url: this.$http.adornUrl('/Internetofthings/Supplier/exportSupplier'),
            method: 'post',
            data: this.$http.adornData({
                'userId': this.dataForm.id || undefined,
                'hotelcode': this.dataForm.hotelcode,
                'hotelname': this.dataForm.hotelname,
                'contact': this.dataForm.contact,
                'mobile': this.dataForm.mobile,
                'hoteladdress': this.dataForm.hoteladdress,
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
