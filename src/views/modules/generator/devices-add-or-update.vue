<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="设备ID" prop="deviceid">
      <el-input v-model="dataForm.deviceid" placeholder="设备ID"></el-input>
    </el-form-item>
    <el-form-item label="设备类型" prop="type">
      <el-input v-model="dataForm.type" placeholder="设备类型"></el-input>
    </el-form-item>
    <el-form-item label="网关ID" prop="gatewayid">
      <el-input v-model="dataForm.gatewayid" placeholder="网关ID"></el-input>
    </el-form-item>
    <el-form-item label="设备名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="设备名称"></el-input>
    </el-form-item>
    <el-form-item label="设备说明" prop="intro">
      <el-input v-model="dataForm.intro" placeholder="设备说明"></el-input>
    </el-form-item>
    <el-form-item label="设备状态" prop="status">
      <el-input v-model="dataForm.status" placeholder="设备状态"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updatetime">
      <el-input v-model="dataForm.updatetime" placeholder="更新时间"></el-input>
    </el-form-item>
    <el-form-item label="设备数据" prop="data">
      <el-input v-model="dataForm.data" placeholder="设备数据"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          deviceid: '',
          type: '',
          gatewayid: '',
          name: '',
          intro: '',
          status: '',
          updatetime: '',
          data: ''
        },
        dataRule: {
          deviceid: [
            { required: true, message: '设备ID不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '设备类型不能为空', trigger: 'blur' }
          ],
          gatewayid: [
            { required: true, message: '网关ID不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '设备名称不能为空', trigger: 'blur' }
          ],
          intro: [
            { required: true, message: '设备说明不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '设备状态不能为空', trigger: 'blur' }
          ],
          updatetime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ],
          data: [
            { required: true, message: '设备数据不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.deviceid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.deviceid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/devices/info/${this.dataForm.deviceid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.deviceid = data.devices.deviceid
                this.dataForm.type = data.devices.type
                this.dataForm.gatewayid = data.devices.gatewayid
                this.dataForm.name = data.devices.name
                this.dataForm.intro = data.devices.intro
                this.dataForm.status = data.devices.status
                this.dataForm.updatetime = data.devices.updatetime
                this.dataForm.data = data.devices.data
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/devices/${'save'}`),
              method: 'post',
              data: this.$http.adornData({
                'deviceid': this.dataForm.deviceid,
                'type': this.dataForm.type,
                'gatewayid': this.dataForm.gatewayid,
                'name': this.dataForm.name,
                'intro': this.dataForm.intro,
                'status': this.dataForm.status,
                'updatetime': this.dataForm.updatetime,
                'data': this.dataForm.data
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
