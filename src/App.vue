<template>
  <div class="">
    <header
      class="h-16 w-full bg-indigo-200 px-6 flex justify-between items-center"
    >
      <h1 class="text-xl text-indigo-900">Vue-Realm</h1>
      <button
        class="text-lg text-white capitalize px-6 py-2 bg-indigo-900 rounded-md"
        @click="handleModal(true)"
      >
        create
      </button>
    </header>
    <section className="mt-10 flex justify-center px-6">
      <ul className="w-full">
        <li
          v-for="user in users"
          :key="user._id"
          className="border-2 p-6 mb-3 rounded-lg flex items-center"
        >
          <section
            className="h-10 w-10 bg-indigo-100 rounded-md flex justify-center items-center mr-4"
          >
            <UserIcon />
          </section>
          <section className="">
            <h2 className="capitalize font-semibold mb-1">{{ user.name }}</h2>
            <p className="capitalize text-gray-500 mb-1">{{ user.location }}</p>
            <p className="capitalize text-indigo-500 font-medium text-sm mb-2">
              {{ user.title }}
            </p>
            <div className="flex">
              <button
                className="text-sm text-red-500 capitalize px-4 py-2 mr-4 border border-red-500 rounded-md"
                @click="deleteAUser(user._id)"
              >
                delete
              </button>
              <button
                className="text-sm text-white capitalize px-4 py-2 bg-indigo-900 rounded-md"
                @click="handleEditClick(user._id)"
              >
                edit
              </button>
            </div>
          </section>
        </li>
      </ul>
    </section>
    <Modal
      :isModal="isModal"
      :isEdit="isEdit"
      :handleModal="handleModal"
      :updateUserValue="updateUserValue"
      :editingId="editingId"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import UserIcon from '@/assets/svg/UserIcon.vue';
import Modal from '@/components/Modal.vue';
import { IUser } from '@/models/user.interface';
import { app, credentials } from '@/utils/mongo.client';
import { BSON } from 'realm-web';

export default defineComponent({
  name: 'App',

  components: {
    UserIcon,
    Modal,
  },

  data: () => ({
    isModal: false,
    isEdit: false,
    users: [] as IUser[],
    userValue: '',
    editingId: '',
  }),

  methods: {
    handleModal(state: boolean) {
      this.isModal = state;
      this.isEdit = false;
    },

    handleEditClick(id: string) {
      this.isModal = true;
      this.isEdit = true;
      this.editingId = id;
    },

    async getListOfUsers() {
      const user: Realm.User = await app.logIn(credentials);
      const listOfUser: Promise<IUser[]> = user.functions.getAllUsers();
      listOfUser.then((resp) => {
        this.users = resp;
      });
    },

    updateUserValue(value: any) {
      this.userValue = value;
    },

    async deleteAUser(id: string) {
      const user: Realm.User = await app.logIn(credentials);
      const delUser = user.functions.deleteUser(
        new BSON.ObjectID(id).toString()
      );
      delUser.then((resp) => {
        this.updateUserValue(resp.deletedCount);
      });
    },
  },

  watch: {
    userValue(latestValue) {
      if (latestValue !== '') {
        this.getListOfUsers();
      }
    },
  },

  mounted() {
    this.getListOfUsers();
  },
});
</script>

<style></style>
