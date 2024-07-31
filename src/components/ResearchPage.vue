<script lang="ts" setup>
import { useAuthStore } from '../store/auth';
import { onMounted, ref } from 'vue';
import router from '../router';
import {coreClient}  from '../utils/axios';
import {getPrettyDate} from '../utils/prettyDate';

const authStore = useAuthStore();


interface Research {
    name: string;
    id: number;
    status: string;
    created_at: string;
    updated_at: string;
    created_by: string;
    day_start: string;
    day_end: string;
    n_samples: number;
    comment: string;
    approval_required: boolean;
    
}
const research = ref<Research>();



onMounted(async () => {
    if (!authStore.accessToken && authStore.role && authStore.role.name !== 'admin') {
        await authStore.logout();
        router.push('/login');
    }
    else{
        try {
            const response = await coreClient.get<Research>('/researches/'+router.currentRoute.value.params.id);
                research.value = response.data;
        } catch (error) {
            console.error('Error fetching kit details:', error);
        }
    }
});
</script>


<template>
        <div v-if="research" class="research-details">
            <h1 class="research-name">{{ research?.name }}</h1>
            <p class="research-comment">{{ research?.comment }}</p>
            <div class="research-info">
                <p><strong>Статус:</strong> {{ research?.status }}</p>
                <p><strong>Дата начала:</strong> {{ getPrettyDate(research?.day_start) }}</p>
                <p v-if="research?.day_end"><strong>Дата окончания:</strong> {{ getPrettyDate(research?.day_end) }}</p>
                <p><strong>Собрано образцов:</strong> {{ research?.n_samples }}</p>
                <p><strong>Необходимо подтверждение:</strong> {{ research?.approval_required ? 'Да' : 'Нет' }}</p>
            </div>
        </div>
</template>

<style scoped>


.research-details {
    padding: 20px;
    border-radius: 8px;
}

.research-name {
    font-weight: bold;
    margin-bottom: 10px;
}

.research-comment {
    font-size: 16px;
    margin-bottom: 20px;
}

.research-info {
    font-size: 14px;
}
</style>
