<template>
  <div class="container" style="padding: 50px 0 100px 0">
    <Profile v-if="store.user" />
    <Auth v-else />
  </div>
</template>

<script>
import { onMounted } from 'vue';
import { store } from './store';
import { supabase } from './supabase';
import Auth from './components/Auth.vue';
import Profile from './components/Profile.vue';
export default {
  components: {
    Auth,
    Profile
  },

  setup() {
    supabase.auth.getUser().then(({ data }) => {
      store.user = data.user;
    });
    supabase.auth.onAuthStateChange((_, session) => {
      console.log('session', session);
      store.user = session?.user;
    });
    async function invokeEdgeFun() {
      try {
        const { data, error } = await supabase.rpc('hello_world');
        console.log('invokeEdgeFun data:', data);
      } catch (error) {
        alert(error.message);
      } finally {
        loading.value = false;
      }
    }
    //https://supabase.com/docs/guides/realtime/subscribing-to-database-changes
    const channel = supabase
      .channel('room1')
      .on(
        'postgres_changes',
        {
          event: 'UPDATE',
          schema: 'public',
          table: 'profiles'
        },
        (payload) => console.log(payload)
      )
      .subscribe();
    onMounted(() => {
      invokeEdgeFun();
    });
    return {
      store,
      invokeEdgeFun
    };
  }
};
</script>
