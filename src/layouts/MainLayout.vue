<template>
  <q-layout view="hHh lpR lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          <div class="absolute-center">
            <q-icon name="savings" />
            Moneyballs
          </div>
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
      class="bg-primary"
      :width="250"
      :breakpoint='767'
    >
      <q-list>
        <q-item-label header class="text-white"> Navigation </q-item-label>

        <NavLink v-for="link in navLinks" :key="link.title" v-bind="link" />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
// import { ref } from "vue";
import NavLink from "src/components/Nav/NavLink.vue";

defineOptions({
  name: "MainLayout",
});

const navLinks = [
  {
    title: "Entries",
    icon: "savings",
    link: "/",
  }
];

// const leftDrawerOpen = ref(false);

// function toggleLeftDrawer() {
//   leftDrawerOpen.value = !leftDrawerOpen.value;
// }
</script>
