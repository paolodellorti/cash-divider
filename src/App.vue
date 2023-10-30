<template>
  <div class="page" :class="{'is-mobile-width': isMobileWidth}">
    <h1>Pocket Money Manager</h1>
    <div class="results-cont md-elevation-2">
      <div class="labels">
        <div
          v-for="size in [50,20,10,5,2,'0,50']"
          :key="size"
          class="label"
          :class="{'no-border': size == '0,50'}"
        >{{ size }} €</div>
      </div>
      <div class="results">
        <div
          v-for="size in [50,20,10,5,2,0.5]"
          :key="size"
          class="label"
          :class="{'no-border': size == 0.5}"
        >{{ cashDivision[size] }}</div>
      </div>
    </div>
    <h3>
      Totale {{ cashTotal }}
      <br>
      <md-button class="md-primary" @click="copyText">
        <md-icon class="md-primary">content_copy</md-icon>
        Copia conteggio
      </md-button>
    </h3>
    <md-list class="md-elevation-2">
      <h2>Persone</h2>
      <div
        v-for="(quantity, days, index) in people"
        :key="days + 'people'"
      >
        <md-list-item>
          <md-icon>{{ quantity == 1 ? 'person' : 'groups' }}</md-icon>
          <span class="md-list-item-text">
            <span>
              <b>{{ quantity }}</b> person{{ quantity == 1 ? 'a' : 'e' }} con <br v-if="isMobileWidth"><b>{{ days }}</b> giorn{{ days == 1 ? 'o': 'i' }} di presenza
            </span>
          </span>
          <md-button class="md-icon-button md-list-action" @click="openEditDialog(days, quantity)">
            <md-icon class="md-primary">edit_square</md-icon>
            <md-tooltip v-if="!isMobileDevice" md-direction="top">Modifica</md-tooltip>
          </md-button>
          <md-button class="md-icon-button md-list-action" @click=" deletingPeople = days; showDeleteDialog = true">
            <md-icon class="md-accent">delete</md-icon>
            <md-tooltip v-if="!isMobileDevice" md-direction="top">Elimina</md-tooltip>
          </md-button>
        </md-list-item>
        <md-divider v-if="index + 1 < Object.keys(people).length"></md-divider>
      </div>
      <div
        v-if="!Object.keys(people).length"
        class="no-data"
      >
        Nessun dato
      </div>
      <md-button class="md-raised md-primary" @click="showPeopleDialog = true">
        <md-icon class="md-primary">add</md-icon> Aggiungi
      </md-button>
    </md-list><br>
    <md-list class="md-elevation-2">
      <h2>Famiglie</h2>
      <div
        v-for="(familyData, members, index) in families"
        :key="members + 'people'"
      >
        <div
          v-for="(quantity, days) in familyData"
          :key="quantity + 'people'"
        >
          <md-list-item>
            <md-icon>family_restroom</md-icon>
            <span class="md-list-item-text">
              <span>
                <b>{{ quantity }}</b> famigli{{ quantity == 1 ? 'a' : 'e' }} di <br v-if="isMobileWidth"><b>{{ members }}</b> membri con <br v-if="isMobileWidth"><b>{{ days }}</b> giorn{{ days == 1 ? 'o': 'i' }} di presenza
              </span>
            </span>
            <md-button class="md-icon-button md-list-action" @click="openEditDialog(days, quantity, members)">
              <md-icon class="md-primary">edit_square</md-icon>
              <md-tooltip v-if="!isMobileDevice" md-direction="top">Modifica</md-tooltip>
            </md-button>
            <md-button class="md-icon-button md-list-action" @click="deletingFamilies = [members, days]; showDeleteDialog = true;">
              <md-icon class="md-accent">delete</md-icon>
              <md-tooltip v-if="!isMobileDevice" md-direction="top">Elimina</md-tooltip>
            </md-button>
          </md-list-item>
          <md-divider v-if="index + 1 < Object.keys(families).length"></md-divider>
        </div>
      </div>
      <div
        v-if="!Object.keys(families).length"
        class="no-data"
      >
        Nessun dato
      </div>
      <md-button class="md-raised md-primary" @click="showFamiliesDialog = true">
        <md-icon class="md-primary">add</md-icon> Aggiungi
      </md-button>
    </md-list>

    <div class="reset-button-cont">
      <md-button class="md-raised md-accent" :disabled="!Object.keys(people).length && !Object.keys(families).length" @click="showResetDialog = true">
        <md-icon class="md-primary">restart_alt</md-icon> Resetta
      </md-button>
    </div>

    <div class="footer">
      Pocket Money Manager v1.04<br>by Paolo Dell'Orti
    </div>

    <md-dialog
      :class="{'is-mobile-device': isMobileDevice}"
      :md-active.sync="showPeopleDialog"
      :md-fullscreen="false"
      @md-closed="onCloseDialog(true)"
    >
      <md-dialog-title>{{ isEditing ? 'Modifica' : 'Aggiungi persone' }}</md-dialog-title>
        <span class="info-box">
          Aggiungi una o più persone con lo stesso numero di presenze. Nel caso esistesse già una o più persone con il numero di presenze indicato in questo form,
          verranno raggruppate in una riga, andando a sommare il totale.
        </span>
        <md-field :class="{'md-invalid': noValidForm && newPeople.quantity < 1}">
          <label>Numero di persone</label>
          <md-input v-model="newPeople.quantity" type="number" :min="1" required></md-input>
        </md-field>
        <md-autocomplete v-if="!isMobileDevice" v-model="newPeople.days" type="number" :min="1" :md-options="autocompleteOptions" :class="{'md-invalid': noValidForm && newPeople.days < 1}" required>
          <label>Giorni di presenza</label>
        </md-autocomplete>
        <md-field v-else :class="{'md-invalid': noValidForm && newPeople.days < 1}">
          <label>Giorni di presenza</label>
          <md-input v-model="newPeople.days" type="number" :min="1" required></md-input>
        </md-field>
      <md-dialog-actions>
        <md-button @click="showPeopleDialog = false">Annulla</md-button>
        <md-button class="md-primary" @click="addPeople">{{ isEditing ? 'Modifica' : 'Aggiungi' }}</md-button>
      </md-dialog-actions>
    </md-dialog>
    <md-dialog
      :class="{'is-mobile-device': isMobileDevice}"
      :md-active.sync="showFamiliesDialog"
      :md-fullscreen="false"
      @md-closed="onCloseDialog(false)"
    >
      <md-dialog-title>{{ isEditing ? 'Modifica' : 'Aggiungi famiglie' }}</md-dialog-title>
        <span class="info-box">
          Aggiungi uno o più gruppi famigliari con lo stesso numero di membri e presenze. Nel caso esistessero già uno o più gruppi famigliari con il numero di membri e presenze indicato in questo form,
          verranno raggruppati in una riga, andando a sommare il totale.
        </span>
        <md-field :class="{'md-invalid': noValidForm && newFamilies.quantity < 1}">
          <label>Numero di famiglie</label>
          <md-input v-model="newFamilies.quantity" type="number" required></md-input>
        </md-field>
        <md-field :class="{'md-invalid': noValidForm && newFamilies.members < 1}">
          <label>Numero di membri per famiglia</label>
          <md-input v-model="newFamilies.members" type="number" required></md-input>
        </md-field>
        <md-autocomplete v-if="!isMobileDevice" v-model="newFamilies.days" type="number" :min="1" :md-options="autocompleteOptions" :class="{'md-invalid': noValidForm && newFamilies.days < 1}" required>
          <label>Giorni di presenza</label>
        </md-autocomplete>
        <md-field v-else :class="{'md-invalid': noValidForm && newFamilies.days < 1}">
          <label>Giorni di presenza</label>
          <md-input v-model="newFamilies.days" type="number" :min="1" required></md-input>
        </md-field>
      <md-dialog-actions>
        <md-button @click="showFamiliesDialog = false">Annulla</md-button>
        <md-button class="md-primary" @click="addFamilies">{{ isEditing ? 'Modifica' : 'Aggiungi' }}</md-button>
      </md-dialog-actions>
    </md-dialog>
    <md-dialog-confirm
      class="delete-dialog"
      :md-active.sync="showDeleteDialog"
      md-title="Vuoi veramente eliminare il dato?"
      :md-content="deletingDataSentence"
      md-confirm-text="Elimina"
      md-cancel-text="Annulla"
      @md-cancel="showDeleteDialog = false; deletingPeople = null; deletingFamilies = null"
      @md-confirm="deleteData"
    />
    <md-dialog-confirm
      class="delete-dialog"
      :md-active.sync="showResetDialog"
      md-title="Vuoi veramente eliminare tutti i dati inseriti?"
      md-content="Perderai tutti i dati visibili, anche quelli salvati nel tuo browser"
      md-confirm-text="Resetta"
      md-cancel-text="Annulla"
      @md-cancel="showResetDialog = false"
      @md-confirm="resetAll"
    />
    <md-snackbar md-position="center" :md-duration="2000" :md-active.sync="showCopySnackbar" md-persistent>
      <span><md-icon class="confirm">check_circle</md-icon> Conteggio copiato</span>
      <md-button class="md-accent" @click="showCopySnackbar = false">
        <md-icon class="md-accent">close</md-icon>
      </md-button>
    </md-snackbar>
    <md-snackbar md-position="center" :md-duration="2000" :md-active.sync="showErrorSnackbar" md-persistent>
      <span><md-icon class="warning">error</md-icon> Compila il form correttamente per continuare</span>
      <md-button class="md-accent" @click="showErrorSnackbar = false">
        <md-icon class="md-accent">close</md-icon>
      </md-button>
    </md-snackbar>
  </div>
