<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <v-container>
        <div class="success-container">
            <v-icon>mdi-check-circle-outline</v-icon>
            <h4>Success!</h4>
            <h5>{{ successText }}</h5>
            <v-btn color="blue" @click="signout">Login to continue</v-btn>
        </div>
    </v-container>
</template>

<script>
import { useTheme } from 'vuetify';
import { ref, computed } from 'vue';
import { useLoadingStore } from '@/stores/loading';
import { useAuthStore } from '@/stores/auth';

export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name: 'Success',

    data() {
        return {
            successText: history.state.successText || 'Your email has been updated successfully!',
        }
    },

    setup() {
        const loadingStore = useLoadingStore();
        const authStore = useAuthStore();
        const theme = useTheme();
        const selectedTheme = ref(theme.global.name.value);
        const currentThemeName = computed(() => {
            return theme.global.name.value === 'dark' ? 'Dark' : 'Light';
        });
        const applyTheme = () => {
            theme.global.name.value = selectedTheme.value;
            localStorage.setItem('theme', selectedTheme.value);
        };
        return {
            loadingStore,
            authStore,
            theme,
            selectedTheme,
            currentThemeName,
            applyTheme,
        };
    },

    methods: {

        async signout() {
            this.loadingStore.show("");
            await this.authStore.logout();
        }

    },
};
</script>

<style scoped>

.success-container {
    display: grid;
    place-items: center;
    margin-top: 50px;
}

.v-icon {
    font-size: 100px !important;
    color: #018d30;
}

h4 {
    font-size: 30px;
    color: #018d30;
    font-weight: bold;
    margin-top: 10px;
}

h5 {
    color: #018d30;
    font-weight: normal;
}

.v-btn {
    text-transform: none;
    height: 40px;
    margin-top: 50px;
}
</style>