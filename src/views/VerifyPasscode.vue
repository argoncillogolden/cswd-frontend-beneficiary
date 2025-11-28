<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <v-row>
        <v-col cols="12">
            <div class="top-container">
                <div class="d-flex justify-space-between">
                    <h3 class="mt-14 ms-3">Verify Passcode</h3>
                    <img :src="logo" loading="lazy" alt="Poofsa Logo" />
                </div>
            </div>
        </v-col>
        <v-col cols="12" class="pt-0">
            <v-icon class="ms-2" @click="this.$router.go(-1);">mdi-arrow-left</v-icon>
            <v-container>
                <v-card class="pa-5">
                    <v-card-text>
                        <p class="mb-5">To continue, we have sent a passcode to your email. Please check and fill out the given field below.</p>
                        <v-form @submit.prevent="sendPasscode">
                            <v-label>Passcode</v-label>
                            <v-text-field v-model="passcode" :rules="[requiredRule]" density="compact" placeholder="Type here...">
                            </v-text-field>
                        </v-form>
                    </v-card-text>
                    <v-spacer></v-spacer>
                    <div class="d-flex justify-space-between ms-2 mb-3">
                        <v-btn prepend-icon="mdi-save" height="30" color="primary" variant="tonal"
                            @click="sendPasscode">Submit
                        </v-btn>
                    </div>
                </v-card>
                <div class="d-flex justify-center">
                    <v-btn color="blue" class="back-to-home" prepend-icon="mdi-home-outline" @click="this.$router.push('/home')">Back to Home</v-btn>
                </div>
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
    name: 'VerifyPasscode',
    components: {
        Alert,
    },
    data() {
        return {
            passcode: '',
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
            return (this.passcode);
        },
    },

    methods: {

        requiredRule(v) {
            return !!v || 'This field is required';
        },

        async sendPasscode() {
            this.loadingStore.show("Saving...")
            try {
                const payload = {
                    passcode: this.passcode,
                    beneficiary_id: this.authStore.beneficiaryId,
                };
                const response = await this.benefeciaryStore.sendPasscodeStore(payload);
                if (response.success === true) {
                    this.$router.push('/success');
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

.back-to-home {
    text-transform: none;
    height: 40px;
    margin-top: 50px;
}
</style>