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
    name: 'passwordConfirm',
    label: '确认密码',
    type: 'password',
    placeholder: '请再次输入密码',
    required: true
}]

const schema = z.object({
    email: z.email('邮箱名无效'),
    password: z.string('密码不能为空').min(8, '密码不能少于8个字符'),
    passwordConfirm: z.string('请再次输入密码').min(8, '密码不能少于8个字符')
}).refine((data) => data.password === data.passwordConfirm, {
    message: '两次输入的密码不一致',
    path: ['passwordConfirm']
})

type Schema = z.output<typeof schema>

async function onSubmit(payload: FormSubmitEvent<Schema>) {
    const { error } = await supabase.auth.signUp({
        email: payload.data.email,
        password: payload.data.password,
        options: {
            emailRedirectTo: `http://localhost:3000`
        }
    })
    if (error) {
        toast.add({ description: '注册失败，请重试', color: 'error' })
    } else {
        toast.add({ description: '注册成功，请检查邮箱以验证账号', color: 'success' })
    }
}
</script>

<template>
    <div class="flex flex-col items-center justify-center gap-4 p-4">
        <UPageCard class="w-full max-w-md">
            <UAuthForm :schema="schema" title="注册" :submit="{ label: '注册' }" description="输入你的账号信息以登录"
                submit-label="Login" icon="i-lucide-user" :fields="fields" @submit="onSubmit">
            </UAuthForm>
        </UPageCard>
    </div>
</template>
