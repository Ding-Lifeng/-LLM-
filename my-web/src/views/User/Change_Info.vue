<template>
  <div id="user-profile">
    <!-- 用户信息表单 -->
    <div class="user-info">
      <h1>修改用户信息</h1>
      <el-form ref="form" :model="userInfo" :rules="rules" label-width="100px">
        <el-form-item label="用户名" prop="name">
          <el-input v-model="userInfo.name"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input type="password" v-model="userInfo.password"></el-input>
        </el-form-item>
        <el-form-item label="姓名" prop="name">
          <el-input v-model="userInfo.real_name"></el-input>
        </el-form-item>
        <el-form-item label="性别" prop="gender">
          <el-radio-group v-model="userInfo.gender">
            <el-radio label="男">男</el-radio>
            <el-radio label="女">女</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="手机号" prop="phoneNum">
          <el-input v-model="userInfo.phoneNum"></el-input>
        </el-form-item>
        <el-form-item label="职位" prop="position" class="position-item">
          <el-select v-model="userInfo.position" placeholder="请选择职位" class="el-select">
            <el-option label="学生" value="学生"></el-option>
            <el-option label="学生助教" value="学生助教"></el-option>
            <el-option label="讲师" value="讲师"></el-option>
            <el-option label="助理教授" value="助理教授"></el-option>
            <el-option label="副教授" value="副教授"></el-option>
            <el-option label="教授" value="教授"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="学校" prop="school">
          <el-input v-model="userInfo.school"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm">修改信息</el-button>
          <el-button @click="resetForm">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import 'element-ui/lib/theme-chalk/index.css';
import {
  Form,
  FormItem,
  RadioGroup,
  Radio,
  Button,
  Option,
} from 'element-ui';

import { changeUserInfo, getUserInfo } from "@/api/changeUserInfo";
import {removeToken} from "@/utils/auth";

export default {
  name: 'UserProfile',
  components: {
    'el-form': Form,
    'el-form-item': FormItem,
    'el-radio-group': RadioGroup,
    'el-radio': Radio,
    'el-button': Button,
    'el-option': Option,
  },
  data() {
    return {
      userInfo: {
        username: '',
        name: '',
        gender: '',
        phoneNum: '',
        position: '',
        school: '',
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
        ],
        name: [
          { required: true, message: '请输入姓名', trigger: 'blur' },
        ],
        gender: [
          { required: true, message: '请选择性别', trigger: 'change' },
        ],
        phoneNum: [
          { required: true, message: '请输入手机号', trigger: 'blur' },
        ],
        position: [
          { required: true, message: '请选择职位', trigger: 'change' },
        ],
        school: [
          { required: true, message: '请输入学校', trigger: 'blur' },
        ],
      },
    };
  },
  created() {
    this.fetchUserInfo();
  },
  methods: {
    fetchUserInfo() {
      getUserInfo()
          .then(response => {
            const { name, password, real_name, gender, phoneNum, position, school } = response.data;
            this.userInfo = { name, password, real_name, gender, phoneNum, position, school };
          })
          .catch(error => {
            console.error('获取用户信息失败:', error);
            this.$message.error('获取用户信息失败!');
          });
    },
    submitForm() {
      this.$refs.form.validate(valid => {
        if (valid) {
          changeUserInfo(this.userInfo.name, this.userInfo.password, this.userInfo.real_name, this.userInfo.gender, this.userInfo.phoneNum, this.userInfo.position, this.userInfo.school)
              .then(response => {
                this.$message.success('用户修改成功!');
                removeToken() // 清除token
                this.$router.push('/Login');
              })
              .catch(error => {
                console.error('修改失败:', error);
                this.$message.error('用户修改失败!');
              });
        } else {
          console.log('表单验证失败');
        }
      });
    },
    resetForm() {
      this.$refs.form.resetFields();
    },
  },
};
</script>

<style scoped>
#user-profile {
  text-align: center;
  padding: 2rem;
  background-color: #f8f9fa;
  border-radius: 10px;
  width: 80%;
  margin: 2rem auto;
}

.user-info {
  background-color: white;
  padding: 2rem;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.card-content h3 {
  margin: 0;
  font-size: 1.5rem;
  color: #333;
}

.card-content p {
  margin: 0.5rem 0;
  color: #666;
}
</style>