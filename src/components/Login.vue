<template>
  <div class='box box--bordered box-rounded white-box box--padded-top'>
    <h3 class='form-header'>Вход</h3>
    <form @submit.prevent='login'>
      <fieldset class='fieldset--input'>
        <legend>ИД</legend>
        <input type='number' placeholder='4020' v-model='id' required />
      </fieldset>
      <fieldset class='fieldset--borderless'>
        <input class='form-activate' type='submit' value='войти' />
        <input class='form-activate' type='reset' value='сбросить' @click='reset' />
      </fieldset>
      <fieldset v-if='is_confirmed'>
        <label><h4>{{ username }}. Это Вы?</h4></label>
        <button class='form-activate' type='button' @click='confirm'>да</button>
        <button class='form-activate' type='button' @click='reset'>нет</button>
      </fieldset>
    </form>
    <a class='form-switcher' @click='toggleEntry'>зарегистрироваться</a>
  </div>
</template>

<script lang='ts'>
  import { defineComponent } from 'vue';

  export default defineComponent({
    name: 'Login',

    props: {

      base: {
        type: String,
      },

      queryLogin: {
        type: String,
        default: '/api/get-resident-by-id',
      },
    },


    data() {
      return { id: null, is_confirmed: false, username: 'John Johnson', user: null }; 
    },

    methods: {
      login: async function( submit_event: Event ) {
        submit_event.preventDefault();
        console.log( `login:${this.id}` );
        let login_response = await fetch( `http://localhost:2154/alice${this.queryLogin}/${this.id}`, { method: 'GET' } );
        console.log( login_response );
        if ( login_response.status == 200 ) {
          let user = await login_response.json();
          console.log( user );
          console.log( typeof user );
          this.username = user.name;
          this.user = user;
          this.is_confirmed = true;
        }
      },

      toggleEntry: function( ) {
        this.$emit( 'toggleEntry', false );
      },

      reset: function( ) {
        this.is_confirmed = false;
      },

      confirm: function( ) {
        this.$emit( 'confirmed', { confirm: false, user: this.user } );
      }
    }
  });
</script>

<!-- Add 'scoped' attribute to limit CSS to this component only -->
<style scoped>
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    display: inline-block;
    margin: 0 10px;
  }
</style>
