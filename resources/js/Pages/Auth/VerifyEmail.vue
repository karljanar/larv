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
            Tänud registreerumast! Enne alustamist, võiks kontrollida oma emaili aadress klõpsates lingil me lihtsalt saadetakse teile? Kui te ei saanud e-kirja, saadame teile hea meelega teise.
        </div>

        <div class="mb-4 font-medium text-sm text-green-600" v-if="verificationLinkSent" >
            Registreerimise käigus esitatud emaili aadressile on saadetud Uus kinnituslink.
        </div>

        <form @submit.prevent="submit">
            <div class="mt-4 flex items-center justify-between">
                <jet-button :class="{ 'opacity-25': form.processing }" :disabled="form.processing">
                    Saada Kinnitusmeil Uuesti
                </jet-button>

                <inertia-link :href="route('logout')" method="post" as="button" class="underline text-sm text-gray-600 hover:text-gray-900">Log Out</inertia-link>
            </div>
        </form>
    </jet-authentication-card>
</template>

<script>
    import JetAuthenticationCard from '@/Jetstream/AuthenticationCard'
    import JetAuthenticationCardLogo from '@/Jetstream/AuthenticationCardLogo'
    import JetButton from '@/Jetstream/Button'

    export default {
        components: {
            JetAuthenticationCard,
            JetAuthenticationCardLogo,
            JetButton,
        },

        props: {
            status: String
        },

        data() {
            return {
                form: this.$inertia.form()
            }
        },

        methods: {
            submit() {
                this.form.post(this.route('verification.send'))
            },
        },

        computed: {
            verificationLinkSent() {
                return this.status === 'verification-link-sent';
            }
        }
    }
</script>
