<template>
    <div>
        <app-layout>

            <div class="py-12">

                <div class="max-w-full mx-auto sm:px-6 lg:px-8">

                    <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg px-4 py-4">


                        <div class="bg-teal-100 border-t-4 border-teal-500 rounded-b text-teal-900 px-4 py-3 shadow-md my-3" role="alert" v-if="this.$page.props.flash.message">

                            <div class="flex">

                                <div>

                                    <p class="text-sm">{{ this.$page.props.flash.message }}</p>

                                </div>

                            </div>

                        </div>

                        <td class="space-x-4 pb-8">
                            <button @click="openModal()" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded my-3 text-center">Lisa rakendus</button>
                            <button v-if="isAdmin === 1" @click="openFrameworkModal()" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded my-3 text-center">Lisa Raamistik</button>
                        </td>

                        <table class="table-fixed w-full break-words">
                            <thead>
                            <tr class="bg-gray-100">
                                <th class="px-4 py-2 border-2 border-gray-200 ">Nimetus</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Raamistik</th>
                                <th class="px-4 py-2 border-2 border-gray-200">URL</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Reaalne URL</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Praegune versioon</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Rakenduse asukoht serveris</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Tellija</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Tehniline vastutaja</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Sisu vastutaja</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Kommentaarid</th>
                                <th class="px-4 py-2 border-2 border-gray-200">Muuda</th>
                            </tr>
                            </thead>
                            <tbody>
                            <tr v-for="row in apps" :key="row.id">
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.user_app_name }}</td>
                                <template v-for="val in framework">
                                    <td v-if="row.framework_id === val.id" class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ val.framework_name }}</td>
                                </template>
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.app_url }}</td>
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.real_app_url }}</td>
                                <td v-if="row.update_available === 1" class="bg-red-600 px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.current_version }}</td>
                                <td v-else class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.current_version }}</td>
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.app_loc_in_server }}</td>
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.service_subscriber_name }}</td>
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.technical_supervisor_name }}</td>
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200">{{ row.content_supervisor_name }}</td>
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200">
                                    <button @click="commentsModal(row)" class="bg-green-500 hover:bg-green-700 text-sm text-white font-bold py-2.5 px-2.5 w-full rounded">Kommentaarid</button>
                                </td>
                                <td class="px-2 py-2 w-1/4 border-2 border-gray-200 space-y-2">
                                    <template v-for="email in notifications">
                                    <template v-if="email.user_apps_id === row.id">
                                        <button v-if="isAdmin === 1" @click="edit(row, email)" class="bg-blue-500 hover:bg-blue-700 text-sm text-white font-bold py-2.5 px-2.5 w-full rounded">Muuda</button>
                                        <button v-if="isAdmin === 1" @click="deleteRow(row)" class="bg-red-500 hover:bg-red-700 text-sm text-white font-bold py-2.5 px-2.5 w-full rounded">Kustuta</button>
                                        <button v-else @click="edit(row, email)" class="bg-blue-500 hover:bg-blue-700 text-sm text-white font-bold py-2.5 px-2.5 w-full rounded">Muuda</button>
                                    </template>
                                    </template>

                                </td>
                            </tr>
                            </tbody>
                        </table>


                        <div class="fixed z-10 inset-0 overflow-y-auto ease-out duration-400" v-if="isCommentsModal">

                            <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">

                                <div class="fixed inset-0 transition-opacity">

                                    <div @click="closeCommentsModal" class="absolute inset-0 bg-gray-500 opacity-75"></div>

                                </div>

                                <!-- This element is to trick the browser into centering the modal contents. -->

                                <span class="hidden sm:inline-block sm:align-middle sm:h-screen"></span>

                                <div class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full" role="dialog" aria-modal="true" aria-labelledby="modal-headline">

                                    <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
                                        <form>
                                            <div class="">
                                                <div class="mb-4">
                                                    <p v-if="form.comments" style="white-space: pre-wrap;">{{ commentSplit }}</p>
                                                    <p v-else>Tühjus</p>
                                                </div>
                                                <button @click="closeCommentsModal()" type="button" class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5">
                                                    Sulge
                                                </button>
                                            </div>
                                        </form>
                                    </div>
                                </div>

                            </div>
                        </div>


                        <div class="fixed z-10 inset-0 overflow-y-auto ease-out duration-400" v-if="isFrameworkModal">
                            <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
                                <div class="fixed inset-0 transition-opacity">
                                    <div @click="closedFrameworkModal()" class="absolute inset-0 bg-gray-500 opacity-75"></div>
                                </div>
                                <!-- This element is to trick the browser into centering the modal contents. -->
                                <span class="hidden sm:inline-block sm:align-middle sm:h-screen"></span>
                                <div class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full" role="dialog" aria-modal="true" aria-labelledby="modal-headline">

                                    <form>

                                        <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">

                                            <div class="">

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Raamistiku Nimetus *</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500"
                                                           name="app_url"
                                                           placeholder="Raamistiku nimetus"
                                                           v-model="form.framework_name">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>

                                                </div>

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Raamistiku versioon *</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500"
                                                           name="app_url"
                                                           placeholder="Raamistiku versioon"
                                                           v-model="form.new_framework_version">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>

                                                </div>

                                            </div>

                                        </div>

                                        <div class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">

                                        <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">

                                            <button v-if="form.new_framework_version && form.framework_name" wire:click.prevent="store()" type="button" class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-green-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-green-500 focus:outline-none focus:border-green-700 focus:shadow-outline-green transition ease-in-out duration-150 sm:text-sm sm:leading-5" @click="saveFramework(form)">
                                                Salvesta
                                            </button>

                                            <button v-else :disabled="true" type="button" class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-gray-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-red-500 focus:outline-none focus:border-green-700 focus:shadow-outline-green transition ease-in-out duration-150 sm:text-sm sm:leading-5" @click="saveFramework(form)">
                                                Salvesta
                                            </button>

                                        </span>
                                        <span class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto">
                                            <button @click="closedFrameworkModal()" type="button" class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5">
                                                Tühista
                                            </button>

                                        </span>

                                        </div>
                                    </form>
                                </div>
                            </div>
                        </div>

                        <div class="fixed z-10 inset-0 overflow-y-auto ease-out duration-400" v-if="isOpen">

                            <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">



                                <div class="fixed inset-0 transition-opacity">

                                    <div @click="closeModal()" class="absolute inset-0 bg-gray-500 opacity-75"></div>

                                </div>

                                <!-- This element is to trick the browser into centering the modal contents. -->

                                <span class="hidden sm:inline-block sm:align-middle sm:h-screen"></span>

                                <div class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-3xl sm:w-full" role="dialog" aria-modal="true" aria-labelledby="modal-headline">

                                    <form>
                                        <p class="text-left ml-6 pt-16 font-bold">Tärniga märgitud väljad on kohustuslikud!</p>
                                        <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">

                                            <div class="inline-grid grid-cols-2 gap-x-20">

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Nimetus *</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                           name="app_url"
                                                           placeholder="Rakenduse nimetus"
                                                           v-model="form.user_app_name">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>

                                                </div>

                                                <div class="mb-4" >
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Raamistik *</label>
                                                    <select name="framework_id" id="framework_id"
                                                            class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                            v-model="form.framework_id">

                                                        <option v-for="val in framework" :value="val.id">{{val.framework_name}}</option>

                                                    </select>

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>

                                                </div>

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Rakenduse URL</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                           name="app_url"
                                                           placeholder="Rakenduse URL..."
                                                           v-model="form.app_url">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>

                                                </div>

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Rakenduse Reaalne URL</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                           name="real_app_url"
                                                           placeholder="Rakenduse tegelik URL..."
                                                           v-model="form.real_app_url">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>
                                                </div>

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Praegune Versioon *</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                           name="current_version"
                                                           placeholder="Praegune versioon..."
                                                           v-model="form.current_version">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>
                                                </div>


                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Rakenduse Asukoht Serveris *</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                           name="app_loc_in_server"
                                                           placeholder="Rakenduse asukoht serveris..."
                                                           v-model="form.app_loc_in_server">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>
                                                </div>

                                                <div class="mb-4">

                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Kommentaarid</label>
                                                    <textarea class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                              name="comments" placeholder="Kommentaarid..."
                                                              v-model="form.comments"> </textarea>

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>
                                                </div>

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Rakenduse Tellija</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                           name="service_subscriber_name"
                                                           placeholder="Rakenduse tellija nimi..."
                                                           v-model="form.service_subscriber_name">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>
                                                </div>

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Tehniline Vastutaja</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                           name="technical_supervisor_name"
                                                           placeholder="Rakenduse tehniline vastutaja..."
                                                           v-model="form.technical_supervisor_name">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>
                                                </div>

                                                <div class="mb-4">
                                                    <label class="block shadow-5xl  w-80 italic placeholder-gray-500">Sisu Vastutaja</label>
                                                    <input type="text" class="block shadow-5xl mb-10 p-2 w-80 italic placeholder-gray-500 rounded"
                                                           name="content_supervisor_name"
                                                           placeholder="Rakenduse sisu vastutaja..."
                                                           v-model="form.content_supervisor_name">

                                                    <div v-if="this.$page.props.errors.title" class="text-red-500">{{ this.$page.props.errors.title[0] }}</div>
                                                </div>

                                                <div v-if="editMode" class="inline-grid grid-cols-2 gap-x-80">
                                                    <label class="block shadow-5xl w-80 font-bold ">Teavituste tellimiseks tehke märge kastis</label>
                                                    <input
                                                           type="checkbox" class="form-checkbox h-8 w-8 border-2 text-green-600 rounded cursor-pointer"
                                                           v-if="emailNotificaitonForm.notification_enabled === 1"
                                                           @change="emailNotificaitonForm.notification_enabled = 0"
                                                           checked
                                                    >
                                                    <input
                                                        type="checkbox" class="form-checkbox h-8 w-8 border-2 text-green-600 rounded cursor-pointer"
                                                        v-else
                                                        @change="emailNotificaitonForm.notification_enabled = 1"
                                                    >
                                                </div>


                                            </div>
                                        </div>
                                        <div class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">

                                            <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">

                                                <button v-if="form.user_app_name && form.framework_id && form.current_version && form.app_loc_in_server" wire:click.prevent="store()" type="button" class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-green-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-green-500 focus:outline-none focus:border-green-700 focus:shadow-outline-green transition ease-in-out duration-150 sm:text-sm sm:leading-5" v-show="!editMode" @click="save(form)">
                                                    Salvesta
                                                </button>

                                                <button v-else :disabled="true" type="button" class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-gray-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-red-500 focus:outline-none focus:border-green-700 focus:shadow-outline-green transition ease-in-out duration-150 sm:text-sm sm:leading-5" v-show="!editMode" @click="save(form)">
                                                    Salvesta
                                                </button>

                                            </span>

                                            <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">

                                                <button v-if="form.user_app_name && form.framework_id && form.current_version && form.app_loc_in_server" wire:click.prevent="store()" type="button" class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-green-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-green-500 focus:outline-none focus:border-green-700 focus:shadow-outline-green transition ease-in-out duration-150 sm:text-sm sm:leading-5" v-show="editMode" @click="update(form, emailNotificaitonForm)">
                                                    Muuda
                                                </button>

                                                <button v-else :disabled="true" type="button" class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-gray-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-red-500 focus:outline-none focus:border-green-700 focus:shadow-outline-green transition ease-in-out duration-150 sm:text-sm sm:leading-5" v-show="editMode" @click="update(form)">
                                                   Muuda
                                                </button>

                                            </span>

                                            <span class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto">

                                              <button @click="closeModal()" type="button" class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5">
                                                Tühista
                                              </button>

                                            </span>

                                        </div>

                                    </form>

                                </div>

                            </div>

                        </div>

                    </div>

                </div>

            </div>

        </app-layout>
    </div>
