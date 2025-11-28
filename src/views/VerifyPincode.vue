<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <div class="login-bg">
        <div>
            <v-sheet>
                <div class="d-flex justify-center">
                    <img :src="logo" loading="lazy" width="50" alt="Logo" />
                </div>
                <v-form @submit.prevent="sendPincode" class="pa-4">
                    <h5 class="text-center mb-3">To continue, we have sent a pincode to your email. Kindly check and fill-out the given field below</h5>
                    <span class="text-white">Pincode</span>
                    <v-text-field v-model="pincode" 
                        :rules="[requiredRule]"
                        placeholder="Type here..."
                        class="text-info"
                        prepend-inner-icon="mdi-lock"
                        variant="outlined"
                        density="compact"
                        autocomplete="pincode" 
                        :type="showPassword ? 'text' : 'password'"
                        :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye-outline'" 
                        @click:append-inner="showPassword = !showPassword"/>

                    <v-btn :disabled="!isFormValid" 
                        @click="sendPincode"
                        color="#004fb6" 
                        size="large" 
                        class="proceed-btn" 
                        height="45" 
                        block 
                        rounded>
                        Submit pincode
                    </v-btn>
                    <div class="to-login">
                        <p class="text-white">Has already an account? 
                            <span class="text-info" @click="$router.push('/')">Login here</span>
                        </p>
                    </div>
                    
                    <h6 class="system-version text-center">Digita ID and Verification Service System <br /> UAT v1.0.0</h6>
                </v-form>
            </v-sheet>
        </div>
    </div>

    <Alert ref="alertRef" />
</template>

<script>
import { useTheme } from 'vuetify';
import { ref, computed } from 'vue';
import { useAuthStore } from '@/stores/auth';
import { useLoadingStore } from '@/stores/loading';
import { useBenefeciaryStore } from '@/stores/benefeciaryStore';
import Alert from '@/components/Alert.vue';

export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name: 'VerifyPincode',
    components: {
        Alert,
    },
    data() {
        return {
            pincode: '',
            showPassword: false,
            logo: require('@/assets/DSWD-logo.png'),
        }
    },

    setup() {
        const authStore = useAuthStore();
        const loadingStore = useLoadingStore();
        const benefeciaryStore = useBenefeciaryStore();
        const theme = useTheme();
        const themeDialog = ref(false);
        const selectedTheme = ref(theme.global.name.value);
        const currentThemeName = computed(() => {
            return theme.global.name.value === 'dark' ? 'Dark' : 'Light';
        });
        const applyTheme = () => {
            theme.global.name.value = selectedTheme.value;
            localStorage.setItem('theme', selectedTheme.value);
            themeDialog.value = false;
        };
        return {
            authStore,
            loadingStore,
            benefeciaryStore,
            theme,
            themeDialog,
            selectedTheme,
            currentThemeName,
            applyTheme,
        };
    },

    computed: {
        isFormValid() {
            return (this.pincode);
        },
    },

    methods: {

        requiredRule(v) {
            return !!v || 'This field is required';
        },

        async sendPincode() {
            this.loadingStore.show("Saving...")
            try {
                const payload = {
                    pincode: this.pincode,
                };
                const response = await this.benefeciaryStore.sendPincodeStore(payload);
                if (response.success === true) {
                    this.$router.push({
                        path: '/new-password',
                        state: { pincode: this.pincode }
                    });
                }
            } catch (error) {
                console.error(error, 'error');
                this.showAlert(error, 'error');
            } finally {
                this.accountDialog = false;
                this.loadingStore.hide();
            }
        },

        showAlert(message) {
            this.$refs.alertRef.showSnackbarAlert(message, "error");
        },

    },
};
</script>

<style scoped>
* {
    margin: 0 !important;
    padding: 0 !important;
}
.login-bg {
    width: 100vw;
    height: 100vh;
}

.v-container {
    align-items: center;
}

.v-sheet {
    margin-left: 0 !important;
}

.v-form {
    padding: 50px !important;
    background-color: #000f50;
    border-radius: 100px;
    height: 100dvh;
}

img {
    min-width: 70px;
    width: 40%;
    border-radius: 10px;
    padding: 50px;
    margin-top: 25px !important;
    margin-bottom: 25px !important;
}

.to-login {
    color: #fffcfc;
    font-size: 14px;
    text-align: center;
    margin-top: 10px !important;
}

.v-input__details {
    display: flex;
}

h5 {
    font-weight: normal;
    color: #d7dbff;
}

.proceed-btn {
    margin-top: 20px !important;
}

.system-version {
    margin-top: 100px !important;
    color: #d3eaff;
}
</style>