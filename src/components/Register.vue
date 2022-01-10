<template>
  <div class='box box--bordered box-rounded white-box box--padded-top'>
    <h3 class='form-header'>Анкета<br>нового персонажа</h3>
    <form @submit.prevent='register'>
      <fieldset class='fieldset--input'>
        <legend>имя</legend>
        <input type='text' v-model='username' placeholder='Вася Пупкин' /> 
      </fieldset>
      <fieldset class='fieldset--input'>
        <legend>пол</legend>
        <select v-model='fkSexName'>
          <option v-for='sex in sexes' v-bind:value='sex.name' :key='sex.name'>{{ sex.name }}</option>
        </select>
      </fieldset>
      <fieldset class='fieldset--input'>
        <legend>масть</legend>
        <select v-model='fkSuitName'>
          <option value=''>отсутствует</option>
          <option v-for='kingdom in kingdoms' v-bind:value='kingdom.fkSuitName' :key='kingdom.id'>{{ kingdom.fkSuitName }}</option>
        </select>
      </fieldset>
      <fieldset  class='fieldset--input'>
        <legend>роль</legend>
        <select v-model='fkRoleName'>
          <option v-for='( role, idx ) in roles' v-bind:value='role.name' :key='idx'>{{ role.name }}</option>
        </select>
      </fieldset>
      <fieldset class='fieldset--borderless'>
        <input class='form-activate' type='submit' value='зарегистрироваться' />
        <input class='form-activate' type='reset' @click='reset' value='сбросить' />
      </fieldset>
      <fieldset v-if='user != null'>
        <strong>Ваш ИД: {{ user.id }}</strong><br>
        Запомните его!<br>
        Он понадобиться вам при входе!
      </fieldset>
    </form>
    <a class='form-switcher' @click='toggleEntry'>войти</a>
  </div>
</template>

<script lang='ts'>
  import { defineComponent } from 'vue';

  export default defineComponent({
    name: 'Register',

    props: {

      base: {
        type: String,
      },

      queryKingdoms: {
        type: String,
        default: '/api/get-kingdoms'
      },

      queryGetAllSexes: {
        type: String,
        default: '/api/get-all-sexes'
      },

      queryGetAllRoles: {
        type: String,
        default: '/api/get-all-roles'
      },

      queryRegister: {
        type: String,
        default: '/api/add-new-resident',
      },
    },


    data() {
      return {
        is_confirmed: false,
        username: '',
        user: null,
        fkSuitName: '',
        fkSexName: '',
        fkRoleName: '',
        sexes: [],
        roles: [],
        kingdoms: []
      }; 
    },

    methods: {
      register: async function( submit_event: Event ) {
        submit_event.preventDefault();
        console.log( `register` );
        let register_response = await fetch( `${this.base}${this.queryRegister}`,
          { 
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify( 
              { name: this.username,
                fkSexName: this.fkSexName,
                fkSuitName: this.fkSuitName == ''? null : this.fkSuitName,
                fkRoleName: this.fkRoleName } )
          } );
        console.log( register_response );
        if ( register_response.status == 200 ) {
          let user = await register_response.json();
          console.log( user );
          console.log( typeof user );
          this.username = user.name;
          this.user = user;
          this.is_confirmed = true;
        }
      },

      toggleEntry: function( ) {
        this.$emit( 'toggleEntry', true );
      },

      reset: function( ) {
        this.is_confirmed = false;
        this.user = null;
      },

      confirm: function( ) {
        this.$emit( 'confirmed', { confirm: false, user: this.user } );
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

      receive_sexes: async function( ) {
        console.log( `trying to receive sexes` );
        let sexes_response = await fetch( `${this.base}${this.queryGetAllSexes}`, { method: 'GET' } );
        console.log( sexes_response );
        if ( sexes_response.status == 200 ) {
          let sexes = await sexes_response.json();
          console.log( sexes );
          console.log( typeof sexes );
          this.sexes = sexes;
        }
      },

      receive_roles: async function( ) {
        console.log( `trying to receive roles` );
        let roles_response = await fetch( `${this.base}${this.queryGetAllRoles}`, { method: 'GET' } );
        console.log( roles_response );
        if ( roles_response.status == 200 ) {
          let roles = await roles_response.json();
          console.log( roles );
          console.log( typeof roles );
          this.roles = roles;
        }
      },
    },

    async mounted() {
      await this.receive_roles();
      this.fkRoleName = this.roles[ 0 ].name;
      await this.receive_sexes();
      this.fkSexName = this.sexes[ 0 ].name;
      await this.receive_kingdoms();
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
