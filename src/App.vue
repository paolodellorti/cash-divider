<template>
  <div class="page">
    <div class="main-cont">
      <h1>CASH DIVIDER</h1>
      <md-table>
        <md-table-row>
          <md-table-head md-numeric>ID</md-table-head>
          <md-table-head>Name</md-table-head>
          <md-table-head>Email</md-table-head>
          <md-table-head>Gender</md-table-head>
          <md-table-head>Job Title</md-table-head>
        </md-table-row>

        <md-table-row>
          <md-table-cell md-numeric>1</md-table-cell>
          <md-table-cell>Shawna Dubbin</md-table-cell>
          <md-table-cell>sdubbin0@geocities.com</md-table-cell>
          <md-table-cell>Male</md-table-cell>
          <md-table-cell>Assistant Media Planner</md-table-cell>
        </md-table-row>

        <md-table-row>
          <md-table-cell md-numeric>2</md-table-cell>
          <md-table-cell>Odette Demageard</md-table-cell>
          <md-table-cell>odemageard1@spotify.com</md-table-cell>
          <md-table-cell>Female</md-table-cell>
          <md-table-cell>Account Coordinator</md-table-cell>
        </md-table-row>

        <md-table-row>
          <md-table-cell md-numeric>3</md-table-cell>
          <md-table-cell>Vera Taleworth</md-table-cell>
          <md-table-cell>vtaleworth2@google.ca</md-table-cell>
          <md-table-cell>Male</md-table-cell>
          <md-table-cell>Community Outreach Specialist</md-table-cell>
        </md-table-row>
      </md-table>
      <md-list>
        <div
        >
          <md-list-item>
            <md-icon>family_restroom</md-icon>
            <span class="md-list-item-text">Trash</span>
            <md-button class="md-icon-button md-list-action">
              <md-icon class="md-primary">chat_bubble</md-icon>
            </md-button>
          </md-list-item>
          <md-divider class="md-inset"></md-divider>
        </div>
      </md-list>
      <table class="result-container">
        <tr>
          <td>taglia</td>
          <td
            v-for="(cash, index) in [50,20,10,5,2,1,0.5]"
            :key="cash"
            :class="{ first: index % 2 == 1}"
          >
            {{ cash }} €
          </td>
        </tr>
        <tr>
          <td>quantità</td>
          <td
            v-for="(cash, index) in [50,20,10,5,2,1,0.5]"
            :key="cash"
            :class="{ first: index % 2 != 1}"
          >
          {{ cashDivision[cash] }}
          </td>
        </tr>
      </table>
      <h2>Persone</h2>
      <InfoCards :people="people" />
      <div class="add-button"  @click="showPeopleDialog = true">
        + Aggiungi persone
      </div>
      <h2>Famiglie</h2>
      <InfoCards :families="families" />
      <div class="add-button"  @click="showFamiliesDialog = true">
        + Aggiungi famiglie
      </div>
    </div>
    <md-dialog :md-active.sync="showFamiliesDialog">
      <md-dialog-title>Preferences</md-dialog-title>

      <md-tabs md-dynamic-height>
        <md-tab md-label="General">
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
        </md-tab>

        <md-tab md-label="Activity">
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
        </md-tab>

        <md-tab md-label="Account">
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Ullam mollitia dolorum dolores quae commodi impedit possimus qui, atque at voluptates cupiditate. Neque quae culpa suscipit praesentium inventore ducimus ipsa aut.</p>
        </md-tab>
      </md-tabs>

      <md-dialog-actions>
        <md-button class="md-primary" @click="showDialog = false">Close</md-button>
        <md-button class="md-primary" @click="showDialog = false">Save</md-button>
      </md-dialog-actions>
    </md-dialog>
    <!-- <el-dialog
      title="Aggiungi persone"
      :visible="showPeopleDialog"
      width="55%"
      @close="nullInputs"
    >
      <div class="input-cont">Numero persone <input type="number" v-model="newPeople.quantity" /></div>
      <div class="input-cont">Giorni di presenza <input type="number" v-model="newPeople.days" /></div>
      <div class="buttons-cont">
        <div class="butt cancel"><font-awesome-icon icon="xmark" @click="showPeopleDialog = false" /> Annulla</div>
        <div class="butt confirm"><font-awesome-icon icon="check" @click="addPeople" /> Conferma</div>
      </div>
    </el-dialog>
    <el-dialog
      title="Aggiungi famiglie"
      :visible="showFamiliesDialog"
      width="55%"
      @close="nullInputs"
    >
      <div class="input-cont">Numero famiglie <input v-model="newFamilies.quantity" type="number" /></div>
      <div class="input-cont">Numero membri <input v-model="newFamilies.capacity" type="number" /></div>
      <div class="input-cont">Giorni di presenza <input v-model="newFamilies.days" type="number" /></div>
      <div class="buttons-cont">
        <div class="butt cancel"><font-awesome-icon icon="xmark" @click="showFamiliesDialog = false" /> Annulla</div>
        <div class="butt confirm"><font-awesome-icon icon="check" @click="addFamilies" /> Conferma</div>
      </div>
    </el-dialog> -->
  </div>
