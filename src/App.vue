<template>
  <Login @confirmed='confirmed' :base='base' v-if='is_not_logged' />
  <PersonalAccount :base='base' :user='user' v-else />
</template>

<script lang="ts">
  import { defineComponent } from 'vue';
  import Login from '@/components/Login.vue';
  import PersonalAccount from '@/components/PersonalAccount.vue';

  export default defineComponent({
    name: 'App',
    components: {
      Login, PersonalAccount
    },

    props: {
      base: {
        type: String,
        default: 'http://localhost:2154/alice'
      }
    },

    data() {
      return {
        is_not_logged: true,
        user: null,
      };
    },

    methods: {

      confirmed( params ) {
        this.is_not_logged = params[ 'confirm' ];
        this.user = params[ 'user' ];
      },

    },
  });
</script>

<style>
  *, *:after, *:before {
    box-sizing: border-box;
  }

  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  .box {
    display: inline-block;
    padding: .8rem;
    border: solid 2px dimgray;
  }

  .box-rounded {
    border-radius: .4rem;
  }

</style>
