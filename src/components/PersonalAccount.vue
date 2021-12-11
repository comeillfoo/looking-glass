<template>
    <nav class='navigate navigate--underlined'>
      <ul class='box filler'>
        <li class='panel'><a class='goto' href='#to-personal'>личная информация</a></li>
        <li class='panel'><a class='goto' href='#to-registration'>прописки</a></li>
        <li class='panel'><a class='goto' href='#to-statistics'>статистика</a></li>
        <li class='panel' v-show='is_gardener'><a class='goto' href='#to-tools'>инструменты</a></li>
        <li class='panel' v-show='is_soldier'><a class='goto' href='#to-weapons'>оружие</a></li>
        <li class='panel' v-show='is_leader'><a class='goto' href='#to-editor'>редактор</a></li>
      </ul>
      <div class='box filler'>
        <h3 class='header--shrinked'>Добро пожаловать, {{ user.name }}!</h3>
      </div>
    </nav>
    <section id='to-personal' class='section--shrinked'>
      <h1 class='header header--underlined header--shrinked'>личная информация</h1>
      <div class='container container--left-shifted'>
          <div class='box box--left-aligned box--filler-20'>
            <img alt='logo' src='@/assets/images/avatars/queen.jpg' />
          </div>
          <div class='box box--left-aligned box--filler-80 box--sticked-right'>
            <h2 class='header--underlined'><span class='field--bolded field--capitalized'>идентификационный номер</span>: <span>{{ user.id }}</span></h2>
            <ul>
              <li>
                <span class='field--bolded field--capitalized'>имя</span>: <span>{{ user.name }}</span>
              </li>
              <li>
                <span class='field--bolded field--capitalized'>пол</span>: 
                <!-- <span class='colorized' v-if="user.fkSexName == 'мужчина'">&#9818;</span>
                <span class='colorized' v-else-if="user.fkSexName == 'женщина'">&#9819;</span>
                <span class='colorized' v-else >&#9822;</span> -->
                <span>{{ user.fkSexName }}</span>
              </li>
              <li>
                <span class='field--bolded field--capitalized'>масть</span>: 
                <!-- <span class='colorized' v-if="user.fkSuitName == 'черви'">&#9829;</span>
                <span class='colorized' v-if="user.fkSuitName == 'трефы'">&#9827;</span>
                <span class='colorized' v-if="user.fkSuitName == 'пики'" >&#9824;</span>
                <span class='colorized' v-if="user.fkSuitName == 'бубны'">&#9830;</span> -->
                <span>{{ user.fkSuitName }}</span>
              </li>
              <li>
                <span class='field--bolded field--capitalized'>роль</span>: <span>{{ user.fkRoleName }}</span>
              </li>
            </ul>
          </div>
      </div>
    </section>
    <section id='to-registration' class='section--shrinked'>
      <h2 class='header header--underlined' >прописки</h2>
      <div class='container--left-shifted'>
        <ul>
          <li v-for='registration in registrations' :key='registration.id'>
            {{ registration.id }}
            {{ registration.issueDate }}
            {{ registration.expiryDate }}
            {{ registration.fkKingdomId }}
          </li>
        </ul>
    </div>
    </section>

    <section id='to-tools' class='section--shrinked' v-if='is_gardener'>
      <h2 class='header header--underlined'>инструменты</h2>
      <div class='container--left-shifted'>
        <ul>
          <li v-for='tool in tools' :key='tool.id'>
            {{ tool.name }}
          </li>
        </ul>
      </div>
    </section>

    <section id='to-weapons' class='section--shrinked' v-if='is_soldier' >
      <h2 class='header header--underlined'>оружие</h2>
      <div class='container--left-shifted'>
        <ul>
          <li v-for='weapon in weapons' :key='weapon.id'>
            {{ weapon.name }}
          </li>
        </ul>
      </div>
    </section>

    <section id='to-statistics' class='section--shrinked'>
      <h2 class='header header--underlined'>статистика</h2>
      <div class='container--left-shifted'>
      </div>
    </section>

    <section id='to-editor' class='section--shrinked' v-if='is_leader'>
      <h2 class='header header--underlined'>редактор</h2>
      <div class='container--left-shifted'>
      </div>
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

  .box--filler-20 {
    width: 20%;
  }

  .box--filler-80 {
    width: 80%;
  }

  .box--sticked-right {
    padding-right: 0;
  }

  .panel {
    display: inline-block;
  }

  .header {
    text-align: left;
    text-transform: capitalize;
    padding-left: 0.95rem;
    color: #6593A6;
  }

  .header--underlined {
    border-bottom: 1px solid #58595B;
    line-height: 2.5rem;
  }

  .header--shrinked {
    margin: 0;
    padding: 0.675rem 1.28rem;
  }

  .navigate {
    margin: 0;
    padding: 0;
    text-align: left;
    display: flex;
    justify-content: space-between;
  }

  .navigate--underlined {
    border-bottom: 1px solid #58595B;
  }

  .filler {
    margin: 0;
    padding: 0;
    text-align: left;
  }

  .goto {
    display: block;
    text-decoration: none;
    padding: 0.8rem 1.28rem;
    color: #6593A6;
  }

  .goto:hover {
    background-color: #F2F2F2;
  }

  .section--shrinked {
    padding: 0rem 2.56rem;
  }

  .container {
    display: flex;
    justify-content: space-between;
  }

  .container--left-shifted {
    text-align: left;
  }

  .field--capitalized {
    text-transform: capitalize;
  }

  .field--bolded {
    font-weight: 600;
  }

  .field--right-aligned {
    text-align: right;
  }
</style>
