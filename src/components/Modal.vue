<template>
  <div
    class="h-screen w-screen bg-indigo-900 bg-opacity-30 z-30 top-0 fixed transform scale-105 transition-all ease-in-out duration-100"
  >
    <div class="flex flex-col justify-center items-center h-full w-full open-nav">
      <div class="flex justify-end w-11/12 lg:w-[479px] 2xl:w-6/12">
        <div
          role="button"
          class="cursor-pointer w-6 h-6 rounded-full flex items-center justify-center bg-white"
        >
          <CloseIcon />
        </div>
      </div>
      <section
        class="w-11/12 lg:w-[479px] 2xl:w-6/12 bg-white flex justify-center items-center mt-5 rounded-lg"
      >
        <div class="w-11/12 py-8">
          <h2 class="capitalize text-xl text-gray-500 font-medium mb-4">
            <!-- {{isEdit ? 'Edit User' : 'add user'}} -->
          </h2>
          <form onsubmit="{handleSubmit}">
            <fieldset class="mb-4">
              <label class="block text-sm text-gray-500 capitalize mb-1">name</label>
              <input
                type="text"
                name="name"
                value="{value.name}"
                onchange="{handleChange}"
                required
                class="w-full h-10 border border-gray-500 rounded-sm px-4"
              />
            </fieldset>
            <fieldset class="mb-4">
              <label class="block text-sm text-gray-500 capitalize mb-1">location</label>
              <input
                type="text"
                name="location"
                value="{value.location}"
                onchange="{handleChange}"
                required
                class="w-full h-10 border border-gray-500 rounded-sm px-4"
              />
            </fieldset>
            <fieldset class="mb-4">
              <label class="block text-sm text-gray-500 capitalize mb-1">title</label>
              <input
                type="text"
                name="title"
                value="{value.title}"
                onchange="{handleChange}"
                required
                class="w-full h-10 border border-gray-500 rounded-sm px-4"
              />
            </fieldset>
            <button class="text-white capitalize px-6 py-2 bg-indigo-900 rounded-md w-full">save</button>
          </form>
        </div>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "Modal",
  props: {
    msg: String,
  },
});
</script>
