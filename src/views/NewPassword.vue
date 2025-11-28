<template>
    <div class="login-bg">
        <div>
            <v-sheet>
                <div class="d-flex justify-center">
                    <img :src="logo" loading="lazy" width="50" alt="Logo" />
                </div>
                <v-form @submit.prevent="saveNewPassword" class="pa-4">
                    <h5 class="text-center mb-3">Submit your new password to continue</h5>
                    <span class="text-white">Password</span>
                    <v-text-field v-model="password" 
                        :rules="[requiredRule]"
                        placeholder="Type here..."
                        class="text-info"
                        prepend-inner-icon="mdi-lock"
                        variant="outlined"
                        density="compact"
                        autocomplete="password"
                        :type="showPassword ? 'text' : 'password'"
                        :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye-outline'" 
                        @click:append-inner="showPassword = !showPassword" />

                    <v-btn :disabled="!isFormValid" 
                        @click="saveNewPassword"
                        color="#004fb6" 
                        size="large" 
                        class="proceed-btn" 
                        height="45" 
                        block 
                        rounded>
                        Change password
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
import { useLoadingStore } from '@/stores/loading';
import { useAuthStore } from '@/stores/auth';
import { useBenefeciaryStore } from '@/stores/benefeciaryStore';
import Alert from '@/components/Alert.vue';


export default {
    name: 'NewPassword',

    data() {
        return {
            logo: require('@/assets/DSWD-logo.png'),
            password: '',
            showPassword: false,
        };
    },

    components: {
        Alert,
    },

    setup() {
        const loadingStore = useLoadingStore();
        const authStore = useAuthStore();
        const benefeciaryStore = useBenefeciaryStore();
        return {
            loadingStore,
            authStore,
            benefeciaryStore,
        };
    },

    computed: {
        isFormValid() {
            return (this.password);
        },

    },
    methods: {
        requiredRule(v) {
            return !!v || 'This field is required';
        },

        async saveNewPassword() {
            this.loadingStore.show("Saving...")
            try {
                const pincode = history.state.pincode;
                const payload = {
                    pincode: pincode,
                    password: this.password,
                };
                const response = await this.benefeciaryStore.saveNewPasswordStore(payload);
                if (response.success === true) {
                    this.$router.push({
                        path: '/success',
                        state: { successText: response.message }
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
    }
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