</template>

<script>
import InfoCards from "./components/InfoCards";
export default {
  name: 'CashDivider',
  components: {
    InfoCards
  },
  data() {
    return {
      showPeopleDialog: false,
      showFamiliesDialog: false,
      people: {
        30: 1,
        40: 1,
      },
      families: {
        4: {
          30: 1
        },
      },
      newPeople: {
        quantity: null,
        days: null
      },
      newFamilies: {
        quantity: null,
        capacity: null,
        days: null
      }
    }
  },
  computed: {
    cashDivision() {
      let vm = this;
      let result = {
        50: 0,
        20: 0,
        10: 0,
        5: 0,
        2: 0,
        1: 0,
        0.5: 0
      }
      result = vm.calculateCashDivision(vm.people, result);
      for (const [familyCapacity, family] of Object.entries(vm.families)) {
        result = vm.calculateCashDivision(family, result, familyCapacity);
      }
      return result;
    }
  },
  methods: {
    addPeople() {
      let vm = this;
      if (vm.newPeople.quantity && vm.newPeople.days) {
        if (vm.people[vm.newPeople.days]) {
          vm.people[vm.newPeople.days] += vm.newPeople.quantity;
        } else {
          vm.people[vm.newPeople.days] = vm.newPeople.quantity;
        }
      }
    },
    addFamilies() {
      let vm = this;
      if (vm.newFamilies.quantity && vm.newFamilies.days && vm.newFamilies.capacity) {
        if (vm.families[vm.newFamilies.capacity] && vm.families[vm.newFamilies.capacity][vm.newFamilies.days]) {
          vm.families[vm.newFamilies.capacity][vm.newFamilies.days] += vm.newFamilies.quantity;
        } else {
          vm.families[vm.newFamilies.capacity][vm.newFamilies.days] = vm.newFamilies.quantity;
        }
      }
    },
    calculateCashDivision(object, result, familyCapacity = 1) {
      for (const [days, quantity] of Object.entries(object)) {
        let total = days * 2.5 * familyCapacity;
        for (const cashSize of [50, 20, 10, 5, 2, 1, 0.5]) {
          if (total / cashSize >= 1) {
            result[cashSize] += parseInt(total / cashSize) * quantity;
            total -= cashSize * parseInt(total / cashSize);
          }
        }
      }
      return result;
    },
    nullInputs() {
      let vm = this;
      vm.newPeople = {
        quantity: null,
        days: null
      };
      vm.newFamilies = {
        quantity: null,
        capacity: null,
        days: null
      };
    }
  }
}
</script>

<style>
  h1 {
    text-align: center;
  }
  .page {
    font-family: Arial, sans-serif;
    padding: 20px 50px;
    background: #ffecd7;
    position: absolute;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    box-sizing: border-box;
    display: flex;
    justify-content: center;
  }
  .main-cont {
    width: 100%;
    max-width: 750px;
  }
  .result-container {
    width: 100%;
    margin-bottom: 20px;
    text-align: center;
    border-spacing: 0;
  }
  .result-container td {
    background: #bcfff5;
    border: none;
  }
  .result-container td.first {
    background: #ffefb7;
  }
  .add-button {
    width: 100%;
    margin-bottom: 20px;
    background: #837262;
    color: #fff;
    height: 42px;
    border-radius: 5px;
    display: flex;
    justify-content: center;
    align-items: center;
    font-weight: bold;
    cursor: pointer;
    box-shadow: 0 1px 2px rgba(0,0,0,.12);
  }
  input {
    height: 25px;
    width: 60px;
    text-align: center;
  }
  .input-cont {
    width: 50%;
    min-width: 210px;
    max-width: 300px;
    margin-bottom: 15px;
    display: flex;
    justify-content: space-between;
  }
  .buttons-cont {
    width: 100%;
    display: flex;
    justify-content: flex-end;
  }
  .butt {
    padding: 10px;
    border-radius: 4px;
    margin-left: 5px;
    color: #fff;
    cursor: pointer;
  }
  .cancel {
    background: #837262;
  }
  .confirm {
    background: #0bbf47;
  }
</style>