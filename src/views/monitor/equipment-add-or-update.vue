<template>

  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">

    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">



      <el-form-item label="编码" prop="equipcode">
        <el-input v-model="dataForm.equipcode" placeholder=""></el-input>
      </el-form-item>

      
      <el-form-item label="设备名称" prop="equipname">
        <el-input v-model="dataForm.equipname" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="所属客户" prop="hotelid">
        <el-input v-model="dataForm.hotelid" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="所在地点" prop="hoteladdress">
        <el-input v-model="dataForm.hoteladdress" placeholder="地址"></el-input>
      </el-form-item>
      
      <el-form-item label="设备模板" prop="equipmodel">
        <el-input v-model="dataForm.equipmodel" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="能耗类别" prop="efficient">
        <el-input v-model="dataForm.efficient" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="所属网关" prop="gateway">
        <el-input v-model="dataForm.gateway" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="分组" prop="groupid">
        <el-input v-model="dataForm.groupid" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="设备离线时间" prop="offlinetime">
        <el-input v-model="dataForm.offlinetime" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="备注" prop="remarks">
        <el-input v-model="dataForm.remarks" placeholder=""></el-input>
      </el-form-item>
      
      <el-form-item label="是否在曲线面板中显示" size="mini" prop="showcurve">
        <el-radio-group v-model="dataForm.showcurve">
          <el-radio :label="0">不显示</el-radio>
          <el-radio :label="1">显示</el-radio>
        </el-radio-group>
      </el-form-item>

      <el-form-item label="是否启用" size="mini" prop="start">
        <el-radio-group v-model="dataForm.start">
          <el-radio :label="0">禁用</el-radio>
          <el-radio :label="1">启用</el-radio>
        </el-radio-group>
      </el-form-item>

    </el-form>

    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>

  </el-dialog>

</template>

<script>
  import { isEmail, isMobile } from '@/utils/validate'
  export default {

    data () 
    {
      var validatePassword = (rule, value, callback) => 
      {
        if (!this.dataForm.id && !/\S/.test(value)) 
        {
          callback(new Error('密码不能为空'))
        } 
        else 
        {
          callback()
        }
      }

      var validateComfirmPassword = (rule, value, callback) => 
      {
        if (!this.dataForm.id && !/\S/.test(value)) 
        {
          callback(new Error('确认密码不能为空'))
        } else if (this.dataForm.password !== value) 
        {
          callback(new Error('确认密码与密码输入不一致'))
        } 
        else 
        {
          callback()
        }
      }

      var validateEmail = (rule, value, callback) => 
      {
        if (!isEmail(value)) 
        {
          callback(new Error('邮箱格式错误'))
        } 
        else 
        {
          callback()
        }
      }

      var validateMobile = (rule, value, callback) => 
      {
        if (!isMobile(value)) 
        {
          callback(new Error('手机号格式错误'))
        } 
        else 
        {
          callback()
        }
      }
      
      return {
        visible: false,
        roleList: [],
        dataForm: {
          id: 0,
          equipcode: '',
          equipname: '',
          hotelid: '',
          hoteladdress: '',
          remarks: '',
          password: '',
          comfirmPassword: '',
          salt: '',
          efficient: '',
          gateway: '',
          groupid: '',
          offlinetime: '',
          equipmodel: '',
         
          roleIdList: [],
          showcurve: 1,
          start: 1
        },

        dataRule: {
          equipcode: [
            { required: true, message: '编码不能为空', trigger: 'blur' }
          ],

          equipname: [
            { required: true, message: '设备名称不能为空', trigger: 'blur' }
          ],

          hotelid: [
            { required: true, message: '所属客户不能为空', trigger: 'blur' }
          ],

          equipmodel: [
            { required: true, message: '设备模板不能为空', trigger: 'blur' }
          ],

          hoteladdress: [
            { required: true, message: '地址不能为空', trigger: 'blur' }
          ],  

          password: [
            { validator: validatePassword, trigger: 'blur' }
          ],

          comfirmPassword: [
            { validator: validateComfirmPassword, trigger: 'blur' }
          ],

          gateway: [
            {required: true, message: '类别不能为空', trigger: 'blur'}
          ],

          groupid: [
            {required: true, message: '组别不能为空', trigger: 'blur'}
          ],

          offlinetime: [
            {required: true, message: '设备离线时间不能为空', trigger: 'blur'}
          ],

          efficient: [
            { required: true, message: '类别不能为空', trigger: 'blur' }
          ]         
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.$http({
          url: this.$http.adornUrl('/sys/role/select'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.roleList = data && data.code === 0 ? data.list : []
        }).then(() => {
          this.visible = true
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
          })
        }).then(() => {
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/Internetofthings/Customer/selectByPrimaryKey?hotelId=${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.equipcode = data.content.equipcode
                this.dataForm.equipname = data.content.equipname
                this.dataForm.hotelid = data.content.hotelid
                this.dataForm.equipmodel = data.content.equipmodel
                this.dataForm.hoteladdress = data.content.hoteladdress
                this.dataForm.remarks = data.content.remarks
               
                this.dataForm.salt = data.content.salt
                this.dataForm.efficient = data.content.efficient
                this.dataForm.gateway = data.content.gateway
                this.dataForm.groupid = data.content.groupid
                this.dataForm.offlinetime = data.content.offlinetime
                this.dataForm.roleIdList = data.content.roleIdList
                this.dataForm.start = data.content.start
                this.dataForm.showcurve = data.content.showcurve
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
              url: this.$http.adornUrl(`/Internetofthings/Customer/insertSelective`),
              method: 'post',
              data: this.$http.adornData({
                'userId': this.dataForm.id || undefined,
                'equipcode': this.dataForm.equipcode,
                'equipname': this.dataForm.equipname,
                'hotelid': this.dataForm.hotelid,
                'equipmodel': this.dataForm.equipmodel,
                'hoteladdress': this.dataForm.hoteladdress,
                'remarks': this.dataForm.remarks,
                'password': this.dataForm.password,
                'salt': this.dataForm.salt,
                'efficient': this.dataForm.efficient,
                'gateway': this.dataForm.gateway,
                'groupid': this.dataForm.groupid,
                'offlinetime': this.dataForm.offlinetime,
                'start': this.dataForm.start,
                'showcurve': this.dataForm.showcurve,
                'roleIdList': this.dataForm.roleIdList
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
