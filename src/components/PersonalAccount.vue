<template>
  <div class='wrap'>
    <nav class='navigate navigate--underlined nav-grounded'>
      <ul class='box filler'>
        <li class='panel'><a class='goto' href='#to-personal'>личная информация</a></li>
        <li class='panel'><a class='goto' href='#to-registration'>прописки</a></li>
        <li class='panel'><a class='goto' href='#to-statistics'>статистика</a></li>
        <li class='panel' v-show='is_gardener'><a class='goto' href='#to-tools'>инструменты</a></li>
        <li class='panel' v-show='is_soldier'><a class='goto' href='#to-weapons'>оружие</a></li>
        <li class='panel' v-show='is_leader'><a class='goto' href='#to-editor'>редактор</a></li>
        <li class='panel'><a class='goto' @click='logout'>выход</a></li>
      </ul>
      <div class='box filler box-filler-coloured'>
        <h3 class='header--shrinked'>Добро пожаловать, {{ user.name }}!</h3>
      </div>
    </nav>
    <section id='to-personal' class='section--shrinked'>
      <h1 class='header header--underlined header--shrinked'>личная информация</h1>
      <div class='container container--left-shifted'>
          <div class='box box--left-aligned box--filler-12'>
            <img class='img--inherited img--vertical-centre-aligned' alt='logo' src='@/assets/images/avatars/rabbit.jpg' v-if="( user.fkSexName == 'кролик' ) || ( user.fkSexName == 'заяц' )" />
            <img class='img--inherited img--vertical-centre-aligned' alt='logo' src='@/assets/images/avatars/queen.jpg' v-else-if="( user.fkRoleName == 'правитель' ) && ( user.fkSexName == 'женщина' )" />
            <img class='img--inherited img--vertical-centre-aligned' alt='logo' src='@/assets/images/avatars/king.jpg' v-else-if="user.fkRoleName == 'правитель'" />
            <img class='img--inherited img--vertical-centre-aligned' alt='logo' src='@/assets/images/avatars/soldier.jpg' v-else-if="user.fkRoleName == 'солдат'" />
            <img class='img--inherited img--vertical-centre-aligned' alt='logo' src='@/assets/images/avatars/gardener.jpg' v-else-if="user.fkRoleName == 'садовник'" />
            <img class='img--inherited img--vertical-centre-aligned' alt='logo' src='@/assets/images/avatars/resident.jpg' v-else />
          </div>
          <div class='box box--left-aligned box--filler-88 box--sticked-right'>
            <h2 class='header--underlined'><span class='field--bolded field--capitalized'>идентификационный номер</span>: <span>{{ user.id }}</span></h2>
            <ul>
              <li>
                <span class='field--bolded field--capitalized'>имя</span>: <span v-if='!is_name_edit_mode_enabled'>{{ user.name }}</span>
                <span v-if='!is_name_edit_mode_enabled'><button type='button' class='btn-action btn-add' @click='set_name_edit_mode( true )'>&#9998;</button></span>
                <span v-if='is_name_edit_mode_enabled'><input type='text' v-bind:placeholder='user.name' v-model='new_resident_name' required/></span>
                <span v-if='is_name_edit_mode_enabled' ><button type='button' class='btn-action btn-add' @click='update_resident_name_by_id( user.id, new_resident_name ); set_name_edit_mode( false )'>&#x2713;</button></span>
                <span v-if='is_name_edit_mode_enabled'><button type='button' class='btn-action btn-cross' @click='set_name_edit_mode( false )'>&#x2717;</button></span>
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
                <span>{{ user.fkSuitName == null? 'отсутствует' : user.fkSuitName }}</span>
              </li>
              <li>
                <span class='field--bolded field--capitalized'>роль</span>: <span>{{ user.fkRoleName == null? 'житель' : user.fkRoleName }}</span>
              </li>
            </ul>
          </div>
      </div>
    </section>
    <section id='to-registration' class='section--shrinked'>
      <h2 class='header header--underlined' >прописки</h2>
      <div class='container--left-shifted'>
        <table class='table--centred table--expanded'>
          <thead class='row--underlined'>
            <tr>
              <th>№</th>
              <th>дата выпуска</th>
              <th>дата окончания</th>
              <th>масть королевства</th>
              <th>+/x</th>
            </tr>
          </thead>

          <tbody class='row--underlined'>
            <tr v-for='( registration, idx ) in registrations' :key='registration.id'>
              <td>{{ idx + 1 }}</td>
              <td>{{ registration.issueDate }}</td>
              <td>{{ registration.expiryDate == null? 'бессрочно' : registration.expiryDate }}</td>
              <td>{{ kingdoms.filter( ( kingdom ) => ( kingdom.id == registration.fkKingdomId ) )[ 0 ].fkSuitName }}</td>
              <td><button type='button' class='btn-action btn-cross' @click='delete_resident_registration_by_id( registration.id )'>x</button></td>
            </tr>
          </tbody>

          <tfoot>
            <tr>
              <td>#</td>
              <td>{{ new Date( ).toISOString( ).slice( 0, 10 ) }}</td>
              <td>
                <input type='date' v-bind:min='new Date( ).toISOString( ).slice( 0, 10 )' v-model='spanDays' required />
              </td>
              <td>
                <select v-model='to_visit.destKingdom'>
                  <option v-for='kingdom in possible_kingdoms' v-bind:value='kingdom.id' :key='kingdom.id'>{{ kingdom.fkSuitName }}</option>
                </select>
              </td>
              <td><button type='button' class='btn-action btn-add' :class="{ 'btn-add-disabled' : is_visit_disabled }" @click='visit_to_kingdom' :disabled='is_visit_disabled'>+</button></td>
            </tr>
          </tfoot>

        </table>
    </div>
    </section>

    <section id='to-tools' class='section--shrinked' v-if='is_gardener'>
      <h2 class='header header--underlined'>инструменты</h2>
      <div class='container--left-shifted'>
        <table class='table--centred table--expanded'>
          <thead class='row--underlined'>
            <tr>
              <th>№</th>
              <th>название</th>
              <th>масть королевства</th>
              <th>+/x</th>
            </tr>
          </thead>

          <tbody class='row--underlined'>
            <tr v-for='( tool, idx ) in tools' :key='tool.id'>
              <td>{{ idx + 1 }}</td>
              <td>{{ tool.name }}</td>
              <td>{{ kingdoms.filter( ( kingdom ) => ( kingdom.id == tool.fkKingdomId ) )[ 0 ].fkSuitName }}</td>
              <td><button type='button' class='btn-action btn-cross' @click='delete_resident_tool_by_id( tool.id )'>x</button></td>
            </tr>
          </tbody>

          <tfoot>
            <tr>
              <td>#</td>
              <td>
                <input type='text' v-model='new_tool.name' />
              </td>
              <td>
                <select v-model='new_tool.suit'>
                  <option v-for='kingdom in kingdoms' v-bind:value='kingdom.id' :key='kingdom.id'>{{ kingdom.fkSuitName }}</option>
                </select>
              </td>
              <td><button type='button' class='btn-action btn-add' :class="{ 'btn-add-disabled' : is_tool_disabled }" @click='add_new_tool' :disabled='is_tool_disabled'>+</button></td>
            </tr>
          </tfoot>
        </table>
      </div>
    </section>

    <section id='to-weapons' class='section--shrinked' v-if='is_soldier' >
      <h2 class='header header--underlined'>оружие</h2>
      <div class='container--left-shifted'>
        <table class='table--centred table--expanded'>
          <thead class='row--underlined'>
            <tr>
              <th>№</th>
              <th>название</th>
              <th>масть королевства</th>
              <th>+/x</th>
            </tr>
          </thead>

          <tbody class='row--underlined'>
            <tr v-for='( weapon, idx ) in weapons' :key='weapon.id'>
              <td>{{ idx + 1 }}</td>
              <td>{{ weapon.name }}</td>
              <td>{{ kingdoms.filter( ( kingdom ) => ( kingdom.id == weapon.fkKingdomId ) )[ 0 ].fkSuitName }}</td>
              <td><button type='button' class='btn-action btn-cross' @click='delete_resident_weapon_by_id( weapon.id )'>x</button></td>
            </tr>
          </tbody>

          <tfoot>
            <tr>
              <td>#</td>
              <td>
                <input type='text' v-model='new_weapon.name' />
              </td>
              <td>
                <select v-model='new_weapon.suit'>
                  <option v-for='kingdom in kingdoms' v-bind:value='kingdom.id' :key='kingdom.id'>{{ kingdom.fkSuitName }}</option>
                </select>
              </td>
              <td><button type='button' class='btn-action btn-add' :class="{ 'btn-add-disabled' : is_weapon_disabled }" @click='add_new_weapon' :disabled='is_weapon_disabled'>+</button></td>
            </tr>
          </tfoot>
        </table>
      </div>
    </section>

    <section id='to-statistics' class='section--shrinked'>
      <h2 class='header header--underlined'>статистика</h2>
      <div class='container--centre-shifted'>
        <div class='chart--inlined' >
          <DoughnutChart v-bind="doughnutKingdomsProps" />
        </div>
        <div class='chart--inlined' >
          <DoughnutChart v-bind="doughnutFemalesProps" />
        </div>
        <div class='chart--inlined' >
          <DoughnutChart v-bind="doughnutMalesProps" />
        </div>
        <div class='chart--inlined' >
          <DoughnutChart v-bind="doughnutWeaponsProps" />
        </div>
        <div class='chart--inlined' >
          <DoughnutChart v-bind="doughnutToolsProps" />
        </div>
      </div>
    </section>

    <section id='to-editor' class='section--shrinked' v-if='is_leader'>
      <h2 class='header header--underlined'>редактор жителей</h2>
      <div class='container--left-shifted'>
        <table class='table--centred table--expanded'>
          <thead class='row--underlined'>
            <th>id</th>
            <th>имя</th>
            <th>пол</th>
            <th>масть</th>
            <th>роль</th>
            <th>+/x</th>
          </thead>

          <tbody class='row--underlined'>
            <tr v-for='resident in residents' :key='resident.id'>
              <td>{{ resident.id }}</td>
              <td>{{ resident.name }}</td>
              <td>{{ resident.fkSexName }}</td>
              <td>{{ resident.fkSuitName == null? 'отсутствует' : resident.fkSuitName }}</td>
              <td>{{ resident.fkRoleName == null? 'житель' : resident.fkRoleName }}</td>
              <td><button type='button' class='btn-action btn-cross'  @click='delete_resident_by_id( resident.id )'>x</button></td>
            </tr>
          </tbody>

          <tfoot>
            <tr>
              <td>#</td>
              <td><input type='text' v-model='new_resident.name' placeholder='Вася Пупкин' /></td>
              <td>
                <select v-model='new_resident.fkSexName'>
                  <option v-for='sex in sexes' v-bind:value='sex.name' :key='sex.name'>{{ sex.name }}</option>
                </select>
              </td>
              <td>
                <select v-model='new_resident.fkSuitName'>
                  <option value=''>отсутствует</option>
                  <option v-for='kingdom in kingdoms' v-bind:value='kingdom.fkSuitName' :key='kingdom.id'>{{ kingdom.fkSuitName }}</option>
                </select>
              </td>
              <td>
                <select v-model='new_resident.fkRoleName'>
                  <option v-for='( role, idx ) in roles' v-bind:value='role.name' :key='idx'>{{ role.name }}</option>
                </select>
              </td>
              <td><button type='button' class='btn-action btn-add' :class="{ 'btn-add-disabled' : is_resident_disabled }" :disabled='is_resident_disabled' @click='add_new_resident'>+</button></td>
            </tr>
          </tfoot>
        </table>
      </div>

      <h2 class='header header--underlined'>редактор прописок</h2>
      <div class='container--left-shifted'>
        <div>
          <select name='registrations_edit' v-model='to_move.residentId' @change='receive_registrations_on_edit( $event )'>
            <option value=''></option>
            <option v-for='resident in residents' :key='resident.id' v-bind:value='resident.id'>{{ resident.name }}:{{ resident.id }}</option>
          </select>
        </div>

        <table class='table--centred table--expanded'>
          <thead class='row--underlined'>
            <tr>
              <th>№</th>
              <th>дата выпуска</th>
              <th>дата окончания</th>
              <th>масть королевства</th>
              <th>+/x</th>
            </tr>
          </thead>

          <tbody class='row--underlined'>
            <tr v-for='( registration, idx ) in to_move.registrations' :key='registration.id'>
              <td>{{ idx + 1 }}</td>
              <td>{{ registration.issueDate }}</td>
              <td>{{ registration.expiryDate == null? 'бессрочно' : registration.expiryDate }}</td>
              <td>{{ kingdoms.filter( ( kingdom ) => ( kingdom.id == registration.fkKingdomId ) )[ 0 ].fkSuitName }}</td>
              <td><button type='button' class='btn-action btn-cross' @click='delete_resident_registration_by_id( registration.id )'>x</button></td>
            </tr>
          </tbody>

          <tfoot>
            <tr>
              <td>#</td>
              <td>{{ new Date( ).toISOString( ).slice( 0, 10 ) }}</td>
              <td>
                бессрочно
              </td>
              <td>
                <select v-model='to_move.destKingdom'>
                  <option v-for='kingdom in possible_kingdoms' v-bind:value='kingdom.id' :key='kingdom.id'>{{ kingdom.fkSuitName }}</option>
                </select>
              </td>
              <td><button type='button' class='btn-action btn-add' :class="{ 'btn-add-disabled' : is_move_disabled }" @click='move_to_kingdom' :disabled='is_move_disabled'>+</button></td>
            </tr>
          </tfoot>

        </table>
      </div>
    </section>
  </div>
