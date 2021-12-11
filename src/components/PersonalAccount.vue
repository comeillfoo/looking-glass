<template>
    <nav>
      <ul>
        <li><a href='#personal'>личная информация</a></li>
        <li><a href='#residences'>прописки</a></li>
        <li><a href='#statistics'>статистика</a></li>
      </ul>
    </nav>
    <section id='personal'>
      <h1>личная информация</h1>
      <div>
          <div class='box--left-aligned'>
            <img alt='logo' />
          </div>
          <div class='box--left-aligned'>
            <ul>
              <li>ИД: {{ user.id }}</li>
              <li>Имя: {{ user.name }}</li>
              <li>Пол: <img alt='sex'  /> {{ user.fkSexName }}</li>
              <li>Масть: <img alt='suit' /> {{ user.fkSuitName }}</li>
              <li>Должность: {{ user.fkRoleName }}</li>
            </ul>
          </div>
      </div>
    </section>
    <section id='show-registrations'>
      <h2>прописки</h2>
      <ul>
        <li v-for='registration in registrations' :key='registration.id'>
          {{ registration.id }}
          {{ registration.issueDate }}
          {{ registration.expiryDate }}
          {{ registration.fkKingdomId }}
        </li>
      </ul>
    </section>

    <section id='show-tools' v-if='is_gardener'>
      <h2>инструменты</h2>
      <ul>
        <li v-for='tool in tools' :key='tool.id'>
          {{ tool.name }}
        </li>
      </ul>
    </section>

    <section id='show-weapons' v-if='is_soldier' >
      <h2>оружие</h2>
      <ul>
        <li v-for='weapon in weapons' :key='weapon.id'>
          {{ weapon.name }}
        </li>
      </ul>
    </section>

    <section id='statistics'>
      <h2>статистика</h2>
    </section>

    <section id='edit' v-if='is_leader'>
      <h2>редактирвать</h2>

    </section>
</template>

<script lang='ts'>
  import { defineComponent } from 'vue';

  export default defineComponent({
    name: 'PersonalAccount',

    props: {
      base: {
        type: String,
      },

      user: {
        type: Object,
        default: null, 
      },

      queryRegistrations: {
        type: String,
        default: '/api/get-residents-registrations',
      },

      queryTools: {
        type: String,
        default: '/api/get-tools-by-resident-id',
      },

      queryWeapons: {
        type: String,
        default: '/api/get-weapons-by-resident-id'
      }
    },

    data() {
      return {
        registrations: [],
        tools: [],
        weapons: [],
      };
    },

    methods: {
      receive_registrations: async function( id ) {
        let regs_response = await fetch( `http://localhost:2154/alice${this.queryRegistrations}/${id}`, { method: 'GET' } );
        console.log( regs_response );
        if ( regs_response.status == 200 ) {
          let registrations = await regs_response.json();
          console.log( registrations );
          console.log( typeof registrations );
          this.registrations = registrations;
        }
      },

      receive_tools: async function( id ) {
        console.log( `trying to receive tools for ${id}` );
        let tools_response = await fetch( `http://localhost:2154/alice${this.queryTools}/${id}`, { method: 'GET' } );
        console.log( tools_response );
        if ( tools_response.status == 200 ) {
          let tools = await tools_response.json();
          console.log( tools );
          console.log( typeof tools );
          this.tools = tools;
        }
      },

      receive_weapons: async function( id ) {
        console.log( `trying to receive weapons for ${id}` );
        let weapons_response = await fetch( `http://localhost:2154/alice${this.queryWeapons}/${id}`, { method: 'GET' } );
        console.log( weapons_response );
        if ( weapons_response.status == 200 ) {
          let weapons = await weapons_response.json();
          console.log( weapons );
          console.log( typeof weapons );
          this.weapons = weapons;
        }
      }
    },

    computed: {
      is_soldier: function() { return this.user.fkRoleName == 'солдат' },
      is_gardener: function() { return this.user.fkRoleName == 'садовник' },
      is_leader: function() { return this.user.fkRoleName == 'правитель' },
    },

    async mounted() {
      await this.receive_registrations( this.user.id );
      if ( this.is_gardener )
        await this.receive_tools( this.user.id );
      if ( this.is_soldier )
        await this.receive_weapons( this.user.id ); 
    },

  });
</script>

<!-- Add 'scoped' attribute to limit CSS to this component only -->
<style scoped>

  .box--left-aligned {
    text-align: left;
  }
</style>
