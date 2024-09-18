<template>
  <form class="row flex flex-center" @submit.prevent="handleLogin">
    <div class="col-6 form-widget">
      <h1 class="header">Login</h1>
      <p class="description">Sign in via magic link with your email below</p>
      <div>
        <input class="inputField" type="email" placeholder="Your email" v-model="email" />
      </div>
      <div>
        <input class="inputField" type="password" placeholder="Your password" v-model="password" />
      </div>
      <div>
        <input type="submit" class="button block" :value="loading ? 'Loading' : '邮箱密码登录'" :disabled="loading" />
      </div>

      <!-- <div>
        <input type="submit" class="button block" :value="loading ? 'Loading' : 'magic link登录'" :disabled="loading" />
      </div> -->
    </div>
  </form>
</template>

<script>
import { ref } from 'vue';
import { supabase } from '../supabase';

export default {
  setup() {
    const loading = ref(false);
    const email = ref('815835721@qq.com');
    const password = ref('123456789');

    const handleLogin = async () => {
      try {
        loading.value = true;
        const { error } = await supabase.auth.signInWithPassword({ email: email.value, password: password.value });
        // const { error } = await supabase.auth.signInWithOtp({ email: email.value });
        // alert('Check your email for the login link!');
        if (error) throw error;
      } catch (error) {
        alert(error.error_description || error.message);
      } finally {
        loading.value = false;
      }
    };

    return {
      loading,
      email,
      handleLogin,
      password
    };
  }
};
</script>
