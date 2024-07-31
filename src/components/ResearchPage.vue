<script lang="ts" setup>
import { useAuthStore } from '../store/auth';
import { onMounted, ref } from 'vue';
import router from '../router';
import {coreClient}  from '../utils/axios';

const authStore = useAuthStore();


interface Research {
    id: number;
    name: string;
    description: string;
}
const research = ref<Research>();

const research_id = router.currentRoute.value.params.id;



onMounted(async () => {
    if (!authStore.accessToken && authStore.role && authStore.role.name !== 'admin') {
        await authStore.logout();
        router.push('/login');
    }
    else{
        try {
            const response = await coreClient.get<Research>('/researches/'+router.currentRoute.value.params.id+'/');
                research.value = response.data;
        } catch (error) {
            console.error('Error fetching kit details:', error);
        }
    }
});
</script>


<template>
    <div>
        <h1>Research {{  research_id }}</h1>
    </div>
</template>


<style lang="scss" scoped>


</style>