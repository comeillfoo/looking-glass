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
    <section id='statistics'>
      <h2>статистика</h2>
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

      queryLogin: {
        type: String,
        default: '/api/get-residents-registrations',
      },
    },

    data() {
      return {
        registrations: [],
      };
    },

    methods: {
      receive_registrations: async function( id ) {
        let regs_response = await fetch( `http://localhost:2154/alice${this.queryLogin}/${id}`, { method: 'GET' } );
        console.log( regs_response );
        if ( regs_response.status == 200 ) {
          let registrations = await regs_response.json();
          console.log( registrations );
          console.log( typeof registrations );
          this.registrations = registrations;
        }
      }
    },
    async mounted() {
      await this.receive_registrations( this.user.id ); 
    },

  });
</script>

<!-- Add 'scoped' attribute to limit CSS to this component only -->
<style scoped>

  .box--left-aligned {
    text-align: left;
  }
</style>
