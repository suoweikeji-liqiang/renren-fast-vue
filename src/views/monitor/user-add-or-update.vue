<template>

  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">

    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">

      <el-form-item label="酒店名称" prop="userName">
        <el-input v-model="dataForm.userName" placeholder="酒店名称"></el-input>
      </el-form-item>

      
      <el-form-item label="联系人" prop="email">
        <el-input v-model="dataForm.email" placeholder="联系人"></el-input>
      </el-form-item>

      <el-form-item label="联系人电话" prop="mobile">
        <el-input v-model="dataForm.mobile" placeholder="手机号"></el-input>
      </el-form-item>

      <el-form-item label="地址" prop="mobile">
        <el-input v-model="dataForm.mobile" placeholder="地址"></el-input>
      </el-form-item>

      <el-form-item label="备注" prop="mobile">
        <el-input v-model="dataForm.mobile" placeholder="备注"></el-input>
      </el-form-item>

      <el-form-item label="是否启用" size="mini" prop="status">
        <el-radio-group v-model="dataForm.status">
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
          userName: '',
          password: '',
          comfirmPassword: '',
          salt: '',
          email: '',
          mobile: '',
          roleIdList: [],
          status: 1
        },
        dataRule: {
          userName: [
            { required: true, message: '用户名不能为空', trigger: 'blur' }
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
          ],
          mobile: [
            { required: true, message: '手机号不能为空', trigger: 'blur' },
            { validator: validateMobile, trigger: 'blur' }
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
              url: this.$http.adornUrl(`/sys/user/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userName = data.user.username
                this.dataForm.salt = data.user.salt
                this.dataForm.email = data.user.email
                this.dataForm.mobile = data.user.mobile
                this.dataForm.roleIdList = data.user.roleIdList
                this.dataForm.status = data.user.status
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
              url: this.$http.adornUrl(`/sys/user/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'userId': this.dataForm.id || undefined,
                'username': this.dataForm.userName,
                'password': this.dataForm.password,
                'salt': this.dataForm.salt,
                'email': this.dataForm.email,
                'mobile': this.dataForm.mobile,
                'status': this.dataForm.status,
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
