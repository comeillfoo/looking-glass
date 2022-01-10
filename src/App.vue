<template>
  <Register @toggleEntry='toggleEntry' :base='base' v-if='is_not_logged && !is_login' />
  <Login @confirmed='confirmed' @toggleEntry='toggleEntry' :base='base' v-if='is_not_logged && is_login' />
  <PersonalAccount @confirmed='confirmed' @updateresident='updateresident' :base='base' :kingdoms='kingdoms' :user='user' v-if='!is_not_logged' />
</template>

<script lang="ts">
  import { defineComponent } from 'vue';
  import Login from '@/components/Login.vue';
  import Register from '@/components/Register.vue';
  import PersonalAccount from '@/components/PersonalAccount.vue';

  export default defineComponent({
    name: 'App',
    components: {
      Login, Register, PersonalAccount
    },

    props: {
      base: {
        type: String,
        default: 'http://localhost:2154/alice'
      },

      queryKingdoms: {
        type: String,
        default: '/api/get-kingdoms'
      },
    },

    data() {
      return {
        is_not_logged: true,
        user: null,
        is_login: true,
        kingdoms: [],
      };
    },

    methods: {

      confirmed( params ) {
        this.is_not_logged = params[ 'confirm' ];
        this.user = params[ 'user' ];
      },

      toggleEntry( is_login ) {
        this.is_login = is_login;
      },

      updateresident( new_resident ) {
        this.user = new_resident;
      },

      receive_kingdoms: async function( ) {
        console.log( `trying to receive kingdoms` );
        let kingdoms_response = await fetch( `${this.base}${this.queryKingdoms}`, { method: 'GET' } );
        console.log( kingdoms_response );
        if ( kingdoms_response.status == 200 ) {
          let kingdoms = await kingdoms_response.json();
          console.log( kingdoms );
          console.log( typeof kingdoms );
          this.kingdoms = kingdoms;
        } 
      },
    },

    async mounted() {
      await this.receive_kingdoms();
    }
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
    background-image: linear-gradient( rgba(87, 89, 91, 0.4 ), rgba(87, 89, 91, 0.4 ) ), url( '~@/assets/images/bg.png' );
    background-size: 1200px;
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

  .white-box {
    background-color: rgba( 242, 242, 242, 1 );
  }

  .box--padded-top {
    margin-top: 16em;
  }

  .box--bordered {
    border: solid 2px #58595B;
  }

  .box-rounded {
    border-radius: .4rem;
  }

  .fieldset--input {
    text-align: left;
  }

  legend {
    font-weight: 600;
    text-transform: capitalize;
  }

  a.form-switcher {
    line-height: 2rem;
    color: rgba(100, 147, 165, 1);
    text-decoration: none;
  }

  a.form-switcher:hover {
    text-decoration: underline;
    cursor: pointer;
  }

  h3.form-header {
    margin-top: 0;
    padding-top: .8em;
    padding-bottom: .8em;
  }

  .form-activate {
    border: 1px solid rgba(100, 147, 165, 1);
    border-radius: .5em;
    background-color: transparent;
    color: rgba(100, 147, 165, 1);
    font-weight: 600;
    margin-right: .8em;
    padding: .6em .5em;
    text-transform: capitalize;
  }

  .form-activate:hover {
    background-color: rgba(100, 147, 165, .33);
  }

  .form-activate:active {
    background-color: rgba(100, 147, 165, .66);
  }

  .fieldset--borderless {
    border-width: 0;
  }

</style>
