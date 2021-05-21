<template>
  <div id="login">
    <div class="login-wrap">
      <ul class="menu-tab">
        <li v-for="item in menuTab" :key="item.id" :class="{'current': item.current}" @click="toggleMenu(item)">{{item.txt}}</li>
      </ul>
      <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm"  class="demo-ruleForm" size="medium">
          <el-form-item  prop="username" class="item-from">
            <label for="">邮箱:</label>
            <el-input type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
          </el-form-item>
          
          <el-form-item  prop="password" class="item-from">
            <label for="">密码:</label>
            <el-input type="password" v-model="ruleForm.password" autocomplete="off" minlength="6" maxlength="20"></el-input>
          </el-form-item>

          <el-form-item  prop="password2" class="item-from" v-show="model === 'register'">
            <label for="">再次输入密码:</label>
            <el-input type="password" v-model="ruleForm.password2" autocomplete="off" minlength="6" maxlength="20"></el-input>
          </el-form-item>
          
          <el-form-item  prop="vCode" class="item-from">
            <label for="">验证码:</label>
            <el-row :gutter="10">
              <el-col :span="15">
                <el-input v-model.number="ruleForm.vCode" minlength="6" maxlength="6"></el-input>
              </el-col>
              <el-col :span="9">
                <el-button type="success" @click="submitForm('ruleForm')" class="btn-submit">获取验证码</el-button>
              </el-col>
            </el-row>
            
          </el-form-item>
          
          <el-form-item>
            <el-button type="danger" @click="submitForm('ruleForm')" class="login-btn btn-submit">提交</el-button>
          </el-form-item>
        </el-form> 
    </div>
  </div>
</template>

<script>
import { stripscript } from '@/utils/validate.js';
export default {
    name: 'login',
    data(){
// 表单内容
      // 验证用户名为username
      var validateUsername = (rule, value, callback) => {
        let reg = /^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$/;
        
        if (value === '') {
          callback(new Error('请输入用户名'));
        }else if(!reg.test(value)){
          callback(new Error('用户名格式有误'));
        } 
        else {
          if (this.ruleForm.username !== '') {
            this.$refs.ruleForm.validateField('username');
          }
          callback();
        }
      };
      // 验证密码规则
      var validatePass = (rule, value, callback) => {
        // 过滤后的字符
        this.ruleForm.password = stripscript(value);
        value = this.ruleForm.password
        let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/;
        if (value === '') {
          callback(new Error('请输入密码'));
        } else if (!reg.test(value)) {
          callback(new Error('请输入6-20位的还有大小写和数字的密码'));
        } else {
          callback();
        }
      };
       // 验证密码规则
      var validatePass2 = (rule, value, callback) => {
        // 模块验证
        if(this.model === 'login'){
          callback();
        }
        // 过滤后的字符
        this.ruleForm.password2 = stripscript(value);
        value = this.ruleForm.password2
        let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/;
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value != this.ruleForm.password) {
          callback(new Error('重复密码不正确！'));
        }else {
          callback();
        }
      };
      // 验证验证码
      var checkVCode = (rule, value, callback) => {
        
        this.ruleForm.vCode = stripscript(value);
        value = this.ruleForm.vCode
        let reg = /^[a-z0-9]{6}$/;
        if (!value) {
          return callback(new Error('请输入验证码'));
        }else if(!reg.test(value)){
          return callback(new Error('需要六位的字母+数字'));
        }else{
           callback();
        }
      };



      return {
        menuTab: [
          { txt: '登录',current: true, type: 'login'},
          { txt: '注册',current: false , type: 'register'},
        ],
        // 模块值
        model: 'login',
        ruleForm: {
          username: '',
          password: '',
          password2: '',
          vCode: ''
        },
        rules: {
          username: [
            { validator: validateUsername, trigger: 'blur' }
          ],
          password: [
            { validator: validatePass, trigger: 'blur' }
          ],
          password2: [
            { validator: validatePass2, trigger: 'blur' }
          ],
          vCode: [
            { validator: checkVCode, trigger: 'blur' }
          ]
        },
      
        
      }
    },
    created(){},
    // 挂在完成后自动执行
    mounted(){

    },
    //写函数的地方
    methods: {

      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      },
      //数据驱动视图
      toggleMenu(data){
        console.log(data);
        this.menuTab.forEach((elem,index) => {
          elem.current = false
          
        });
        data.current = true
        // 修改模块的值
        this.model = data.type
      }
      
    }

}
</script>

<style lang="scss" scoped>
#login {
  height: 100vh;
  background-color: #344a5f;
}
.login-wrap {
  width: 330px;
  margin: auto;
  // display: inline-block;
}
.menu-tab {
  text-align: center;
  li {
    display: inline-block;
    width: 88px;
    line-height: 36px;
    font-size: 14px;
    color: #fff;
    border-radius: 2px;
    cursor: pointer;
  }
  .current {
    background-color: rgba(0, 0, 0, 0.1);
  }
}
.demo-ruleForm {

  margin-top: 29px;
  label {
    display: block;
    margin-bottom: 3px;

    font-size: 14px;
    color: #fff;
  }
  .item-from{
    margin-bottom: 13px;
  }
  .btn-submit {
    width: 100%;
    display: block;
  }
  .login-btn{
    margin-top: 19px;

  }
}


</style>
