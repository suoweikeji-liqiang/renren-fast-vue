<template>

  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">

    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">



      <el-form-item label="编码" prop="hotelcode">
        <el-input v-model="dataForm.hotelcode" placeholder=""></el-input>
      </el-form-item>

      
      <el-form-item label="酒店名称" prop="hotelname">
        <el-input v-model="dataForm.hotelname" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="SIM卡号" prop="cardnumber">
        <el-input v-model="dataForm.cardnumber" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="所属楼宇" prop="hotel">
        <el-input v-model="dataForm.hotel" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="离线时间设置" prop="offlinetime">
        <el-input v-model="dataForm.offlinetime" placeholder=""></el-input>
      </el-form-item>
      
      <el-form-item label="说明" prop="remarks">
        <el-input v-model="dataForm.remarks" placeholder=""></el-input>
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
          hotelcode: '',
          hotelname: '',
          hotel: '',
          offlinetime: '',
          remarks: '',
          password: '',
          comfirmPassword: '',
          salt: '',
          email: '',
          cardnumber: '',
         
          roleIdList: [],
          start: 1
        },

        dataRule: {
          hotelcode: [
            { required: true, message: '编码不能为空', trigger: 'blur' }
          ],

          hotelname: [
            { required: true, message: '酒店名称不能为空', trigger: 'blur' }
          ],

          hotel: [
            { required: true, message: '楼宇不能为空', trigger: 'blur' }
          ],

          offlinetime: [
            { required: true, message: '离线时间不能为空', trigger: 'blur' }
          ],

          cardnumber: [
            { required: true, message: '卡号不能为空', trigger: 'blur' }
          ],

          password: [
            { validator: validatePassword, trigger: 'blur' }
          ],

          comfirmPassword: [
            { validator: validateComfirmPassword, trigger: 'blur' }
          ],

          email: [
            { required: true, message: '邮箱不能为空', trigger: 'blur' },
            { validator: validateEmail, trigger: 'blur' }
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
              url: this.$http.adornUrl(`/Internetofthings/Gateway/selectByPrimaryKey?hotelid=${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.hotelcode = data.content.hotelcode
                this.dataForm.hotelname = data.content.hotelname
                this.dataForm.hotel = data.content.hotel
                this.dataForm.cardnumber = data.content.cardnumber
                this.dataForm.mobile = data.content.mobile
                this.dataForm.offlinetime = data.content.offlinetime
                this.dataForm.remarks = data.content.remarks
               
                this.dataForm.salt = data.content.salt
                this.dataForm.email = data.content.email
               
                this.dataForm.roleIdList = data.content.roleIdList
                this.dataForm.start = data.content.start
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
              url: this.$http.adornUrl(`/Internetofthings/Gateway/insertSelective`),
              method: 'post',
              data: this.$http.adornData({
                'userId': this.dataForm.id || undefined,
                'hotelcode': this.dataForm.hotelcode,
                'hotelname': this.dataForm.hotelname,
                'hotel': this.dataForm.hotel,
                'cardnumber': this.dataForm.cardnumber,
                'mobile': this.dataForm.mobile,
                'offlinetime': this.dataForm.offlinetime,
                'remarks': this.dataForm.remarks,
                'password': this.dataForm.password,
                'salt': this.dataForm.salt,
                'email': this.dataForm.email,
                'start': this.dataForm.start,
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
