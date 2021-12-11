<template>
  <div class='box box--bordered box-rounded'>
    <h3>Вход</h3>
    <form @submit.prevent='login'>
      <fieldset>
        <label>ИД</label><input type='number' placeholder='4020' v-model='id' required />
      </fieldset>
      <fieldset>
        <input type='submit' value='войти' />
        <input type='reset' value='сбросить' @click='reset' />
      </fieldset>
      <fieldset v-if='is_confirmed'>
        <label><h4>{{ username }}. Это Вы?</h4></label>
        <button type='button' @click='confirm'>да</button>
        <button type='button' @click='reset'>нет</button>
      </fieldset>
    </form>
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
  h3 {
    margin: 40px 0 0;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    display: inline-block;
    margin: 0 10px;
  }
  a {
    color: #42b983;
  }
</style>
