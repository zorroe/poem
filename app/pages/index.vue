<script setup>
const client = useSupabaseClient()
const poems = ref([])

async function getPoem() {
    const { data } = await client.from('poem')
        .select()
    poems.value = data
}

onMounted(() => {
    getPoem()
})
</script>

<template>
    <ul>
        <li v-for="poem in poems" :key="poem.id">{{ poem.title }}</li>
    </ul>
</template>