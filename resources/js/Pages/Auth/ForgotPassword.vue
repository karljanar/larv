<template>
    <jet-authentication-card>
        <template #logo>
            <inertia-link :href="route('login')" class="text-sm text-2xl text-gray-600 hover:text-gray-900">
                Logi Sisse
            </inertia-link>
            <inertia-link :href="route('register')" class="text-sm text-2xl text-gray-600 hover:text-gray-900">
                Registreeru
            </inertia-link>
        </template>

        <div class="mb-4 text-sm text-gray-600">
            Unustasid oma parooli? Pole probleemi. Lihtsalt andke meile teada oma emaili aadress ja me saadame teile parooli lähtestamise lingi, mis võimaldab teil valida uue.
        </div>

        <div v-if="status" class="mb-4 font-medium text-sm text-green-600">
            {{ status }}
        </div>

        <jet-validation-errors class="mb-4" />

        <form @submit.prevent="submit">
            <div>
                <jet-label for="email" value="Email" />
                <jet-input id="email" type="email" class="mt-1 block w-full" v-model="form.email" required autofocus />
            </div>

            <div class="flex items-center justify-end mt-4">
                <jet-button :class="{ 'opacity-25': form.processing }" :disabled="form.processing">
                    Emaili Parooli Lähtestamise Link
                </jet-button>
            </div>
        </form>
    </jet-authentication-card>
</template>

<script>
    import JetAuthenticationCard from '@/Jetstream/AuthenticationCard'
    import JetAuthenticationCardLogo from '@/Jetstream/AuthenticationCardLogo'
    import JetButton from '@/Jetstream/Button'
    import JetInput from '@/Jetstream/Input'
    import JetLabel from '@/Jetstream/Label'
    import JetValidationErrors from '@/Jetstream/ValidationErrors'

    export default {
        components: {
            JetAuthenticationCard,
            JetAuthenticationCardLogo,
            JetButton,
            JetInput,
            JetLabel,
            JetValidationErrors
        },

        props: {
            status: String
        },

        data() {
            return {
                form: this.$inertia.form({
                    email: ''
                })
            }
        },

        methods: {
            submit() {
                this.form.post(this.route('password.email'))
            }
        }
    }
</script>
