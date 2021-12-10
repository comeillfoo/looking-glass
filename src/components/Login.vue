<template>
  <div class='box box-rounded'>
    <h3>Вход</h3>
    <form @submit.prevent='login'>
      <fieldset>
        <label>ИД</label><input type='number' placeholder='4020' v-model='id' required />
      </fieldset>
      <fieldset>
        <input type='submit' value='войти' />
        <input type='reset' value='сбросить' />
      </fieldset>
      <fieldset v-if='confirm'>
        <label>{{ username }}. Это Вы?</label>
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
        type: String
      },

      queryLogin: {
        type: String,
        default: '/api/get-resident-by-id',
      },
    },

    data() {
      return { id: null, confirm: false, username: 'John Johnson' }; 
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
          this.username = user.name;
          this.confirm = true;
        }
      },
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
