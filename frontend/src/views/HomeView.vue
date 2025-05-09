<script setup lang="ts">
import IconExam from "@/components/icons/IconExam.vue";
import IconLogout from "@/components/icons/IconLogout.vue";
import IconPeople from "@/components/icons/IconPeople.vue";
import IconTickChecklist from "@/components/icons/IconTickChecklist.vue";
import { PUBLIC_API } from "@/services/main";
import { useProfileStore } from "@/stores/profile";
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";

const profile = useProfileStore();
const router = useRouter();

const loading = ref(true);

async function fetchProfile() {
  await profile.retrieveData();
  if (!profile.name) {
    router.replace({ path: "/login" });
    return;
  }
}

onMounted(async () => {
  // Fetch to see how our profile is going and put in our store.
  await fetchProfile();
  loading.value = false;
});

async function logout() {
  await fetch(`${PUBLIC_API}/logout`, {
    method: "GET",
    mode: "cors",
    credentials: "include",
  });
  profile.name = undefined;
  profile.role = undefined;
  router.replace({ path: "/login" });
}
</script>

<template>
  <div class="w-full animate-pulse" v-if="loading">Loading...</div>

  <div class="flex w-full flex-col" v-else>
    <span class="w-full justify-self-end text-right font-semibold"
      >Welcome back,
      <span class="text-leaf hover:text-moss underline">{{ profile.name }}</span></span
    >

    <div class="mt-32 flex w-full flex-col gap-6 font-semibold text-white">
      <div class="flex w-full flex-col gap-2">
        <RouterLink
          to="/customers"
          class="bg-moss flex w-full items-center justify-center gap-2 rounded-lg from-black/25 to-black/25 px-4 py-2 text-white hover:bg-gradient-to-r"
          v-if="profile.role == 'acceptance'"
        >
          <IconPeople class="size-5 fill-white" />
          <span class="font-semibold">Customers Registration</span>
        </RouterLink>

        <RouterLink
          to="/exams"
          class="bg-moss flex w-full items-center justify-center gap-2 rounded-lg from-black/25 to-black/25 px-4 py-2 text-white hover:bg-gradient-to-r"
          v-if="profile.role == 'acceptance' || profile.role == 'accounting'"
        >
          <IconExam class="size-5 fill-white" />
          <span class="font-semibold">Exams Registration</span>
        </RouterLink>

        <RouterLink
          to="/enrollments"
          class="bg-moss flex w-full items-center justify-center gap-2 rounded-lg from-black/25 to-black/25 px-4 py-2 text-white hover:bg-gradient-to-r"
        >
          <IconTickChecklist class="size-5 fill-white" />
          <span class="font-semibold">Takers Registration</span>
        </RouterLink>
        <RouterLink
          to="/certificates"
          class="bg-moss flex w-full items-center justify-center gap-2 rounded-lg from-black/25 to-black/25 px-4 py-2 text-white hover:bg-gradient-to-r"
        >
          <IconTickChecklist class="size-5 fill-white" />
          <span class="font-semibold">Certificate Lookup</span>
        </RouterLink>
      </div>

      <button
        class="bg-red flex w-full cursor-pointer items-center justify-center gap-2 rounded-lg from-black/25 to-black/25 px-4 py-2 hover:bg-gradient-to-r"
        @click="logout"
      >
        <IconLogout class="size-5 fill-white" />
        Logout
      </button>
    </div>
  </div>
</template>