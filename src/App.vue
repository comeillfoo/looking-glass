<template>
  <Login @confirmed='confirmed' :base='base' v-if='is_not_logged' />
  <PersonalAccount @confirmed='confirmed' :base='base' :user='user' v-else />
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

  /* color scheme */
  /* Color Theme Swatches in Hex */
  .yellow-grey-1-hex { color: #6593A6; }
  .yellow-grey-2-hex { color: #58595B; }
  .yellow-grey-3-hex { color: #F2CF63; }
  .yellow-grey-4-hex { color: #F2C063; }
  .yellow-grey-5-hex { color: #F2F2F2; }

  /* Color Theme Swatches in RGBA */
  .yellow-grey-1-rgba { color: rgba(100, 147, 165, 1); }
  .yellow-grey-2-rgba { color: rgba(87, 89, 91, 1); }
  .yellow-grey-3-rgba { color: rgba(242, 207, 98, 1); }
  .yellow-grey-4-rgba { color: rgba(242, 191, 98, 1); }
  .yellow-grey-5-rgba { color: rgba(242, 242, 242, 1); }

  /* Color Theme Swatches in HSLA */
  .yellow-grey-1-hsla { color: hsla(197, 26, 52, 1); }
  .yellow-grey-2-hsla { color: hsla(219, 1, 35, 1); }
  .yellow-grey-3-hsla { color: hsla(45, 84, 66, 1); }
  .yellow-grey-4-hsla { color: hsla(39, 84, 66, 1); }
  .yellow-grey-5-hsla { color: hsla(0, 0, 94, 1); }
  /* color scheme */

  *, *:after, *:before {
    box-sizing: border-box;
  }

  body {
    margin: 0;
  }

  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #58595B;
  }

  .box {
    display: inline-block;
    padding: .8rem;
    vertical-align: top;
  }

  .box--bordered {
    border: solid 2px #58595B;
  }

  .box-rounded {
    border-radius: .4rem;
  }

</style>