</template>

<script>
import { useWindowSize } from 'vue-window-size';

export default {
  name: 'CashDivider',
  data() {
    return {
      people: {},
      families: {},
      newPeople: {
        quantity: null,
        days: null
      },
      newFamilies: {
        quantity: null,
        members: null,
        days: null
      },
      isEditing: null,
      showPeopleDialog: false,
      showFamiliesDialog: false,
      showDeleteDialog: false,
      showResetDialog: false,
      deletingPeople: null,
      deletingFamilies: null,
      windowWidth: 0,
      trigger: 0,
      noValidForm: false,
      showCopySnackbar: false,
      showErrorSnackbar: false,
      autocompleteOptions: [31,30,29,28,27,26,25,24,23,22,21,20,19,18,17,16,15,14,13,12,11,10,9,8,7,6,5,4,3,2,1]
    }
  },
  computed: {
    cashDivision() {
      let vm = this;
      vm.trigger++;
      localStorage.setItem('cashDivisor', JSON.stringify([vm.people, vm.families]));
      let result = {
        50: 0,
        20: 0,
        10: 0,
        5: 0,
        2: 0,
        0.5: 0
      }
      result = vm.calculateCashDivision(vm.people, result);
      for (const [familymembers, family] of Object.entries(vm.families)) {
        result = vm.calculateCashDivision(family, result, familymembers);
      }
      return result;
    },
    cashTotal() {
      let vm = this;
      let result = 0;
      for (const [cash, quantity] of Object.entries(vm.cashDivision)) {
        result += cash * quantity;
      }
      const formatter = new Intl.NumberFormat('it-IT', {
        style: 'currency',
        currency: 'EUR'
      });
      return formatter.format(result);
    },
    deletingDataSentence() {
      let vm = this;
      if (vm.deletingPeople) {
        return `
          <b>${vm.people[vm.deletingPeople]}</b> person${vm.people[vm.deletingPeople] == 1 ? 'a' : 'e'}
          con <b>${vm.deletingPeople}</b> giorn${vm.deletingPeople == 1 ? 'o' : 'i'} di presenza
        `
      } else if (vm.deletingFamilies) {
        return `
          <b>${vm.families[vm.deletingFamilies[0]][vm.deletingFamilies[1]]}</b> famigli${vm.families[vm.deletingFamilies[0]][vm.deletingFamilies[1]] == 1 ? 'a' : 'e'}
          di <b>${vm.deletingFamilies[0]}</b> membri
          con <b>${vm.deletingFamilies[1]}</b> giorn${vm.deletingFamilies[1] == 1 ? 'o' : 'i'} di presenza
        `
      }
      return '1 famiglia da 3 membri con 4 giorni di presenza'
    },
    isMobileDevice() {
      return !!(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent));
    },
    isMobileWidth() {
      return !!useWindowSize().width.value < 768;
    },
  },
  methods: {
    addPeople() {
      let vm = this;
      if (vm.newPeople.quantity < 1 || vm.newPeople.days < 1) {
        vm.noValidForm = true;
        vm.showErrorSnackbar = true;
      } else {
        vm.trigger ++;
        if (vm.isEditing) {
          delete vm.people[vm.isEditing];
        }
        if (vm.people[parseInt(vm.newPeople.days)]) {
          vm.people[parseInt(vm.newPeople.days)] += parseInt(vm.newPeople.quantity);
        } else {
          vm.people[parseInt(vm.newPeople.days)] = parseInt(vm.newPeople.quantity);
        }
        vm.newPeople = {
          quantity: null,
          days: null
        };
        vm.noValidForm = false;
        vm.showPeopleDialog = false;
      }
    },
    addFamilies() {
      let vm = this;
      if (vm.newFamilies.quantity < 1 || vm.newFamilies.days < 1 || vm.newFamilies.members < 1) {
        vm.noValidForm = true;
        vm.showErrorSnackbar = true;
      } else {
        vm.trigger ++;
        if (vm.isEditing) {
          delete vm.families[vm.isEditing[0]][vm.isEditing[1]];
        }
        if (vm.families[parseInt(vm.newFamilies.members)] && vm.families[parseInt(vm.newFamilies.members)][parseInt(vm.newFamilies.days)]) {
          vm.families[parseInt(vm.newFamilies.members)][parseInt(vm.newFamilies.days)] += parseInt(vm.newFamilies.quantity);
        } else {
          vm.families[parseInt(vm.newFamilies.members)] = {
            ...vm.families[parseInt(vm.newFamilies.members)],
            [parseInt(vm.newFamilies.days)]: parseInt(vm.newFamilies.quantity)
          };
        }
        vm.newFamilies = {
          quantity: null,
          members: null,
          days: null
        };
        vm.noValidForm = false;
        vm.showFamiliesDialog = false;
      }
    },
    deleteData() {
      let vm = this;
      vm.trigger ++;
      if (vm.deletingPeople) {
        delete vm.people[vm.deletingPeople];
      } else if (vm.deletingFamilies) {
        delete vm.families[vm.deletingFamilies[0]][vm.deletingFamilies[1]];
        if (!Object.keys(vm.families[vm.deletingFamilies[0]]).length) {
          delete vm.families[vm.deletingFamilies[0]];
        }
      }
      vm.deletingPeople = null;
      vm.deletingFamilies = null
      vm.showDeleteDialog = false;
    },
    resetAll() {
      let vm = this;
      vm.trigger ++;
      vm.people = {};
      vm.families = {};
      vm.showResetDialog = false;
    },
    openEditDialog(days, quantity, members = null) {
      let vm = this;
      vm.isEditing = members ? [members, days] : days;
      if (!members) {
        vm.newPeople = {
          days: days,
          quantity: quantity
        };
        vm.showPeopleDialog = true;
      } else {
        vm.newFamilies = {
          days: days,
          quantity: quantity,
          members: members
        };
        vm.showFamiliesDialog = true;
      }
    },
    onCloseDialog(isPeopleDialog) {
      let vm = this;
      vm.noValidForm = false;
      if (vm.isEditing && isPeopleDialog) {
        vm.newPeople = {
          days: null,
          quantity: null,
        };
      } else if (vm.isEditing) {
        vm.newFamilies = {
          days: null,
          quantity: null,
          members: null
        };
      }
      vm.isEditing = null;
    },
    copyText() {
      let vm = this;
      let result = '';
      for (let [cashSize, quantity] of Object.entries(vm.cashDivision)) {
        if (quantity) {
          cashSize = cashSize == 0.5 ? '0,50' : cashSize;
          result += cashSize + ' € => ' + quantity + '\r\n';
        }
      }
      result += 'Totale => ' + vm.cashTotal;
      navigator.clipboard.writeText(result);
      vm.showCopySnackbar = true;
    },
    calculateCashDivision(object, result, familymembers = 1) {
      for (const [days, quantity] of Object.entries(object)) {
        let total = days * 2.5 * familymembers;
        for (const cashSize of [50, 20, 10, 5, 2, 1, 0.5]) {
          if (total / cashSize >= 1) {
            result[cashSize] += parseInt(total / cashSize) * quantity;
            total -= cashSize * parseInt(total / cashSize);
          }
        }
      }
      return result;
    }
  },
  beforeMount() {
    let vm = this;
    if (localStorage.cashDivisor) {
      vm.people = JSON.parse(localStorage.cashDivisor)[0];
      vm.families = JSON.parse(localStorage.cashDivisor)[1];
    }
  },
  created() {
    let vm = this;
    window.addEventListener("resize", () => vm.windowWidth = window.innerWidth);
  },
  destroyed() {
    let vm = this;
    window.removeEventListener("resize", () => vm.windowWidth = window.innerWidth);
  }
}
</script>

