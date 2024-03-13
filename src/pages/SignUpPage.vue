<template>
    <van-row justify="center">
        <van-image
                width="343"
                src="../../public/juyuan.jpg"
                style="background-position:center"
        />
    </van-row>
  <van-notice-bar v-if="notice" color="#1989fa" background="#ecf9ff" left-icon="info-o">
    {{ notice_text }}
  </van-notice-bar>
    <van-form @submit="onSubmit">
        <van-cell-group inset>
            <van-field
                    v-model="phone"
                    required
                    label="手机号"
                    placeholder="请输入手机号"
                    :rules="[{ required: true, message: '请填写手机号' }]"
            >
            </van-field>
            <van-field
                    v-model="username"
                    required
                    label="用户名"
                    placeholder="请输入用户名"
                    :rules="[{ required: true, message: '请填写用户名' }]"
            />
            <van-field
                    v-model="password"
                    required
                    type="password"
                    label="密码"
                    placeholder="请输入密码"
                    :rules="[{ required: true, message: '请填写密码' }]"
            />
            <van-field
                    v-model="confirmPassword"
                    required
                    type="password"
                    label="确认密码"
                    placeholder="请输入密码"
                    :rules="[{ validator, message: '两次输入的密码不一致' }]"
            />
        </van-cell-group>
        <div style="margin: 16px;">
            <van-button round block type="primary" native-type="submit">
                注册
            </van-button>
        </div>
    </van-form>
</template>

<script setup>
import {ref} from "vue";
import {showFailToast, showNotify, showSuccessToast} from "vant";
import myAxios from "../plugins/my-axios.js";
import {useRouter} from "vue-router";
import 'vant/es/notify/style'

const reg_tel = /^(13[0-9]|14[01456879]|15[0-35-9]|16[2567]|17[0-8]|18[0-9]|19[0-35-9])\d{8}$/;
const reg_username = /^[a-zA-Z][a-zA-Z0-9_]{4,15}$/;
const username = ref('');
const password = ref('');
const phone = ref('');
const confirmPassword = ref('')
const lock = ref(true)
let router = useRouter();
const notice = ref(false);
const notice_text = ref('')
const validator = () => {
    return password.value === confirmPassword.value;
}

const onSubmit = async () => {
    if (phone.value === '') {
        showFailToast("请填写手机号")
        return
    }
    if (password.value === '' || confirmPassword.value === '') {
        showFailToast("请填写密码")
        return
    }
  if (!reg_username.test(username.value)) {
    showFailToast("账号非法");
    notice.value = true;
    notice_text.value = '字母开头，允许5-16字节，允许字母数字下划线'
    username.value = ''
    return
  }
    const res = await myAxios.post("/user/register", {
        phone: phone.value,
        userAccount: username.value,
        userPassword: password.value,
        checkPassword: confirmPassword.value
    })
    if (res?.data.code === 0) {
        showSuccessToast("注册成功")
        sessionStorage.setItem("token", res.data.data)
        location.href = "/after"
    } else {
        showFailToast("注册失败," + res?.data.description ?? '')
    }
};
</script>

<style scoped>
</style>
