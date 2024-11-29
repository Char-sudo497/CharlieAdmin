<template>
  <v-app dark>
    <v-app-bar v-if="showDrawer" app style="background-color: #333; color: white;">
      <v-app-bar-nav-icon @click="drawer = !drawer" style="color: white;"></v-app-bar-nav-icon>
      <v-toolbar-title>Admin Panel</v-toolbar-title>
    </v-app-bar>

    <v-navigation-drawer v-if="showDrawer" v-model="drawer" :mini-variant="miniVariant" :clipped="clipped" fixed app
      style="background-color: #333;">
      <v-layout column fill-height>
        <v-list>
          <v-list-item v-for="(item, i) in items" :key="i" :to="item.to" router exact style="color: white;">
            <v-list-item-action>
              <v-icon style="color: white;">{{ item.icon }}</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>

        <!-- Spacer to push logout button to the bottom -->
        <v-spacer></v-spacer>
      </v-layout>

      <!-- Logout Button at Bottom -->
      <v-divider></v-divider>
      <v-list-item class="logout-button">
        <v-btn block color="red" dark @click="logout">
          <v-icon left>mdi-logout</v-icon>
          Logout
        </v-btn>
      </v-list-item>
    </v-navigation-drawer>

    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { auth } from '~/plugins/firebase';

export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: false,
      drawer: false,
      miniVariant: false,
      showDrawer: true,
      items: [
        { icon: 'mdi-view-dashboard', title: 'Dashboard', to: '/admin/admindashboard' },
        { icon: 'mdi-account', title: 'Account Management', to: '/admin/accountmanagement' },
        { icon: 'mdi-cube', title: 'Product Management', to: '/admin/itemmanagement' },
        { icon: 'mdi-order-bool-ascending', title: 'Order Management', to: '/admin/ordermanagement' },
        { icon: 'mdi-currency-php', title: 'Price Management', to: '/admin/pricemanagement' },
        { icon: 'mdi-folder', title: 'Category Management', to: '/admin/categorymanagement' },
        { icon: 'mdi-warehouse', title: 'Inventory Management', to: '/admin/inventorymanagement' },
        { icon: 'mdi-credit-card', title: 'Payment Methods', to: '/admin/payments' },
        { icon: 'mdi-star', title: 'Ratings', to: '/admin/ratings' },
        { icon: 'mdi-qrcode', title: 'Qr Code', to: '/admin/qrscanner' }
      ],
      showDrawerOnRoutes: [
        '/admin/admindashboard',
        '/admin/accountmanagement',
        '/admin/itemmanagement',
        '/admin/pricemanagement',
        '/admin/categorymanagement',
        '/admin/inventorymanagement',
        '/admin/ordermanagement',
        '/admin/payments',   
        '/admin/ratings',    
        '/admin/qrscanner'
      ]
    };
  },
  watch: {
    $route(to) {
      this.checkDrawerVisibility(to.path);
    }
  },
  mounted() {
    this.checkDrawerVisibility(this.$route.path);
  },
  methods: {
    checkDrawerVisibility(path) {
      this.showDrawer = this.showDrawerOnRoutes.includes(path);
    },
    async logout() {
      try {
        await auth.signOut();
        console.log("User has successfully logged out."); // Log confirmation
        this.$router.replace('/');  // Redirect and prevent back navigation
      } catch (error) {
        console.error("Error logging out:", error);
      }
    }
  }
}
</script>

<style scoped>
.logout-button {
  position: absolute;
  bottom: 0;
  width: 100%;
}
</style>
