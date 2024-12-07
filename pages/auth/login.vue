<script lang="ts" setup>
import * as bankData from '@/data/financialData.js';
import passwordInput from '@/components/UI/passwordInput.vue';
import NumericKeypad from '@/components/UI/NumericKeypad.vue';
import auth from '@/middleware/auth';
import { useRouter } from 'vue-router';
import { ref } from 'vue';
import { useAuthStore } from '@/stores/auth';
import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';

definePageMeta({
  layout: 'custom',
  middleware: [auth],
});

const { authenticateUser } = useAuthStore();

const user = ref({
  username: '',
  password: '',
});

const logoUrl = ref(bankData.logoUrl)
const isKeypadVisible = ref(false);

const handleKeypadInput = (value: string) => {
  if (value === 'backspace') {
    user.value.password = user.value.password.slice(0, -1);
  } else if (value === 'clear') {
    user.value.password = '';
  } else {
    user.value.password += value;
  }
};

const toggleKeypad = () => {
  isKeypadVisible.value = !isKeypadVisible.value;
};

const router = useRouter();

const login = async () => {
  let data = await authenticateUser(user.value);
  if (data.authenticated) {
    router.push('/dashboard/overview');
    toast.success("Connexion réussie", {
      theme: 'auto',
      icon: true,
      hideProgressBar: true,
      autoClose: true,
      position: 'top-right',
    });
  } else {
    toast.error("Identifiants incorrects", {
      theme: 'auto',
      icon: true,
      hideProgressBar: true,
      autoClose: true,
      position: 'top-right',
    });
  }
};
</script>

<script lang="ts">
export default {
  components: {
    passwordInput,
    NumericKeypad
  }
}
</script>

<template>
  <div class="container h-100 d-flex justify-content-center align-items-center">
    <div class="row justify-content-center align-items-center w-100">
      <div class="col-xxl-5 col-xl-6 col-lg-7 col-md-8 col-sm-10 col-12">
        <div class="my-5 d-flex justify-content-center">
          <NuxtLink style="display: flex; justify-content: center" to='/'>
            <img :src="logoUrl" alt="logo" class="desktop-logo" style="max-width: 200px; height: auto; display: block; margin: 0 auto;">
<!--            <img src="https://mabanque.bnpparibas/content/dam/mabanque/generique/bnp-alone.png" alt="logo" class="desktop-dark">-->
          </NuxtLink>
        </div>
        <div class="card custom-card p-4">
          <div class="card-body p-5">
            <p class="h4 fw-bold mb-4 text-center">Connexion à votre compte</p>
            <div class="row gy-3">
              <div class="col-xl-12">
                <label for="signin-username" class="form-label text-default">Identifiant</label>
                <input v-model="user.username" name="uname" type="text" class="form-control form-control-lg"
                       id="signin-username" placeholder="Saisissez votre identifiant" :required="true">
              </div>
              <div class="col-xl-12 mb-2">
<!--                <label for="signin-password" class="form-label text-default d-block">Mot de passe-->
<!--                  <NuxtLink to="/authentication/reset-password/basic" class="float-end text-danger">Mot de passe oublié ?</NuxtLink>-->
<!--                </label>-->
                <div class="input-group">
                  <!-- Champ de mot de passe avec pavé numérique -->
                  <input v-model="user.password" name="password" id="password" placeholder="Mot de passe"
                         class="form-control form-control-lg" type="password" @focus="toggleKeypad" readonly />
                </div>
              </div>

              <div v-if="isKeypadVisible" class="col-xl-12 mb-3">
                <NumericKeypad @input="handleKeypadInput" />
              </div>

              <div class="col-xl-12 d-grid mt-2">
                <button @click.prevent="login" class="btn btn-lg btn-primary">Se connecter</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  height: 100vh;
}

.card.custom-card {
  border: none;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
}

.card-body {
  padding: 40px 30px;
}

.btn-primary {
  background-color: #0046b3;
  border-color: #0046b3;
}

.btn-primary:hover {
  background-color: #00338d;
  border-color: #00338d;
}

.authentication-barrier {
  margin: 20px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}

.authentication-barrier span {
  background: #fff;
  padding: 0 15px;
  z-index: 1;
}

.authentication-barrier::before {
  content: "";
  position: absolute;
  top: 50%;
  width: 100%;
  height: 1px;
  background: #e0e0e0;
}

.form-check-input {
  width: 20px;
  height: 20px;
}

input[type="checkbox"] {
  accent-color: #0046b3;
}
</style>
