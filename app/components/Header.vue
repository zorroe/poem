<script setup lang="ts">

const userName = ref('')
const avatarUrl = ref('')

onMounted(async () => {
    const client = useSupabaseClient()
    const { data } = await client.auth.getUser()
    userName.value = data.user?.user_metadata.full_name || ''
    avatarUrl.value = data.user?.user_metadata.avatar_url || ''
})

const rightItems = computed(() => {
    return [
        [
            {
                label: `${userName.value || ''}`,
                avatar: {
                    src: `${avatarUrl.value}`
                },
                type: 'label'
            }
        ],
        [

            {
                label: '设置',
                icon: 'i-lucide-cog'
            }
        ],
        [
            {
                label: '退出',
                icon: 'i-lucide-log-out',
                onSelect: async () => {
                    const supabase = useSupabaseClient()
                    await supabase.auth.signOut()
                    navigateTo('/login')
                }
            }
        ]
    ]
})

</script>

<template>
    <UHeader>
        <template #right>
            <UColorModeButton />
            <UTooltip text="Open on GitHub" :kbds="['meta', 'G']">
                <UButton color="neutral" variant="ghost" to="https://github.com/zorroe/poem" target="_blank"
                    icon="i-simple-icons-github" aria-label="GitHub" />
            </UTooltip>
            <UDropdownMenu :items="rightItems">
                <UButton icon="i-lucide-menu" color="neutral" variant="outline" />
            </UDropdownMenu>
        </template>
    </UHeader>
</template>
