<template>

  <div class="mod-equipment">

    <el-select v-model="value1" clearable placeholder="是否已配置">
      <el-option
        v-for="item in options"
        :key="item.value1"
        :label="item.label"
        :value="item.value1">
      </el-option>
    </el-select>

    <el-select v-model="value2" clearable placeholder="请选择代理商">
      <el-option
        v-for="item in options"
        :key="item.value2"
        :label="item.label"
        :value="item.value2">
      </el-option>
    </el-select>

    <el-select v-model="value3" clearable placeholder="请选择客户">
      <el-option
        v-for="item in options"
        :key="item.value3"
        :label="item.label"
        :value="item.value3">
      </el-option>
    </el-select>

    <el-select v-model="value4" clearable placeholder="请选择地点">
      <el-option
        v-for="item in options"
        :key="item.value4"
        :label="item.label"
        :value="item.value4">
      </el-option>
    </el-select>

    <el-select v-model="value5" clearable placeholder="请选择网关">
      <el-option
        v-for="item in options"
        :key="item.value5"
        :label="item.label"
        :value="item.value5">
      </el-option>
    </el-select>

    <el-select v-model="value6" clearable placeholder="请选择设备型号">
      <el-option
        v-for="item in options"
        :key="item.value6"
        :label="item.label"
        :value="item.value6">
      </el-option>
    </el-select>

    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">

      <el-form-item>
        <el-input v-model="dataForm.userName" placeholder="编码或名称" clearable></el-input>
      </el-form-item>

      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('sys:user:save')" type="primary" @click="addOrUpdateHandle()">添加设备</el-button>
        <el-button v-if="isAuth('sys:user:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
        <el-button  type="primary"  @click="exportHandle()">导出Excel表</el-button>
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
        width="50"
        align="center"
        >
      </el-table-column>

      <el-table-column
        prop="equipid"
        fixed="left"
        header-align="center"
        align="center"
        width="60"
        label="ID">
      </el-table-column>

      <el-table-column
        prop="equipname"
        fixed="left"
        header-align="center"
        align="center"
        width="100"
        label="设备名称">
      </el-table-column>

      <el-table-column
        prop="equipcode"
        header-align="center"
        align="center"
        width="80"
        label="编码">
      </el-table-column>

      <el-table-column
        prop="onlinestatu"
        header-align="center"
        align="center"
        width="100"
        label="在线状态">
      </el-table-column>

      <el-table-column
        prop="datatable"
        header-align="center"
        align="center"
        width="100"
        label="实时数据表名">
      </el-table-column>

      <el-table-column
        prop="statuinfor"
        header-align="center"
        align="center"
        width="100"
        label="状态消息">
      </el-table-column>

      <el-table-column
        prop="showcurve"
        header-align="center"
        align="center"
        width="120"
        label="是否在曲线面板中显示">

        <template slot-scope="scope">
          <el-tag v-if="scope.row.showcurve === 0" size="small" type="danger">禁用</el-tag>
          <el-tag v-else size="small">显示</el-tag>
        </template>

      </el-table-column>

      <el-table-column
        prop="callinfor"
        header-align="center"
        align="center"
        width="100"
        label="报警消息">
      </el-table-column>

      <el-table-column
        prop="efficient"
        header-align="center"
        align="center"
        width="100"
        label="能耗类别">
      </el-table-column>

      <el-table-column
        prop="equipmodel"
        header-align="center"
        align="center"
        width="100"
        label="设备模板">
      </el-table-column>

      <el-table-column
        prop="remarks"
        header-align="center"
        align="center"
        width="100"
        label="备注">
      </el-table-column>

      <el-table-column
        prop="equiptime"
        header-align="center"
        align="center"
        width="120"
        label="设备状态更新时间">
      </el-table-column>

      <el-table-column
        prop="servertime"
        header-align="center"
        align="center"
        width="120"
        label="服务器接收时间">
      </el-table-column>

     <el-table-column
        prop="gateway"
        header-align="center"
        align="center"
        width="100"
        label="所属网关">
      </el-table-column>

      <el-table-column
        prop="hotelid"
        header-align="center"
        align="center"
        width="100"
        label="所属客户">
      </el-table-column>

      <el-table-column
        prop="hotelname"
        header-align="center"
        align="center"
        width="100"
        label="所属楼宇">
      </el-table-column>

      <el-table-column
        prop="groupid"
        header-align="center"
        align="center"
        width="120"
        label="设备分组ID">
      </el-table-column>

      <el-table-column
        prop="equipvirtual"
        header-align="center"
        align="center"
        width="120"
        label="虚拟设备计算配置">
      </el-table-column>

      <el-table-column
        prop="image"
        header-align="center"
        align="center"
        width="100"
        label="图片url">
      </el-table-column>     

      <el-table-column
        prop="equipextend"
        header-align="center"
        align="center"
        width="120"
        label="设备扩展属性 ">
      </el-table-column>

      <el-table-column
        prop="virtual"
        header-align="center"
        align="center"
        width="120"
        label="是否虚拟设备">
        
        <template slot-scope="scope">
          <el-tag v-if="scope.row.virtual === 0" size="small" type="danger">否</el-tag>
          <el-tag v-else size="small">是</el-tag>
        </template>

      </el-table-column>

      <el-table-column
        prop="equiporder"
        header-align="center"
        align="center"
        width="120"
        label="虚拟设备计算顺序">
      </el-table-column>

      <el-table-column
        prop="offlinetime"
        header-align="center"
        align="center"
        width="100"
        label="设备离线时间">
      </el-table-column>  

      <el-table-column
        prop="listdefinition"
        header-align="center"
        align="center"
        width="150"
        label="实时数据显示数据时，列定义">
      </el-table-column> 

      <el-table-column
        prop="showorder"
        header-align="center"
        align="center"
        width="100"
        label="展示顺序">
      </el-table-column> 

      <el-table-column
        prop="start"
        header-align="center"
        align="center"
        width="100"
        label="是否启用">
        
        <template slot-scope="scope">
          <el-tag v-if="scope.row.start === 0" size="small" type="danger">禁用</el-tag>
          <el-tag v-else size="small">启用</el-tag>
        </template>

      </el-table-column>

      <el-table-column
        prop="createtime"
        header-align="center"
        align="center"
        width="100"
        label="创建时间">
      </el-table-column>

      <el-table-column
        prop="founder"
        header-align="center"
        align="center"
        width="100"
        label="创建人">
      </el-table-column>

      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="100"
        label="操作">

        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.equipid)">修改</el-button>
          <el-button v-if="isAuth('sys:user:delete')" type="text" size="small" @click="deleteHandle(scope.row.equipid)">删除</el-button>
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
  import AddOrUpdate from './equipment-add-or-update'
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
          url: this.$http.adornUrl('/Internetofthings/Equip/showAllEquip'),
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
        var equipids = id ? [id] : this.dataListSelections.map(item => {
          return item.equipid
        })
        this.$confirm(`确定对[id=${equipids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/Internetofthings/Equip/deleteByEquip'),
            method: 'post',
            data: this.$http.adornData(equipids, false)
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
            url: this.$http.adornUrl('/Internetofthings/Equip/exportSupplier'),
            method: 'post',
            data: this.$http.adornData({
                'equipid': this.dataForm.id || undefined,
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
