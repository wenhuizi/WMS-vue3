<template>
  <!-- <div class="grid grid-cols-2 h-screen"> -->
  <!-- flex-col 用于指定子元素垂直排列 -->
  <div class="flex justify-center items-center h-full flex-col">
    <h1 class="font-bold text-4xl mb-5">欢迎回来</h1>
    <!-- 设置 flex 布局，内容垂直水平居中，文字颜色，以及子内容水平方向 x 轴间距 -->
    <div class="flex items-center justify-center mb-7 text-gray-400 space-x-2">
      <!-- 左边横线，高度为 1px, 宽度为 16，背景色设置 -->
      <span class="h-[1px] w-16 bg-gray-200"></span>
      <span>账号密码登录</span>
      <!-- 右边横线 -->
      <span class="h-[1px] w-16 bg-gray-200"></span>
    </div>
    <!-- 引入 Element Plus 表单组件，移动端设置宽度为 5/6，PC 端设置为 2/5 -->
    <el-form class="w-5/6 md:w-2/5" ref="formRef" :rules="rules" :model="form">
      <el-form-item prop="username">
        <!-- 输入框组件 -->
        <el-input
          size="large"
          v-model="form.username"
          placeholder="请输入用户名"
          :prefix-icon="User"
          clearable
        />
      </el-form-item>
      <el-form-item prop="password">
        <!-- 密码框组件 -->
        <el-input
          size="large"
          v-model="form.password"
          type="password"
          placeholder="请输入密码"
          :prefix-icon="Lock"
          clearable
        />
      </el-form-item>
      <el-form-item>
        <!-- 登录按钮，宽度设置为 100% -->
        <el-button
          class="w-full mt-2"
          size="large"
          :loading="loading"
          type="primary"
          @click="onSubmit"
          >登录</el-button
        >
      </el-form-item>
      <el-form-item>
        <!-- 登录按钮，宽度设置为 100% -->
        <el-button class="w-full" size="large" type="primary">注册</el-button>
      </el-form-item>
    </el-form>
  </div>
  <!-- </div> -->
</template>

<script setup>
// 引入 Element Plus 中的用户、锁图标
import { User, Lock } from "@element-plus/icons-vue";
import { login } from "@/api/admin/user";
import { reactive, ref, onMounted, onBeforeUnmount } from "vue";
import { useRouter } from "vue-router";
import { showMessage } from "@/composables/util";

// 定义响应式的表单对象
const form = reactive({
  username: "",
  password: "",
});

// 表单引用
const formRef = ref(null);
// 表单验证规则
const rules = {
  username: [
    {
      required: true,
      message: "用户名不能为空",
      trigger: "blur",
    },
  ],
  password: [
    {
      required: true,
      message: "密码不能为空",
      trigger: "blur",
    },
  ],
};

// 按回车键后，执行登录事件
function onKeyUp(e) {
  console.log(e);
  if (e.key == "Enter") {
    onSubmit();
  }
}

// 添加键盘监听
onMounted(() => {
  console.log("添加键盘监听");
  document.addEventListener("keyup", onKeyUp);
});

// 移除键盘监听
onBeforeUnmount(() => {
  document.removeEventListener("keyup", onKeyUp);
});

const router = useRouter();
// 登录按钮加载
const loading = ref(false);

// 登录
const onSubmit = () => {
  console.log("登录");
  // 先验证 form 表单字段
  formRef.value.validate((valid) => {
    if (!valid) {
      console.log("表单验证不通过");
      return false;
    }

    // 调用登录接口
    login(form.username, form.password)
      .then((res) => {
        console.log(res);
        // 判断是否成功
        if (res.data.success == true) {
          // 提示登录成功
          showMessage("登陆成功");

          // 跳转到后台首页
          router.push("/admin/index");
        } else {
          // 获取服务端返回的错误消息
          let message = res.data.message;
          // 提示消息
          showMessage(message, "error");
        }
      })
      .finally(() => {
        //结束加载
        loading.value = false;
      });
  });
};
</script>
