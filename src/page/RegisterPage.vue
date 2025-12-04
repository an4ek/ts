<template>
  <div class="register-container">
    <div class="card">
      <h2>Создание аккаунта</h2>
      <p>Зарегистрируйтесь, чтобы начать работу</p>

      <form @submit.prevent="handleRegister">
        <div class="input-group">
          <label>Email</label>
          <div class="input-with-icon">
            <input
              v-model="email"
              type="email"
              placeholder="Введите ваш email"
              :class="{ error: errors.email }"
              :disabled="isLoading"
            />
            <span class="icon">
              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="20" height="20">
                <path d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14v-7H5v7z"/>
              </svg>
            </span>
          </div>
          <div v-if="errors.email" class="error-message">
            <span>⚠️ {{ errors.email }}</span>
          </div>
        </div>

        <div class="input-group">
          <label>Пароль</label>
          <div class="input-with-icon">
            <input
              v-model="password"
              :type="showPassword ? 'text' : 'password'"
              placeholder="Придумайте пароль"
              :class="{ error: errors.password }"
              :disabled="isLoading"
            />
            <span class="icon" @click="togglePassword" :class="{ clickable: !isLoading }">
              <svg v-if="showPassword" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="20" height="20">
                <path d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/>
                <path d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"/>
              </svg>
              <svg v-else xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="20" height="20">
                <path d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/>
                <path d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"/>
                <path d="M12 12h.01"/>
              </svg>
            </span>
          </div>
          <div v-if="errors.password" class="error-message">
            <span>⚠️ {{ errors.password }}</span>
          </div>
        </div>

        <div class="input-group">
          <label>Подтверждение пароля</label>
          <div class="input-with-icon">
            <input
              v-model="confirmPassword"
              :type="showConfirmPassword ? 'text' : 'password'"
              placeholder="Повторите пароль"
              :class="{ error: errors.confirmPassword }"
              :disabled="isLoading"
            />
            <span class="icon" @click="toggleConfirmPassword" :class="{ clickable: !isLoading }">
              <svg v-if="showConfirmPassword" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="20" height="20">
                <path d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/>
                <path d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"/>
              </svg>
              <svg v-else xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="20" height="20">
                <path d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/>
                <path d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"/>
                <path d="M12 12h.01"/>
              </svg>
            </span>
          </div>
          <div v-if="errors.confirmPassword" class="error-message">
            <span>⚠️ {{ errors.confirmPassword }}</span>
          </div>
        </div>

        <button
          type="submit"
          class="btn-register"
          :disabled="isLoading"
        >
          {{ isLoading ? 'Регистрируемся...' : 'Зарегистрироваться →' }}
        </button>
      </form>

      <div class="footer">
        Уже есть аккаунт? <router-link to="/login">Войдите</router-link>
      </div>
    </div>

    <div v-if="globalError" class="global-error">
      <span>⚠️ {{ globalError }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { ref } from 'vue'
  import { useRouter } from 'vue-router'
  import { useUserStore } from '@/stores/useUserStore'

  const router = useRouter()
  const userStore = useUserStore()

  const email = ref<string>('')
  const password = ref<string>('')
  const confirmPassword = ref<string>('')
  const showPassword = ref<boolean>(false)
  const showConfirmPassword = ref<boolean>(false)
  const isLoading = ref<boolean>(false)

  const errors = ref<{ email?: string; password?: string; confirmPassword?: string }>({})
  const globalError = ref<string>('')

  function togglePassword() {
    if (!isLoading.value) showPassword.value = !showPassword.value
  }
  function toggleConfirmPassword() {
    if (!isLoading.value) showConfirmPassword.value = !showConfirmPassword.value
  }

  function clearErrors() {
    errors.value = {}
    globalError.value = ''
  }

  function validateForm(): boolean {
    clearErrors()

    if (!email.value.trim()) {
      errors.value.email = 'Email обязателен'
      return false
    }

    if (!password.value) {
      errors.value.password = 'Пароль обязателен'
      return false
    }

    if (password.value.length < 6) {
      errors.value.password = 'Пароль должен содержать минимум 6 символов'
      return false
    }

    if (!confirmPassword.value) {
      errors.value.confirmPassword = 'Подтверждение пароля обязательно'
      return false
    }

    if (password.value !== confirmPassword.value) {
      errors.value.confirmPassword = 'Пароли не совпадают'
      return false
    }

    const emailExists = userStore.users.some(user => user.email === email.value)
    if (emailExists) {
      errors.value.email = 'Пользователь с таким email уже существует'
      return false
    }

    return true
  }

  async function handleRegister() {
    if (!validateForm()) return

    isLoading.value = true

    try {
      await userStore.register(email.value, password.value)
      setTimeout(() => {
        router.push('/')
      }, 600)
    } catch (e: unknown) {
      let message = 'Ошибка регистрации'
      if (e instanceof Error) {
        if (e.message.toLowerCase().includes('email')) {
          message = 'Пользователь с таким email уже существует'
        }
      }
      globalError.value = message
    } finally {
      isLoading.value = false
    }
  }
</script>

<style scoped lang="scss">
  .register-container {
    font-family: 'Times New Roman', Times, serif;
    font-weight: normal;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    padding: 20px;
    background-color: var(--bg);

    .card {
      background: var(--bg);
      border: 1px solid var(--text);
      border-radius: 12px;
      box-shadow:
        0 8px 32px var(--input-shadow),
        0 0 0 1px rgba(0, 0, 0, 0.05);
      padding: 52px 44px;
      width: 100%;
      max-width: 460px;
      text-align: center;
      animation: fadeIn 0.6s ease-out;

      h2 {
        color: var(--input);
        font-weight: normal;
        font-size: 34px;
        margin-bottom: 14px;
        letter-spacing: -0.6px;
      }

      p {
        color: var(--text);
        margin-bottom: 36px;
        font-size: 18px;
        line-height: 1.5;
        opacity: 0.9;
      }

      .input-group {
        margin-bottom: 26px;
        text-align: left;

        label {
          display: block;
          margin-bottom: 10px;
          color: var(--text);
          font-weight: normal;
          font-size: 16px;
        }

        .input-with-icon {
          position: relative;

          input {
            font-family: 'Times New Roman', Times, serif;
            font-weight: normal;
            width: 100%;
            padding: 14px 18px 14px 50px;
            border: 1px solid var(--input-text);
            border-radius: 10px;
            font-size: 16px;
            background: var(--bg);
            color: var(--text);
            transition: border-color 0.3s, box-shadow 0.3s;

            &:focus {
              outline: none;
              border-color: var(--input);
              box-shadow: 0 0 0 3px var(--button-focus-light);
            }

            &.error {
              border-color: var(--error-red);
              background-color: var(--error-bg);
            }

            &:disabled {
              opacity: 0.7;
              cursor: not-allowed;
            }
          }

          .icon {
            position: absolute;
            left: 16px;
            top: 50%;
            transform: translateY(-50%);

            &.clickable {
              cursor: pointer;
            }

            svg {
              width: 22px;
              height: 22px;
              color: var(--input-text);
            }
          }
        }

        .error-message {
          color: var(--error-red);
          font-size: 15px;
          margin-top: 6px;
          display: flex;
          align-items: center;
          gap: 6px;
        }
      }

      .btn-register {
        font-family: 'Times New Roman', Times, serif;
        width: 100%;
        padding: 14px;
        background: var(--button);
        color: var(--button-text);
        border: none;
        border-radius: 10px;
        font-weight: normal;
        font-size: 18px;
        letter-spacing: 0.8px;
        cursor: pointer;
        transition: transform 0.2s, box-shadow 0.2s;

        &:hover:not(:disabled) {
          transform: translateY(-3px);
          box-shadow: 0 6px 16px var(--input-shadow);
        }

        &:disabled {
          opacity: 0.75;
          cursor: not-allowed;
        }
      }

      .footer {
        margin-top: 28px;
        color: var(--text);
        font-size: 16px;

        a {
          color: var(--input);
          text-decoration: underline;
          text-underline-offset: 4px;
          font-weight: normal;

          &:hover {
            text-decoration: none;
          }
        }
      }
    }

    .global-error {
      margin-top: 22px;
      font-family: 'Times New Roman', Times, serif;
      color: var(--error-red);
      font-size: 16px;
      background: var(--error-bg);
      padding: 12px 24px;
      border-radius: 10px;
      border-left: 4px solid var(--error-red);
      display: flex;
      align-items: center;
      gap: 8px;
    }
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: translateY(12px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
</style>