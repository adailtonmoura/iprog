<template>
  <jet-action-section>
    <template #title>
      Autenticação de dois fatores
    </template>

    <template #description>
      Adicione a autenticação de dois fatores na sua conta para uma maior segurança.
    </template>

    <template #content>
      <h3 class="text-lg font-medium text-gray-900" v-if="twoFactorEnabled">
        A autenticação de dois fatores está ativada.
      </h3>

      <h3 class="text-lg font-medium text-gray-900" v-else>
        A autenticação de dois fatores não está ativada.
      </h3>

      <div class="mt-3 max-w-xl text-sm text-gray-600">
        <p>
          Quando a autenticação de dois fatores está habilitada, você será solicitado a fornecer um token aleatório seguro durante a autenticação. Você pode recuperar esse token do aplicativo Google Authenticator do seu telefone.
        </p>
      </div>

      <div v-if="twoFactorEnabled">
        <div v-if="qrCode">
          <div class="mt-4 max-w-xl text-sm text-gray-600">
            <p class="font-semibold">
              Autenticação de dois fatores está ativada. Escaneie o QR Code usando o aplicativo de autenticação no seu celular.
            </p>
          </div>

          <div class="mt-4 dark:p-4 dark:w-56 dark:bg-white" v-html="qrCode">
          </div>
        </div>

        <div v-if="recoveryCodes.length > 0">
          <div class="mt-4 max-w-xl text-sm text-gray-600">
            <p class="font-semibold">
              Armazene esses códigos de recuperação em um gerenciador de senhas seguro. Eles podem ser usados para recuperar o acesso ao seu
              conta se o seu dispositivo de autenticação de dois fatores for perdido.

            </p>
          </div>

          <div class="grid gap-1 max-w-xl mt-4 px-4 py-4 font-mono text-sm bg-gray-100 rounded-lg">
            <div v-for="code in recoveryCodes" :key="code">
              {{ code }}
            </div>
          </div>
        </div>
      </div>

      <div class="mt-5">
        <div v-if="! twoFactorEnabled">
          <jet-confirms-password @confirmed="enableTwoFactorAuthentication">
            <jet-button type="button" :class="{ 'opacity-25': enabling }" :disabled="enabling">
              Ligar
            </jet-button>
          </jet-confirms-password>
        </div>

        <div v-else>
          <jet-confirms-password @confirmed="regenerateRecoveryCodes">
            <jet-secondary-button class="mr-3"
                                  v-if="recoveryCodes.length > 0">
              Gerar novos códigos de recuperação.
            </jet-secondary-button>
          </jet-confirms-password>

          <jet-confirms-password @confirmed="showRecoveryCodes">
            <jet-secondary-button class="mr-3" v-if="recoveryCodes.length == 0">
              Mostra códigos de recuperação
            </jet-secondary-button>
          </jet-confirms-password>

          <jet-confirms-password @confirmed="disableTwoFactorAuthentication">
            <jet-danger-button
                :class="{ 'opacity-25': disabling }"
                :disabled="disabling">
              Desativar
            </jet-danger-button>
          </jet-confirms-password>
        </div>
      </div>
    </template>
  </jet-action-section>
</template>

<script>
import JetActionSection from '@/Jetstream/ActionSection'
import JetButton from '@/Jetstream/Button'
import JetConfirmsPassword from '@/Jetstream/ConfirmsPassword'
import JetDangerButton from '@/Jetstream/DangerButton'
import JetSecondaryButton from '@/Jetstream/SecondaryButton'

export default {
  components: {
    JetActionSection,
    JetButton,
    JetConfirmsPassword,
    JetDangerButton,
    JetSecondaryButton,
  },

  data() {
    return {
      enabling: false,
      disabling: false,

      qrCode: null,
      recoveryCodes: [],
    }
  },

  methods: {
    enableTwoFactorAuthentication() {
      this.enabling = true

      this.$inertia.post('/user/two-factor-authentication', {}, {
        preserveScroll: true,
      }).then(() => {
        return Promise.all([
          this.showQrCode(),
          this.showRecoveryCodes()
        ])
      }).then(() => {
        this.enabling = false
      })
    },

    showQrCode() {
      return axios.get('/user/two-factor-qr-code')
          .then(response => {
            this.qrCode = response.data.svg
          })
    },

    showRecoveryCodes() {
      return axios.get('/user/two-factor-recovery-codes')
          .then(response => {
            this.recoveryCodes = response.data
          })
    },

    regenerateRecoveryCodes() {
      axios.post('/user/two-factor-recovery-codes')
          .then(response => {
            this.showRecoveryCodes()
          })
    },

    disableTwoFactorAuthentication() {
      this.disabling = true

      this.$inertia.delete('/user/two-factor-authentication', {
        preserveScroll: true,
      }).then(() => {
        this.disabling = false
      })
    },
  },

  computed: {
    twoFactorEnabled() {
      return !this.enabling && this.$page.user.two_factor_enabled
    }
  }
}
</script>
