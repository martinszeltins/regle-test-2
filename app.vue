<template>
    <div class="p-5 space-y-5 origin-top-left">
        <h1 class="text-xl font-semibold">My Form</h1>

        <div>
            <pre>isSkillNameRequired: {{ isSkillNameRequired }}</pre>
        </div>

        <!-- Dump "form" -->
        <div style="border: 1px solid gray; position: fixed; right: 750px; top: 20px; max-height: calc(100vh - 100px); width: 300px; font-size: 11px; overflow:auto;padding: 10px;background: #f8f8f8;border-radius: 4px;font-family: monospace;"><pre><b>form:</b> {{ form }}</pre></div>

        <!-- Dump "errors" -->
        <div style="border: 1px solid gray; position: fixed; right: 430px; top: 20px; max-height: calc(100vh - 100px); width: 300px; font-size: 11px; overflow:auto;padding: 10px;background: #f8f8f8;border-radius: 4px;font-family: monospace;"><pre><b>errors:</b> {{ errors }}</pre></div>

        <!-- Dump "regle" -->
        <div style="border: 1px solid gray; position: fixed; right: 10px; top: 20px; max-height: calc(100vh - 100px); width: 400px; font-size: 11px; overflow:auto;padding: 10px;background: #f8f8f8;border-radius: 4px;font-family: monospace;"><pre><b>regle:</b> {{ regle }}</pre></div>

        <form @submit.prevent="submit">
            <div>
                <label for="name">Name</label>
                <input v-model="form.name" type="text" class="border border-gray-300 shadow ml-4">
                <ul>
                    <li v-for="error of errors.name" :key="error">{{ error }}</li>
                </ul>
            </div>

            <div>
                <label for="email">Email</label>
                <input v-model="form.email" type="text" class="border border-gray-300 shadow ml-4">
                <ul>
                    <li v-for="error of errors.email" :key="error">{{ error }}</li>
                </ul>
            </div>

            <!-- Skills, add skills -->
            <div class="mt-4">
                <label for="skills">Skills</label>
                <button
                    @click="form.skills.push({ skillName: '', level: 0 })"
                    type="button"
                    class="shadow mb-2 bg-gray-200 px-5 py-1 rounded hover:bg-gray-300 hover:active:bg-gray-400 ml-4">

                    Add Skill
                </button>

                <div v-for="(skill, index) of form.skills" :key="index">
                    <input v-model="skill.skillName" type="text" class="border border-gray-300 shadow ml-4">
                    <input v-model="skill.level" type="number" class="border border-gray-300 shadow ml-4">
                </div>
            </div>

            <button type="submit" class="shadow mt-6 bg-blue-200 px-5 py-2 rounded hover:bg-blue-300 hover:active:bg-blue-400">
                Submit
            </button>

            <button
                @click="form.email = 'test'"
                type="button"
                class="shadow mt-4 bg-gray-200 px-5 py-2 rounded hover:bg-gray-300 hover:active:bg-gray-400 ml-4">

                email = 'test'
            </button>

            <button
                @click="touchSkillName"
                type="button"
                class="shadow mt-4 bg-gray-200 px-5 py-2 rounded hover:bg-gray-300 hover:active:bg-gray-400 ml-4">

                touch skills[0].skillName
            </button>

            <button
                @click="isSkillNameRequired = !isSkillNameRequired"
                type="button"
                class="shadow mt-4 bg-gray-200 px-5 py-2 rounded hover:bg-gray-300 hover:active:bg-gray-400 ml-4">

                isSkillNameRequired = !isSkillNameRequired
            </button>
        </form>
    </div>
</template>

<script setup lang="ts">
    import { useRegle } from '@regle/core'
    import { email, required } from '@regle/validators'

    interface Skill {
        skillName: string
        level: number
    }

    interface Form {
        name: string
        email: string
        skills: Skill[]
    }

    const isSkillNameRequired = ref(false)

    const form = ref<Form>({
        name: '',
        email: '',
        skills: [],
    })

    const { errors, validateForm, regle } = useRegle(form, () => ({
        name: {
            required,
        },

        email: {
            email: isSkillNameRequired.value ? email : undefined,
            required: isSkillNameRequired.value ? required : undefined,
        },

        skills: {
            $each: {
                skillName: {
                    required: isSkillNameRequired.value ? required : undefined,
                },

                level: {
                    required,
                },
            },
        }
    }), { autoDirty: true })

    const submit = async () => {
        const result = await validateForm()

        console.log(result)
    }

    const touchSkillName = () => {
        regle.$fields.skills.$each[0].$fields.skillName.$touch()
    }
</script>
