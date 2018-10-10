<template>

  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">

    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">

      <el-form-item label="编码" prop="agentcode">
        <el-input v-model="dataForm.agentcode" placeholder=""></el-input>
      </el-form-item>

      
      <el-form-item label="名称" prop="agentname">
        <el-input v-model="dataForm.agentname" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="账号" prop="number">
        <el-input v-model="dataForm.number" placeholder="账号"></el-input>
      </el-form-item>

      <el-form-item label="密码" prop="password">
        <el-input v-model="dataForm.password" placeholder="密码"></el-input>
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
          agentcode: '',
          password: '',
          comfirmPassword: '',
          salt: '',
          agentname: '',
          number: '',
          roleIdList: [],
          start: 1
        },
        dataRule: {
          agentcode: [
            { required: true, message: '代理商不能为空', trigger: 'blur' }
          ],
          password: [
            { validator: validatePassword, message: '密码不能为空',trigger: 'blur' }
          ],
          comfirmPassword: [
            { validator: validateComfirmPassword, trigger: 'blur' }
          ],
          agentname: [
            { required: true, message: '名称不能为空', trigger: 'blur' },
          ],
          number: [
            { required: true, message: '账号不能为空', trigger: 'blur' },
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
                this.dataForm.agentcode = data.user.agentcode
                this.dataForm.salt = data.user.salt
                this.dataForm.agentname = data.user.agentname
                this.dataForm.number = data.user.number
                this.dataForm.password = data.user.password
                this.dataForm.roleIdList = data.user.roleIdList
                this.dataForm.start = data.user.start
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
                'agentcode': this.dataForm.agentcode,
                'password': this.dataForm.password,
                'salt': this.dataForm.salt,
                'agentname': this.dataForm.agentname,
                'number': this.dataForm.number,
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
