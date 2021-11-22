<template>
  <div
    class="h-screen w-screen bg-indigo-900 bg-opacity-30 z-30 top-0 fixed transform scale-105 transition-all ease-in-out duration-100"
    :class="isModal ? 'block' : 'hidden'"
  >
    <div
      class="flex flex-col justify-center items-center h-full w-full open-nav"
      @click="onClose"
    >
      <div class="flex justify-end w-11/12 lg:w-1/2 2xl:w-6/12">
        <div
          role="button"
          class="cursor-pointer w-6 h-6 rounded-full flex items-center justify-center bg-white"
          @click="handleModal()"
        >
          <CloseIcon />
        </div>
      </div>
      <section
        class="w-11/12 lg:w-1/2 2xl:w-6/12 bg-white flex justify-center items-center mt-5 rounded-lg"
      >
        <div class="w-11/12 py-8">
          <h2 class="capitalize text-xl text-gray-500 font-medium mb-4">
            {{ isEdit ? 'Edit User' : 'add user' }}
          </h2>
          <form @submit.prevent="onSubmitForm">
            <fieldset class="mb-4">
              <label class="block text-sm text-gray-500 capitalize mb-1"
                >name</label
              >
              <input
                type="text"
                name="name"
                v-model="name"
                required
                class="w-full h-10 border border-gray-500 rounded-sm px-4"
              />
            </fieldset>
            <fieldset class="mb-4">
              <label class="block text-sm text-gray-500 capitalize mb-1"
                >location</label
              >
              <input
                type="text"
                name="location"
                v-model="location"
                required
                class="w-full h-10 border border-gray-500 rounded-sm px-4"
              />
            </fieldset>
            <fieldset class="mb-4">
              <label class="block text-sm text-gray-500 capitalize mb-1"
                >title</label
              >
              <input
                type="text"
                name="title"
                v-model="title"
                required
                class="w-full h-10 border border-gray-500 rounded-sm px-4"
              />
            </fieldset>
            <button
              class="text-white capitalize px-6 py-2 bg-indigo-900 rounded-md w-full"
            >
              save
            </button>
          </form>
        </div>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import CloseIcon from '@/assets/svg/CloseIcon.vue';
import { IUser } from '@/models/user.interface';
import { app, credentials } from '@/utils/mongo.client';
import { BSON } from 'realm-web';

export default defineComponent({
  name: 'Modal',
  components: { CloseIcon },
  props: {
    isModal: Boolean,
    isEdit: Boolean,
    handleModal: Function,
    updateUserValue: Function as PropType<(value: any) => void>,
    editingId: String,
  },

  data: () => ({
    name: '',
    location: '',
    title: '',
  }),

  methods: {
    onClose(e: Event) {
      const target = e.target as HTMLDivElement;
      if (target.classList.contains('open-nav')) {
        this.handleModal!(false);
      }
    },

    async onSubmitForm() {
      const user: Realm.User = await app.logIn(credentials);
      if (this.isEdit) {
        const edit: Promise<IUser> = user.functions.editUser(
          new BSON.ObjectID(this.editingId).toString(),
          this.name,
          this.location,
          this.title
        );
        edit.then((resp) => {
          this.updateUserValue!(resp._id!);
          this.name = '';
          this.location = '';
          this.title = '';
          this.handleModal!(false);
        });
      } else {
        const create = user.functions.createUser(
          this.name,
          this.location,
          this.title
        );
        create.then((resp) => {
          this.updateUserValue!(resp.insertedId);
          this.name = '';
          this.location = '';
          this.title = '';
        });
      }
    },

    async getAUser() {
      const user: Realm.User = await app.logIn(credentials);
      const getUser: Promise<IUser> = user.functions.getSingleUser(
        new BSON.ObjectID(this.editingId).toString()
      );
      getUser.then((resp) => {
        this.name = resp.name;
        this.location = resp.location;
        this.title = resp.title;
      });
    },
  },

  watch: {
    isEdit(latestValue) {
      if (latestValue === true) {
        this.getAUser();
      } else {
        this.name = '';
        this.location = '';
        this.title = '';
      }
    },
  },
});
</script>
