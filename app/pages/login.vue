<script setup lang="ts">
import * as z from 'zod'
import type { FormSubmitEvent, AuthFormField } from '@nuxt/ui'

const supabase = useSupabaseClient()
const toast = useToast()

const fields: AuthFormField[] = [{
    name: 'email',
    type: 'email',
    label: '邮箱',
    placeholder: '请输入邮箱',
    required: true
}, {
    name: 'password',
    label: '密码',
    type: 'password',
    placeholder: '请输入密码',
    required: true
}, {
    name: 'remember',
    label: '记住我',
    type: 'checkbox'
}]

const schema = z.object({
    email: z.email('Invalid email'),
    password: z.string('Password is required').min(8, 'Must be at least 8 characters')
})

type Schema = z.output<typeof schema>

async function onSubmit(payload: FormSubmitEvent<Schema>) {
    const { error } = await supabase.auth.signInWithPassword({
        email: payload.data.email,
        password: payload.data.password
    })
    if (error) {
        toast.add({ description: '账号或密码错误', color: 'error' })
    } else {
        toast.add({ description: '登陆成功', color: 'success' })
        navigateTo('/')
    }
}
</script>

<template>
    <div class="flex flex-col items-center justify-center gap-4 p-4">
        <UPageCard class="w-full max-w-md">
            <UAuthForm :schema="schema" title="登录" :submit="{ label: '登录' }" description="输入你的账号信息以登录"
                submit-label="Login" icon="i-lucide-user" :fields="fields" @submit="onSubmit">
                <template #description>
                    还没有账号？ <ULink to="/register" class="text-primary font-medium">注册</ULink>.
                </template>
            </UAuthForm>
        </UPageCard>
    </div>
</template>