<style>
body {
  font-size: 16px;
}
h1 {
  line-height: 30px;
}
h1, h3, h2 {
  text-align: center;
}
h2 {
  margin-top: 5px;
  margin-bottom: 10px;
}
.page {
  margin: 10px auto;
  max-width: 750px;
  padding: 0 10px;
}
.results-cont {
  width: 100%;
  margin-bottom: 20px;
  text-align: center;
  background: #fff;
  border-radius: 2px;
  padding: 10px 0;
}
.results-cont .labels {
  color: #0000008A;
  font-weight: bold;
}
.results-cont .labels,
.results-cont .results {
  display: flex;
  justify-content: center;
  display: flex;
}
.results-cont .label {
  width: 16.5%;
  border-right: 1px solid #0000001f;
  font-size: 12px;
}
.results-cont .label {
  padding: 10px 0;
}
.results-cont .no-border {
  border-right: none;
}
.md-dialog-container {
  padding: 0 20px;
}
.md-list-item-text :nth-child(2), .md-list-item-text :nth-child(3) {
  font-size: 16px!important;
}
.no-data {
  text-align: center;
  font-size: 16px;
  font-style: italic;
  color: #9b9b9b;
  margin: 10px 0;
}
.footer {
  padding: 40px 0 15px 0;
  width: 100%;
  text-align: center;
  font-size: 12px;
  color: #9b9b9b;
  font-style: italic;
}
.delete-dialog .md-button.md-theme-default.md-primary {
  color: #ff5252!important;
}
.md-list-item-text {
  white-space: normal!important;
}
.md-menu-content {
  z-index: 9999!important;
}
.reset-button-cont {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 30px 0 0 0;
}
.reset-button-cont .md-disabled .md-icon {
  color: var(--md-theme-default-disabled, rgba(0,0,0,0.26))!important;
}
.md-table-head, .md-table-cell {
  text-align: center!important;
}
.is-mobile-width .md-table-head-label,
.is-mobile-width .md-table-cell-container {
  padding-right: 12px;
  padding-left: 12px;
}
.is-mobile-device.md-dialog {
  top: auto;
}
.is-mobile-device .md-dialog-container {
  max-width: 100%;
  width: 100%;
}
.confirm {
  color: #0bbf47!important;
}
.warning {
  color: #ff9800!important;
}
.info-box {
  font-style: italic;
  font-size: 12px;
  color: #0000008A;
}
</style>