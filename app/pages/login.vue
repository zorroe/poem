<script setup lang="ts">
definePageMeta({
    layout: 'login'
})

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

const providers = [{
    label: 'GitHub',
    icon: 'i-simple-icons-github',
    onClick: () => {
        supabase.auth.signInWithOAuth({
            provider: 'github'
        })
    }
}]

const schema = z.object({
    email: z.email('请输入有效的邮箱'),
    password: z.string('请输入密码').min(8, '密码不能少于8个字符')
})

type Schema = z.output<typeof schema>

async function onSubmit(payload: FormSubmitEvent<Schema>) {
    const { error } = await supabase.auth.signInWithPassword({
        email: payload.data.email,
        password: payload.data.password
    })
    if (error) {
        toast.add({ title: '登陆失败', description: '账号或密码错误', color: 'error' })
    }
}
</script>

<template>
    <div class="flex flex-col items-center justify-center gap-4 p-4">
        <UPageCard class="w-full max-w-md">
            <UAuthForm :schema="schema" title="登录" :submit="{ label: '登录' }" description="输入你的账号信息以登录"
                submit-label="Login" icon="i-lucide-user" :fields="fields" :providers="providers" @submit="onSubmit">
                <template #description>
                    还没有账号？ <ULink to="/register" class="text-primary font-medium">注册</ULink>.
                </template>
            </UAuthForm>
        </UPageCard>
    </div>
</template>