</template>

<script lang='ts'>
  import { defineComponent } from 'vue';
  import { computed, toRef } from 'vue';
  import { DoughnutChart, useDoughnutChart } from "vue-chart-3";
  import { Chart, ChartData, ChartOptions, registerables } from "chart.js";

  Chart.register(...registerables);

  export default defineComponent({
    name: 'PersonalAccount',

    components: {
      DoughnutChart
    },

    setup( props ) {
      console.log( 'all setup starts' );
      const kingdoms = toRef( props, 'kingdoms' );
      const kingdomsValues = kingdoms.value.map( ( kingdom ) => ( kingdom.numberOfResidents ) );
      const kingdomsLabels = kingdoms.value.map( ( kingdom ) => ( kingdom.fkSuitName ) );
      console.log( kingdomsLabels );
      console.log( kingdomsValues );
      const females = toRef( props, 'females' );
      const femalesValues = females.value.map( ( female ) => ( female.females ) );
      const femalesLabels = females.value.map( ( female ) => ( female.kingdom ) );
      console.log( femalesLabels );
      console.log( femalesValues );
      const males = toRef( props, 'males' );
      const malesValues = males.value.map( ( male ) => ( male.males ) );
      const malesLabels = males.value.map( ( male ) => ( male.kingdom ) );
      console.log( malesLabels );
      console.log( malesValues );
      const weapons = toRef( props, 'weaponsStat' );
      const weaponsValues = weapons.value.map( ( weapon ) => ( weapon.weaponsNumber ) );
      const weaponsLabels = weapons.value.map( ( weapon ) => ( weapon.kingdom ) );
      console.log( weaponsLabels );
      console.log( weaponsValues );
      const tools = toRef( props, 'toolsStat' );
      const toolsValues = tools.value.map( ( tool ) => ( tool.toolsNumber ) );
      const toolsLabels = tools.value.map( ( tool ) => ( tool.kingdom ) );
      console.log( toolsLabels );
      console.log( toolsValues );

      console.log( 'setup kingdoms start' );
      const kingdomsData = computed<ChartData<"doughnut">>(() => ({
        labels: kingdomsLabels,
        datasets: [
          {
            data: kingdomsValues,
            backgroundColor: [
              "#84BF04",
              "#2C4002",
              "#5A7302",
              "#465902",
            ],
          },
        ],
      }));

      const kingdomsOptions = computed<ChartOptions<"doughnut">>(() => ({
        scales: {
          myScale: {
            type: "logarithmic",
            position: "left",
          },
        },
        plugins: {
          legend: {
            position: "bottom",
          },
          title: {
            display: true,
            text: "Число жителей в королевствах",
          },
        },
      }));

      const { doughnutChartProps: doughnutKingdomsProps, doughnutChartRef: doughnutKingdomsRef } = useDoughnutChart({
        chartData: kingdomsData,
        options: kingdomsOptions,
      });
      console.log( 'setup kingdoms end' );

      console.log( 'setup females start' );
      const femalesData = computed<ChartData<"doughnut">>(() => ({
        labels: femalesLabels,
        datasets: [
          {
            data: femalesValues,
            backgroundColor: [
              "#C72C37",
              "#E34B45",
              "#E9735F",
              "#FEB079",
            ],
          },
        ],
      }));

      const femalesOptions = computed<ChartOptions<"doughnut">>(() => ({
        scales: {
          myScale: {
            type: "logarithmic",
            position: "left",
          },
        },
        plugins: {
          legend: {
            position: "bottom",
          },
          title: {
            display: true,
            text: "Число женщин в королевствах",
          },
        },
      }));

      const { doughnutChartProps: doughnutFemalesProps, doughnutChartRef: doughnutFemalesRef } = useDoughnutChart({
        chartData: femalesData,
        options: femalesOptions,
      });
      console.log( 'setup females end' );


      console.log( 'setup males start' );
      const malesData = computed<ChartData<"doughnut">>(() => ({
        labels: malesLabels,
        datasets: [
          {
            data: malesValues,
            backgroundColor: [
              "#77CEFF",
              "#0079AF",
              "#123E6B",
              "#97B0C4",
            ],
          },
        ],
      }));

      const malesOptions = computed<ChartOptions<"doughnut">>(() => ({
        scales: {
          myScale: {
            type: "logarithmic",
            position: "left",
          },
        },
        plugins: {
          legend: {
            position: "bottom",
          },
          title: {
            display: true,
            text: "Число мужчин в королевствах",
          },
        },
      }));

      const { doughnutChartProps: doughnutMalesProps, doughnutChartRef: doughnutMalesRef } = useDoughnutChart({
        chartData: malesData,
        options: malesOptions,
      });
      console.log( 'setup males end' );


      console.log( 'setup weapons start' );
      const weaponsData = computed<ChartData<"doughnut">>(() => ({
        labels: weaponsLabels,
        datasets: [
          {
            data: weaponsValues,
            backgroundColor: [
              "#80585A",
              "#FF646B",
              "#FFB1B4",
              "#803236",
            ],
          },
        ],
      }));

      const weaponsOptions = computed<ChartOptions<"doughnut">>(() => ({
        scales: {
          myScale: {
            type: "logarithmic",
            position: "left",
          },
        },
        plugins: {
          legend: {
            position: "bottom",
          },
          title: {
            display: true,
            text: "Число единиц оружия в королевствах",
          },
        },
      }));

      const { doughnutChartProps: doughnutWeaponsProps, doughnutChartRef: doughnutWeaponsRef } = useDoughnutChart({
        chartData: weaponsData,
        options: weaponsOptions,
      });
      console.log( 'setup weapons end' );


      console.log( 'setup tools start' );
      const toolsData = computed<ChartData<"doughnut">>(() => ({
        labels: toolsLabels,
        datasets: [
          {
            data: toolsValues,
            backgroundColor: [
              "#F2CB05",
              "#594E14",
              "#8C7B26",
              "#F2B705",
            ],
          },
        ],
      }));

      const toolsOptions = computed<ChartOptions<"doughnut">>(() => ({
        scales: {
          myScale: {
            type: "logarithmic",
            position: "left",
          },
        },
        plugins: {
          legend: {
            position: "bottom",
          },
          title: {
            display: true,
            text: "Число единиц инструментов в королевствах",
          },
        },
      }));

      const { doughnutChartProps: doughnutToolsProps, doughnutChartRef: doughnutToolsRef } = useDoughnutChart({
        chartData: toolsData,
        options: toolsOptions,
      });
      console.log( 'setup tools end' );

      console.log( 'all setup ends' );

      return {
        kingdomsData,
        kingdomsOptions,
        doughnutKingdomsProps,
        doughnutKingdomsRef,

        femalesData,
        femalesOptions,
        doughnutFemalesProps,
        doughnutFemalesRef,

        malesData,
        malesOptions,
        doughnutMalesProps,
        doughnutMalesRef,

        weaponsData,
        weaponsOptions,
        doughnutWeaponsProps,
        doughnutWeaponsRef,

        toolsData,
        toolsOptions,
        doughnutToolsProps,
        doughnutToolsRef,     
      };
    },

    props: {
      base: {
        type: String,
      },

      kingdoms: {
        type: Object,
        default: null
      },

      females: {
        type: Object,
        default: null
      },

      males: {
        type: Object,
        default: null
      },

      weaponsStat: {
        type: Object,
        default: null
      },

      toolsStat: {
        type: Object,
        default: null
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
      },

      queryUpdateResidentName: {
        type: String,
        default: '/api/update-resident-name-by-id'
      },

      queryGetAllSexes: {
        type: String,
        default: '/api/get-all-sexes'
      },

      queryGetAllRoles: {
        type: String,
        default: '/api/get-all-roles'
      },

      queryGetResidents: {
        type: String,
        default: '/api/get-residents'
      },

      queryDeleteResident: {
        type: String,
        default: '/api/delete-resident-by-id'
      },

      queryDeleteResidentTool: {
        type: String,
        default: '/api/delete-resident-tool-by-id'
      },

      queryDeleteResidentWeapon: {
        type: String,
        default: '/api/delete-resident-weapon-by-id'
      },

      queryDeleteResidentRegistration: {
        type: String,
        default: '/api/delete-resident-registration-by-id'
      },

      queryVisitKingdom: {
        type: String,
        default: '/api/visit-to-kingdom'
      },

      queryMoveToKingdom: {
        type: String,
        default: '/api/add-new-registration'
      },

      queryAddNewTool: {
        type: String,
        default: '/api/add-new-tool'
      },

      queryAddNewWeapon: {
        type: String,
        default: '/api/add-new-weapon'
      },

      queryAddNewResident: {
        type: String,
        default: '/api/add-new-resident'
      },
    },

    data() {
      return {
        registrations: [],
        tools: [],
        weapons: [],
        is_name_edit_mode_enabled: false,
        new_resident_name: "",
        new_tool: { residentId: this.user.id, name: "", suit: null },
        new_weapon: { residentId: this.user.id, name: "", suit: null },
        sexes: [],
        residents: [],
        to_visit: {
          residentId: this.user.id,
          spanDays: 0,
          destKingdom: null
        },
        roles: [],
        new_resident: {
          name: "",
          fkSexName: null,
          fkSuitName: "",
          fkRoleName: null,
        },

        to_move: {
          registrations: [],
          residentId: '',
          destKingdom: null
        },
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

      get_registrations: async function( id ) {
        let regs_response = await fetch( `http://localhost:2154/alice${this.queryRegistrations}/${id}`, { method: 'GET' } );
        console.log( regs_response );
        if ( regs_response.status == 200 ) {
          let registrations = await regs_response.json();
          console.log( registrations );
          console.log( typeof registrations );
          return registrations;
        }
        return [];
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
      },

      logout: function() {
          this.$emit( 'confirmed', { confirm: true, user: null } );
      },

      set_name_edit_mode: function( value ) {
        this.is_name_edit_mode_enabled = value;
      },

      update_resident_name_by_id: async function( resident_id, new_resident_name ) {
        console.log( `trying to update resident name` );
        let resident_response = await fetch( `http://localhost:2154/alice${this.queryUpdateResidentName}`, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify( { id: resident_id, name: new_resident_name } ) } );
        console.log( resident_response );
        if ( resident_response.status == 200 ) {
          let renamed_resident = await resident_response.json();
          console.log( renamed_resident );
          console.log( typeof renamed_resident );
          this.$emit( 'updateresident', renamed_resident );
        } else console.log( 'unable to update name' );
      },

      receive_registrations_on_edit: async function( event ) {
        const id = event.target.value;
        if ( id == '' || id === null || id === undefined ) return;
        console.log( `getting registrations for ${id}` );
        this.to_move.registrations = await this.get_registrations( id );
      },

      visit_to_kingdom: async function( ) {
        console.log( 'trying to add a visit registration to another kingdom' );
        let visit_response = await fetch( `http://localhost:2154/alice${this.queryVisitKingdom}`,
          { 
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify( { residentId: this.to_visit.residentId, spanDays: this.to_visit.spanDays, destKingdom: this.to_visit.destKingdom } )
          } );
        console.log( visit_response );
        if ( visit_response.status == 200 ) {
          await this.receive_registrations( this.user.id );
          console.log( 'able to visit another kingdom' );
        } else console.log( 'unable to visit another kingdom' );
      },

      add_new_tool: async function( ) {
        console.log( 'trying to add a new tool' );
        let tool_response = await fetch( `http://localhost:2154/alice${this.queryAddNewTool}`,
          { 
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify( { residentId: this.new_tool.residentId, name: this.new_tool.name, suit: this.new_tool.suit } )
          } );
        console.log( tool_response );
        if ( tool_response.status == 200 ) {
          let tool = await tool_response.json();
          console.log( tool );
          console.log( typeof tool );
          await this.receive_tools( this.user.id );
          console.log( 'new tool successfully added' );
        } else console.log( 'unable to add new tool' );
      },

      add_new_weapon: async function( ) {
        console.log( 'trying to add a new weapon' );
        let weapon_response = await fetch( `http://localhost:2154/alice${this.queryAddNewWeapon}`,
          { 
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify( { residentId: this.new_weapon.residentId, name: this.new_weapon.name, suit: this.new_weapon.suit } )
          } );
        console.log( weapon_response );
        if ( weapon_response.status == 200 ) {
          let weapon = await weapon_response.json();
          console.log( weapon );
          console.log( typeof weapon );
          await this.receive_weapons( this.user.id );
          console.log( 'new weapon successfully added' );
        } else console.log( 'unable to add new weapon' );
      },

      add_new_resident: async function( ) {
        console.log( 'trying to add a new resident' );
        let resident_response = await fetch( `http://localhost:2154/alice${this.queryAddNewResident}`,
          { 
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify( 
              { name: this.new_resident.name,
                fkSexName: this.new_resident.fkSexName,
                fkSuitName: this.new_resident.fkSuitName == ''? null : this.new_resident.fkSuitName,
                fkRoleName: this.new_resident.fkRoleName } )
          } );
        console.log( resident_response );
        if ( resident_response.status == 200 ) {
          let resident = await resident_response.json();
          console.log( resident );
          console.log( typeof resident );
          await this.receive_residents( );
          console.log( 'new resident successfully added' );
        } else console.log( 'unable to add new resident' );
      },

      receive_sexes: async function( ) {
        console.log( `trying to receive sexes` );
        let sexes_response = await fetch( `http://localhost:2154/alice${this.queryGetAllSexes}`, { method: 'GET' } );
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
        let roles_response = await fetch( `http://localhost:2154/alice${this.queryGetAllRoles}`, { method: 'GET' } );
        console.log( roles_response );
        if ( roles_response.status == 200 ) {
          let roles = await roles_response.json();
          console.log( roles );
          console.log( typeof roles );
          this.roles = roles;
        }
      },

      receive_residents: async function( ) {
        console.log( `trying to receive residents` );
        let residents_response = await fetch( `http://localhost:2154/alice${this.queryGetResidents}`, { method: 'GET' } );
        console.log( residents_response );
        if ( residents_response.status == 200 ) {
          let residents = await residents_response.json();
          console.log( residents );
          console.log( typeof residents );
          this.residents = residents.filter( ( r ) => ( r.fkSuitName == this.user.fkSuitName || r.fkSuitName == null ) );
        }
      },

      delete_resident_by_id: async function( resident_id: number ) {
        console.log( `trying to delete resident` );
        let resident_response = await fetch( `http://localhost:2154/alice${this.queryDeleteResident}/${resident_id}`, { method: 'DELETE' } );
        console.log( resident_response );
        if ( resident_response.status == 200 ) {
          console.log( 'successfully deleted resident' );
          await this.receive_residents();
        }
      },

      delete_resident_tool_by_id: async function( resident_tool_id: number ) {
        console.log( `trying to delete resident's tool` );
        let resident_tool_response = await fetch( `http://localhost:2154/alice${this.queryDeleteResidentTool}/${resident_tool_id}`, { method: 'DELETE' } );
        console.log( resident_tool_response );
        if ( resident_tool_response.status == 200 ) {
          console.log( 'successfully deleted residents tool' );
          await this.receive_tools( this.user.id );
        }
      },

      delete_resident_weapon_by_id: async function( resident_weapon_id: number ) {
        console.log( `trying to delete resident's weapon` );
        let resident_weapon_response = await fetch( `http://localhost:2154/alice${this.queryDeleteResidentWeapon}/${resident_weapon_id}`, { method: 'DELETE' } );
        console.log( resident_weapon_response );
        if ( resident_weapon_response.status == 200 ) {
          console.log( 'successfully deleted residents weapon' );
          await this.receive_weapons( this.user.id );
        }
      },

      delete_resident_registration_by_id: async function( resident_registration_id: number ) {
        console.log( `trying to delete resident's registration` );
        let resident_registration_response = await fetch( `http://localhost:2154/alice${this.queryDeleteResidentRegistration}/${resident_registration_id}`, { method: 'DELETE' } );
        console.log( resident_registration_response );
        if ( resident_registration_response.status == 200 ) {
          console.log( 'successfully deleted residents registration' );
          await this.receive_registrations( this.user.id );
          if ( this.to_move.residentId != '' )
            this.to_move.registrations = await this.get_registrations( this.to_move.residentId );
        }
      },

      move_to_kingdom: async function( ) {
        console.log( 'trying to add an unlimited registration to another kingdom' );
        let move_response = await fetch( `http://localhost:2154/alice${this.queryMoveToKingdom}`,
          { 
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify( { residentId: this.to_move.residentId, destKingdom: this.to_move.destKingdom } )
          } );
        console.log( move_response );
        if ( move_response.status == 200 ) {
          await this.receive_registrations( this.user.id );
          console.log( 'able to move to another kingdom' );
          await this.receive_residents( );
          if ( this.to_move.residentId != '' )
            this.to_move.registrations = await this.get_registrations( this.to_move.residentId );
        } else console.log( 'unable to move to another kingdom' );
      },
    },

    computed: {
      is_soldier: function() { return this.user.fkRoleName == 'солдат' },
      is_gardener: function() { return this.user.fkRoleName == 'садовник' },
      is_leader: function() { return this.user.fkRoleName == 'правитель' },
      is_visit_disabled: function () { return this.to_visit.destKingdom == null; },
      is_move_disabled: function() { return this.to_move.destKingdom == null; },
      is_tool_disabled: function () { return this.new_tool.suit == null || this.new_tool.name.replaceAll( ' ', '' ) == ''; },
      is_weapon_disabled: function () { return this.new_weapon.suit == null || this.new_weapon.name.replaceAll( ' ', '' ) == ''; },
      is_resident_disabled: function () { return this.new_resident.name.replaceAll( ' ', '' ) == '' || this.new_resident.fkRoleName == null || this.new_resident.fkSexName == null; },

      possible_kingdoms: function() {
        let unexpired = this.registrations.filter( ( r ) => ( r.expiryDate == null ) ).map( ( r ) => ( r.fkKingdomId ) );
        console.log( 'filtered registrations' );
        console.log( unexpired );
        if ( unexpired.length == 0 )
          return this.kingdoms;
        else return this.kingdoms.filter( ( k ) => ( !unexpired.includes( k.id ) ) );
      },

      // TODO: add computed property for possible kingdoms for moved resident

      spanDays: {
        get() {
          let date = new Date();
          date.setDate( date.getDate() + this.to_visit.spanDays );
          return date.toISOString().slice( 0, 10 );
        },

        set( date ) {
          let today = new Date();
          console.log( `today is ${today}` );
          let diffTime = new Date( date ).getTime() - today.getTime();
          console.log( `different: ${diffTime}` );
          this.to_visit.spanDays = Math.trunc( diffTime / ( 21 * 3600 * 1000 ) );
        },
      },
    },

    async mounted() {
      // await this.receive_kingdoms();
      await this.receive_registrations( this.user.id );
      if ( this.is_leader ) {
        await this.receive_sexes();
        await this.receive_roles();
        await this.receive_residents();
        this.new_resident.fkSexName = this.sexes[ 0 ].name;
        this.new_resident.fkRoleName = this.roles[ 0 ].name;
      }
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

  .box--filler-12 {
    width: 12%;
  }

  .box--filler-88 {
    width: 88%;
  }

  .box--sticked-right {
    padding-right: 0;
  }

  .img--vertical-centre-aligned {
    padding-top: 2.8em;
    padding-bottom: 2.8em;
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

  .box-filler-coloured {
    background-color: rgba( 242, 207,  98, .75 );
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

  .nav-grounded {
    background: url( '~@/assets/images/bg.png' );
    background-size: 300px;
  }

  .filler {
    margin: 0;
    padding: 0;
    text-align: left;
  }

  .goto {
    display: block;
    text-decoration: none;
    font-weight: 800;
    padding: 0.8rem 1.28rem;
    background-color: rgba( 242, 207,  98, .75 );
    color: #6593A6;
  }

  .goto:hover {
    background-color: rgba( 242, 242, 242, .75 );
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

  .container--centre-shifted {
    text-align: center;
  }

  .table--centred {
    margin: 0 auto;
  }

  .table--expanded {
    border-spacing: 1.8rem;
    border-collapse: collapse;
  }

  .table--expanded td,
  .table--expanded th {
    padding: 1rem 4rem;
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

  .img--inherited {
    width: 100%;
  }

  .row--underlined {
    border-bottom: 1px solid #58595B;
  }

  .btn-action {
    color: #F2F2F2;
    border-radius: 50%;
    padding: .3em .6128em;
  }

  .btn-cross {
    border: 1px solid rgba(242, 191, 98, 1);
    background-color: rgba(242, 191, 98, 1); 
  }

  .btn-add {
    border: 1px solid rgba(100, 147, 165, 1);
    background-color: rgba(100, 147, 165, 1);
  }

  .btn-add-disabled {
    border: 1px solid rgba(100, 147, 165, .5);
    background-color: rgba(100, 147, 165, .5);
  }

  .wrap {
    margin: 0;
    background-color: rgb(242, 242, 242);
    width: 100%;
  }

  .chart--inlined {
    display: inline-block;
    width: 33%;
  }
</style>
