<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <v-row>
        <v-col cols="12">
            <div class="top-container">
                <div class="d-flex justify-space-between">
                    <h3 class="mt-14 ms-3">Change Email</h3>
                    <img :src="logo" loading="lazy" alt="Poofsa Logo" />
                </div>
            </div>
        </v-col>
        <v-col cols="12" class="pt-0">
            <v-icon class="ms-2" @click="this.$router.go(-1);">mdi-arrow-left</v-icon>
            <v-container>
                <v-card class="pa-3">
                    <v-card-text>
                        <v-form @submit.prevent="checkEmail">
                            <v-label>Current email</v-label>
                            <v-text-field v-model="email" :rules="[requiredRule, emailFormatRule]" density="compact">
                            </v-text-field>

                            <v-label>New email</v-label>
                            <v-text-field v-model="new_email" :rules="[requiredRule, emailFormatRule]" density="compact">
                            </v-text-field>
                        </v-form>
                    </v-card-text>
                    <v-spacer></v-spacer>
                    <div class="d-flex justify-space-between ms-2 pa-3">
                        <v-btn :disabled="!isFormValid" prepend-icon="mdi-save" height="30" color="primary"
                            @click="checkEmail">Next
                        </v-btn>
                    </div>
                </v-card>
            </v-container>
        </v-col>
    </v-row>

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
    name: 'ChangeEmail',
    components: {
        Alert,
    },
    data() {
        return {
            email: '',
            new_email: '',
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
            return (this.email, this.new_email);
        },
    },

    methods: {

        requiredRule(v) {
            return !!v || 'This field is required';
        },

        emailFormatRule(v) {
            const pattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
            return pattern.test(v) || 'Invalid email format';
        },

        async checkEmail() {
            this.loadingStore.show("Saving...")
            try {
                const payload = {
                    email: this.email,
                    new_email: this.new_email,
                    beneficiary_id: this.authStore.beneficiaryId,
                };
                const response = await this.benefeciaryStore.checkEmailStore(payload);
                if (response.success === true) {
                    this.$router.push('/verify-passcode');
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
.descriptionColor {
    color: #a3a3a3;
}

.v-icon {
    padding: 20px !important;
}

.top-container {
    min-height: 85px;
    width: 100%;
    height: 85px;
    background: url('@/assets/buildings.jpg');
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
}

img {
    width: 80px;
    margin: 5px;
}

.btn-list {
    display: flex !important;
    justify-content: space-between !important;
    border-radius: 20px;
    padding-top: 15px;
    padding-bottom: 15px;
    width: 100%;
    margin-bottom: 10px;
    background-color: transparent;
}

.chevron-icon {
    color: #3c70ff;
    position: absolute;
    right: 0px;
}

span {
    font-size: 14px;
}

.auth-person-img {
    width: 130px;
    height: 130px;
    border-radius: 10px;
    margin-top: 7px;
}
</style>