</template>

<script>

import AppLayout from './../Layouts/AppLayout'

import Welcome from './../Jetstream/Welcome'

import { ref } from 'vue';

import Modal from "@/Jetstream/Modal";
import Label from "@/Jetstream/Label";
import Button from "@/Jetstream/Button";

export default {

    components: {
        Button,
        Label,
        Modal,

        AppLayout,

        Welcome,

    },

    props: ['apps', 'framework', 'errors', 'isAdmin', 'notifications'],


    data() {


        return {

            isFrameworkModal: false,

            isCommentsModal: false,

            editMode: false,

            isOpen: false,

            form: {
                title: null,

                framework_id: null,

                user_app_name: null,

                real_app_url: null,

                app_url: null,

                current_version: null,

                app_loc_in_server: null,

                comments: null,

                service_subscriber_name: null,

                technical_supervisor_name: null,

                content_supervisor_name: null,

                framework_name: null,

                new_framework_version: null,


            },

            emailNotificaitonForm:{
                notification_enabled: null
            }

        }

    },

    computed: {
        commentSplit: function() {

            return this.form.comments.split('[');

        }
    },

    methods: {


        openFrameworkModal: function(){
            this.isFrameworkModal = true;
        },

        closedFrameworkModal: function () {

            this.isFrameworkModal = false;

        },

        commentsModal: function(data){
            this.form = Object.assign({}, data);

            this.openCommentsModal();
        },

        openCommentsModal: function(){
            this.isCommentsModal = true;
        },

        openModal: function () {

            this.isOpen = true;

        },

        closeCommentsModal: function () {

            this.isCommentsModal = false;

        },

        closeModal: function () {

            this.isOpen = false;

            this.reset();

            this.editMode = false;

        },

        reset: function () {

            this.form = {
                title: null,

                body: null,

                framework_id: null,

                user_app_name: null,

                real_app_url: null,

                app_url: null,

                current_version: null,

                app_loc_in_server: null,

                comments: null,

                service_subscriber_name: null,

                technical_supervisor_name: null,

                content_supervisor_name: null,

                framework_name: null,

                new_framework_version: null,


            };
            this.emailNotificaitonForm = {
                notification_enabled: null
            }

        },

        save: function (data) {

            this.$inertia.post('/apps', data)

            this.reset();

            this.closeModal();

            this.editMode = false;


        },

        saveFramework: function (data) {

            this.$inertia.post('/apps/framework', data)

            this.reset();

            this.closedFrameworkModal();

        },

        edit: function (data, email) {

            this.form = Object.assign({}, data);

            this.emailNotificaitonForm = Object.assign({}, email);

            this.editMode = true;

            this.openModal();

        },

        update: function (data, emailNotification) {

            data._method = 'PATCH';

            this.$inertia.post('/apps/edit/' + emailNotification.notification_enabled, data);

            this.reset();

            this.closeModal();

        },


        editNotification: function (data) {

            data._method = 'PATCH';

            this.$inertia.post('/apps/editNotification/' + data.user_apps_id, data);

            this.reset();

            this.closeModal();

        },

        deleteRow: function (data) {

            if (!confirm('Kas te olete kindlat, et soovite antud rakendust kustutada?')) return;

            data._method = 'DELETE';

            this.$inertia.post('/apps/destroy/' + data.id, data)

            this.reset();

            this.closeModal();

        }
    }

}

</script>
