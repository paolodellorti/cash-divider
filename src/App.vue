<template>
  <div class="page" :class="{'is-mobile-width': isMobileWidth}">
    <h1>Pocket Money Manager</h1>
    <div class="results-cont md-elevation-2">
      <div class="labels">
        <div
          v-for="size in [50,20,10,5,2,1,'0,50','0,20','0,10']"
          :key="size"
          class="label"
          :class="{'no-border': size == '0,10'}"
        >{{ size }} €</div>
      </div>
      <div class="results">
        <div
          v-for="size in [50,20,10,5,2,1,0.5,0.2,0.1]"
          :key="size"
          class="label"
          :class="{'no-border': size == 0.1}"
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
        v-for="(peopleGroup, index) in people"
        :key="index"
      >
        <md-list-item>
          <md-icon>{{ peopleGroup.quantity == 1 ? 'person' : peopleGroup.quantity == 2 ? 'group' : 'groups' }}</md-icon>
          <span class="md-list-item-text">
            <span v-if="peopleGroup.name">
              <b>{{ peopleGroup.name }}</b>
            </span>
            <span><b>{{ peopleGroup.quantity }}</b> person{{ peopleGroup.quantity == 1 ? 'a' : 'e' }}
              con <br v-if="isMobileWidth"><b>{{ peopleGroup.days }}</b> giorn{{ peopleGroup.days == 1 ? 'o': 'i' }} di presenza
            </span>
            <span v-if="peopleGroup.advancePayment">
              <b>€ {{ peopleGroup.advancePayment }}</b> di anticipo
            </span>
          </span>
          <md-button class="md-icon-button md-list-action" @click="openEditDialog(peopleGroup, index)">
            <md-icon class="md-primary">edit_square</md-icon>
            <md-tooltip v-if="!isMobileDevice" md-direction="top">Modifica</md-tooltip>
          </md-button>
          <md-button class="md-icon-button md-list-action" @click="deletingPeople = index; showDeleteDialog = true">
            <md-icon class="md-accent">delete</md-icon>
            <md-tooltip v-if="!isMobileDevice" md-direction="top">Elimina</md-tooltip>
          </md-button>
        </md-list-item>
        <md-divider v-if="index + 1 < people.length"></md-divider>
      </div>
      <div
        v-if="!people.length"
        class="no-data"
      >
        Nessun dato
      </div>
      <md-button class="md-raised md-primary" @click="showAddPeopleDialog = true">
        <md-icon class="md-primary">add</md-icon> Aggiungi
      </md-button>
    </md-list><br>
    <md-list class="md-elevation-2">
      <h2>Famiglie</h2>
      <div
        v-for="(family, index) in families"
        :key="family.name"
      >
        <md-list-item>
          <md-icon>family_restroom</md-icon>
          <span class="md-list-item-text">
            <span v-if="family.name">
              <b>{{family.name}}</b>
            </span>
            <span>
              <b>{{ family.quantity }}</b> famigli{{ family.quantity == 1 ? 'a' : 'e' }} di <br v-if="isMobileWidth"><b>{{ family.members }}</b> membri con <br v-if="isMobileWidth"><b>{{ family.days }}</b> giorn{{ family.days == 1 ? 'o': 'i' }} di presenza
            </span>
            <span v-if="family.advancePayment">
              <b>€ {{ family.advancePayment }}</b> di anticipo
            </span>
          </span>
          <md-button class="md-icon-button md-list-action" @click="openEditDialog(family, index)">
            <md-icon class="md-primary">edit_square</md-icon>
            <md-tooltip v-if="!isMobileDevice" md-direction="top">Modifica</md-tooltip>
          </md-button>
          <md-button class="md-icon-button md-list-action" @click="deletingFamilies = index; showDeleteDialog = true;">
            <md-icon class="md-accent">delete</md-icon>
            <md-tooltip v-if="!isMobileDevice" md-direction="top">Elimina</md-tooltip>
          </md-button>
        </md-list-item>
        <md-divider v-if="index + 1 < families.length || exceptionalFamilies.length"></md-divider>
      </div>
      <div
        v-for="(family, index) in exceptionalFamilies"
        :key="family.members + ',' + index"
      >
        <md-list-item>
          <md-icon>family_restroom</md-icon>
          <span class="md-list-item-text">
            <span v-if="family.name">
              <b>{{family.name}}</b>
            </span>
            <span>
              <b>1</b> famiglia di <b>{{ family.members }}</b> membri di cui: <br>
              <div
                v-for="attendance in family.attendances"
                :key="attendance.days + 'family.attendances'"
                class="family-attendances"
              >
                <b>{{ attendance.members }}</b> membr{{ attendance.members == 1 ? 'o' : 'i' }} con <br v-if="isMobileWidth">
                <b>{{ attendance.days }}</b> giorn{{ attendance.days == 1 ? 'o': 'i' }} di presenza
              </div>
            </span>
            <span v-if="family.advancePayment">
              <b>€ {{ family.advancePayment }}</b> di anticipo
            </span>
          </span>
          <md-button class="md-icon-button md-list-action" @click="deletingExceptionalFamily = index; showDeleteDialog = true;">
            <md-icon class="md-accent">delete</md-icon>
            <md-tooltip v-if="!isMobileDevice" md-direction="top">Elimina</md-tooltip>
          </md-button>
        </md-list-item>
        <md-divider v-if="index + 1 < exceptionalFamilies.length"></md-divider>
      </div>
      <div
        v-if="!families.length && !exceptionalFamilies.length"
        class="no-data"
      >
        Nessun dato
      </div>
      <md-button class="md-raised md-primary" @click="showFamilyTypesDialog = true">
        <md-icon class="md-primary">add</md-icon> Aggiungi
      </md-button>
    </md-list>

    <div class="reset-button-cont">
      <md-button
        class="md-raised md-accent"
        :disabled="!people.length && !families.length && !exceptionalFamilies.length"
        @click="showResetDialog = true"
      >
        <md-icon class="md-primary">restart_alt</md-icon> Resetta
      </md-button>
    </div>

    <div class="footer">
      Pocket Money Manager v3.0<br>by Paolo Dell'Orti
    </div>

    <md-dialog
      :class="{'is-mobile-device': isMobileDevice}"
      :md-active.sync="showAddPeopleDialog"
      :md-fullscreen="false"
      @md-closed="onCloseDialog('people')"
    >
      <md-dialog-title>{{ isEditing !== null ? 'Modifica' : 'Aggiungi persone' }}</md-dialog-title>
        <span v-if="isEditing === null" class="info-box">
          Aggiungi una o più persone con lo stesso numero di presenze.<br v-if="!isMobileWidth">
          Usa il nome per identificare la persona singola o la casa.
        </span>
        <md-field :class="{'md-invalid': noValidForm && newPeople.quantity < 1}">
          <md-icon>groups</md-icon>
          <label>Numero di persone</label>
          <md-input v-model="newPeople.quantity" type="number" :min="1" required></md-input>
        </md-field>
        <md-field :class="{'md-invalid': noValidForm && newPeople.days < 1}">
          <md-icon>calendar_month</md-icon>
          <label>Giorni di presenza</label>
          <md-input v-model="newPeople.days" type="number" :min="1" required></md-input>
        </md-field>
        <md-field>
          <md-icon>euro</md-icon>
          <label>Anticipo</label>
          <md-input v-model="newPeople.advancePayment" type="number" :min="0"></md-input>
        </md-field>
        <md-field>
          <md-icon>badge</md-icon>
          <label>Nome</label>
          <md-input v-model="newPeople.name"></md-input>
        </md-field>
      <md-dialog-actions>
        <md-button @click="showAddPeopleDialog = false">Annulla</md-button>
        <md-button class="md-primary" @click="addPeople">{{ isEditing !== null ? 'Modifica' : 'Aggiungi' }}</md-button>
      </md-dialog-actions>
    </md-dialog>
    <md-dialog
      :class="{'is-mobile-device': isMobileDevice}"
      :md-active.sync="showFamilyTypesDialog"
      :md-fullscreen="false"
    >
      <md-card
        class="families-type-card first"
        md-with-hover
        @click.native="showAddFamiliesDialog = true;showFamilyTypesDialog = false"
      >
        <md-ripple>
          <div class="families-type-card-container">
            <md-icon>add</md-icon>
            <md-card-header>
              <div class="md-title">Famiglia/e standard</div>
              <div class="md-subhead">Aggiungi una o più famiglie in cui i membri hanno tutti lo stesso numero di presenze</div>
            </md-card-header>
          </div>
        </md-ripple>
      </md-card>
      <md-card
        class="families-type-card"
        md-with-hover
        @click.native="showAddExceptionalFamilyDialog = true;showFamilyTypesDialog = false"
      >
        <md-ripple>
          <div class="families-type-card-container">
            <md-icon>add</md-icon>
            <md-card-header>
              <div class="md-title">Famiglia non omogenea</div>
              <div class="md-subhead">Aggiungi una famiglia in cui i membri <b>NON</b> hanno tutti lo stesso numero di presenze</div>
            </md-card-header>
          </div>
        </md-ripple>
      </md-card>
    </md-dialog>
    <md-dialog
      :class="{'is-mobile-device': isMobileDevice}"
      :md-active.sync="showAddFamiliesDialog"
      :md-fullscreen="false"
      @md-closed="onCloseDialog('families')"
    >
      <md-dialog-title>{{ isEditing !== null ? 'Modifica' : 'Aggiungi famiglie' }}</md-dialog-title>
      <span v-if="isEditing === null" class="info-box">
        Aggiungi una o più famiglie con lo stesso numero di membri e presenze.
      </span>
      <md-field :class="{'md-invalid': noValidForm && newFamilies.quantity < 1}">
        <md-icon>family_restroom</md-icon>
        <label>Numero di famiglie</label>
        <md-input v-model="newFamilies.quantity" type="number" :min="1" required></md-input>
      </md-field>
      <md-field :class="{'md-invalid': noValidForm && newFamilies.members < 2}">
        <md-icon>groups</md-icon>
        <label>Numero di membri per famiglia</label>
        <md-input v-model="newFamilies.members" type="number" :min="2" required></md-input>
        <span class="md-error">Il numero di membri per famiglia deve essere almeno 2</span>
      </md-field>
      <md-field :class="{'md-invalid': noValidForm && newFamilies.days < 1}">
        <md-icon>calendar_month</md-icon>
        <label>Giorni di presenza</label>
        <md-input v-model="newFamilies.days" type="number" :min="1" required></md-input>
      </md-field>
      <md-field>
        <md-icon>euro</md-icon>
        <label>Anticipo</label>
        <md-input v-model="newFamilies.advancePayment" type="number" :min="0"></md-input>
      </md-field>
      <md-field>
        <md-icon>badge</md-icon>
        <label>Nome</label>
        <md-input v-model="newFamilies.name"></md-input>
      </md-field>
      <md-dialog-actions>
        <md-button @click="showAddFamiliesDialog = false">Annulla</md-button>
        <md-button class="md-primary" @click="addFamilies">{{ isEditing !== null ? 'Modifica' : 'Aggiungi' }}</md-button>
      </md-dialog-actions>
    </md-dialog>
    <md-dialog
      :class="{'is-mobile-device': isMobileDevice}"
      :md-active.sync="showAddExceptionalFamilyDialog"
      :md-fullscreen="false"
      @md-closed="onCloseDialog('exceptionalFamilies')"
    >
      <md-dialog-title>Aggiungi famiglia non omogenea</md-dialog-title>
      <span class="info-box">
        Aggiungi una famiglia in cui ci sono membri con un numero di presenze diverse l'uno dall'altro.<br v-if="!isMobileWidth">
        Inserisci il nome e il numero di membri totali, poi potrai scegliere il numero di presenze dei singoli membri.
      </span>
      <md-field :class="{'md-invalid': noValidForm && newExceptionalFamily.members < 2}">
        <md-icon>groups</md-icon>
        <label>Numero di membri della famiglia</label>
        <md-input v-model="newExceptionalFamily.members" type="number" :min="2" required></md-input>
        <span class="md-error">Il numero di membri della famiglia deve essere almeno 2</span>
      </md-field>
      <md-field>
        <md-icon>euro</md-icon>
        <label>Anticipo</label>
        <md-input v-model="newExceptionalFamily.advancePayment" type="number" :min="0"></md-input>
      </md-field>
      <md-field>
        <md-icon>badge</md-icon>
        <label>Nome</label>
        <md-input v-model="newExceptionalFamily.name"></md-input>
      </md-field>
      <md-dialog-actions>
        <md-button @click="showAddExceptionalFamilyDialog = false">Annulla</md-button>
        <md-button class="md-primary" @click="checkAddAttendances">Continua</md-button>
      </md-dialog-actions>
    </md-dialog>
    <md-dialog
      :class="{'is-mobile-device': isMobileDevice}"
      :md-active.sync="showAddAttendancesDialog"
      :md-fullscreen="false"
      @md-closed="onCloseDialog('exceptionalFamilies')"
    >
      <md-dialog-title>Aggiungi famiglia non omogenea</md-dialog-title>
      <span class="info-box">
        Aggiungi i membri raggruppati per giorni di presenza.<br v-if="!isMobileWidth">
        Potrai salvare la famiglia solo quando avrai inserito tutti i membri con le relative presenze.
      </span>
      <h3 class="md-invalid">
        <md-icon v-if="parseInt(selectedMemberNewExceptionalFamily) == parseInt(newExceptionalFamily.members)" class="confirm">check</md-icon> &nbsp;
        <span :class="{confirm: parseInt(selectedMemberNewExceptionalFamily) == parseInt(newExceptionalFamily.members)}">{{ parseInt(selectedMemberNewExceptionalFamily) }}/{{ parseInt(newExceptionalFamily.members) }}</span>
        membri aggiunti
      </h3>
      <md-list>
        <div
          v-for="(attendance, index) in newExceptionalFamily.attendances"
          :key="attendance.days + ',' + attendance.members"
        >
          <md-list-item>
            <md-icon>{{ attendance.members == 1 ? 'person' : attendance.members == 2 ? 'group' : 'groups' }}</md-icon>
            <span class="md-list-item-text">
              <span>
                <b>{{ attendance.members }}</b> membr{{ attendance.members == 1 ? 'o' : 'i' }} con <b>{{ attendance.days }}</b> giorni di presenza
              </span>
            </span>
            <md-button class="md-icon-button md-list-action" @click="newExceptionalFamily.attendances.splice(index, 1)">
              <md-icon class="md-accent">delete</md-icon>
              <md-tooltip v-if="!isMobileDevice" md-direction="top">Elimina</md-tooltip>
            </md-button>
          </md-list-item>
          <md-divider v-if="index + 1 < newExceptionalFamily.attendances.length"></md-divider>
        </div>
        <div
          v-if="!newExceptionalFamily.attendances.length"
          class="no-data"
        >
          Nessun dato
        </div>
        <md-button
          class="md-raised md-primary"
          :disabled="newExceptionalFamily.members == selectedMemberNewExceptionalFamily"
          @click="showAddAttendanceDialog = true"
        >
          <md-icon class="md-primary">add</md-icon> Aggiungi
        </md-button>
      </md-list>
      <md-dialog-actions>
        <md-button @click="showAddAttendancesDialog = false; showAddExceptionalFamilyDialog = true">Indietro</md-button>
        <md-button class="md-primary" @click="addExceptionalFamily">Salva</md-button>
      </md-dialog-actions>
    </md-dialog>
    <md-dialog
      :md-active.sync="showAddAttendanceDialog"
      class="add-attendance-dialog"
      :class="{'is-mobile-device': isMobileDevice}"
      :md-fullscreen="false"
      @md-opened="noValidForm = false"
    >
      <h3>
        <span
          :class="{
            'invalid-text': parseInt(selectedMemberNewExceptionalFamily || 0) + parseInt(newAttendance.members || 0) > parseInt(newExceptionalFamily.members),
            'confirm': parseInt(selectedMemberNewExceptionalFamily || 0) + parseInt(newAttendance.members || 0) == parseInt(newExceptionalFamily.members)
          }"
        >
          {{ parseInt(selectedMemberNewExceptionalFamily || 0) + parseInt(newAttendance.members || 0) }}/{{ parseInt(newExceptionalFamily.members) }}
        </span>
        membri aggiunti
      </h3>
      <md-field :class="{'md-invalid': noValidForm && (newAttendance.members < 1 || (parseInt(selectedMemberNewExceptionalFamily + newAttendance.members) > parseInt(newExceptionalFamily.members))) }">
        <md-icon>groups</md-icon>
        <label>Numero di membri (min 1, max {{ newExceptionalFamily.members - selectedMemberNewExceptionalFamily }})</label>
        <md-input v-model="newAttendance.members" type="number" :min="1" :max="parseInt(newExceptionalFamily.members) - parseInt(selectedMemberNewExceptionalFamily) + parseInt(newAttendance.members)" required></md-input>
      </md-field>
      <md-field :class="{'md-invalid': noValidForm && newAttendance.days < 1}">
        <md-icon>calendar_month</md-icon>
        <label>Giorni di presenza</label>
        <md-input v-model="newAttendance.days" type="number" :min="1" required></md-input>
      </md-field>
      <md-dialog-actions>
        <md-button @click="showAddAttendanceDialog = false; showAddAttendancesDialog = true">Indietro</md-button>
        <md-button class="md-primary" @click="addAttendance">Aggiungi</md-button>
      </md-dialog-actions>
    </md-dialog>
    <md-dialog-confirm
      class="delete-dialog"
      :md-active.sync="showDeleteDialog"
      md-title="Vuoi veramente eliminare il dato?"
      :md-content="deletingDataSentence"
      md-confirm-text="Elimina"
      md-cancel-text="Annulla"
      @md-cancel="showDeleteDialog = false; deletingPeople = null; deletingFamilies = null; deletingExceptionalFamily = null;"
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
      <span><md-icon class="confirm">check_circle</md-icon>&nbsp;Conteggio copiato</span>
      <md-button class="md-accent" @click="showCopySnackbar = false">
        <md-icon class="md-accent">close</md-icon>
      </md-button>
    </md-snackbar>
    <md-snackbar md-position="center" :md-duration="2000" :md-active.sync="showErrorSnackbar" md-persistent>
      <span><md-icon class="warning">error</md-icon>&nbsp;{{ errorSentence || "Compila il form correttamente per continuare" }}</span>
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
      people: [],
      families: [],
      exceptionalFamilies: [],
      newPeople: {
        name: null,
        quantity: null,
        days: null,
        advancePayment: null
      },
      newFamilies: {
        name: null,
        quantity: null,
        members: null,
        days: null,
        advancePayment: null
      },
      newExceptionalFamily: {
        name: null,
        members: null,
        attendances: [],
        advancePayment: null
      },
      newAttendance: {
        members: null,
        days: null
      },
      isEditing: null,
      showAddPeopleDialog: false,
      showAddFamiliesDialog: false,
      showAddExceptionalFamilyDialog: false,
      showAddAttendancesDialog: false,
      showAddAttendanceDialog: false,
      showFamilyTypesDialog: false,
      showDeleteDialog: false,
      showResetDialog: false,
      deletingPeople: null,
      deletingFamilies: null,
      deletingExceptionalFamily: null,
      windowWidth: 0,
      trigger: 0,
      noValidForm: false,
      showCopySnackbar: false,
      showErrorSnackbar: false,
      autocompleteOptions: [31,30,29,28,27,26,25,24,23,22,21,20,19,18,17,16,15,14,13,12,11,10,9,8,7,6,5,4,3,2,1],
      errorSentence: null
    }
  },
  watch: {
    "newExceptionalFamily.members": {
      handler() {
        let vm = this;
        vm.newExceptionalFamily.attendances = [];
      }
    },
    errorSentence(newVal) {
      let vm = this;
      if (newVal) {
        setTimeout(() => {
          vm.errorSentence = null;
        }, 2200)
      }
    }
  },
  computed: {
    cashDivision() {
      let vm = this;
      vm.trigger++;
      localStorage.setItem('cashDivisor', JSON.stringify([vm.people, vm.families, vm.exceptionalFamilies]));
      let result = {
        50: 0,
        20: 0,
        10: 0,
        5: 0,
        2: 0,
        1: 0,
        0.5: 0,
        0.2: 0,
        0.1: 0
      };
      const calculateTotal = (days, members = 1, advancePayment = 0) => {
        return (days * 2.5 * members) - advancePayment;
      };

      const updateResult = (total, quantity = 1) => {
        for (const cashSize of [50, 20, 10, 5, 2, 1, 0.5, 0.2, 0.1]) {
          if (total / cashSize >= 1) {
            let count = parseInt(total / cashSize);
            result[cashSize] += count * quantity;
            let deduction = Math.round(cashSize * count * 100) / 100;
            total = Math.round((total - deduction) * 100) / 100;
          }
        }
        return total;
      };
      for (const person of vm.people) {
        let total = calculateTotal(person.days, 1, person.advancePayment);
        updateResult(total, person.quantity);
      }
      for (const family of vm.families) {
        let total = calculateTotal(family.days, family.members, family.advancePayment);
        updateResult(total, family.quantity);
      }
      for (const family of vm.exceptionalFamilies) {
        let total = 0;
        for (const attendance of family.attendances) {
          total += calculateTotal(attendance.days, attendance.members);
        }
        total -= (family.advancePayment || 0);
        updateResult(total);
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
      if (vm.deletingPeople !== null) {
        const peopleGroup = vm.people[vm.deletingPeople];
        return `
          ${peopleGroup.name ? '<b>' + peopleGroup.name + '</b><br>' : ''}
          <b>${peopleGroup.name}</b><br>
          <b>${peopleGroup.quantity}</b> person${peopleGroup.quantity == 1 ? 'a' : 'e'}
          con <b>${peopleGroup.days}</b> giorn${peopleGroup.days ? 'o' : 'i'} di presenza
          ${peopleGroup.advancePayment ? '<br> <b>€' + peopleGroup.advancePayment + '</b> anticipo' : ''}
        `
      } else if (vm.deletingFamilies !== null) {
        const family = vm.families[vm.deletingFamilies];
        return `
          ${family.name ? '<b>' + family.name + '</b><br>' : ''}
          <b>${family.quantity}</b> famigli${family.quantity == 1 ? 'a' : 'e'}
          di <b>${family.members}</b> membri<brZ
          con <b>${family.days}</b> giorn${family.days == 1 ? 'o' : 'i'} di presenza
          ${family.advancePayment ? '<br> <b>€' + family.advancePayment + '</b> anticipo' : ''}
        `
      } else if (vm.deletingExceptionalFamily !== null) {
        const exceptionalFamily = vm.exceptionalFamilies[vm.deletingExceptionalFamily];
        let result = `
          ${exceptionalFamily.name ? '<b>' + exceptionalFamily.name + '</b><br>' : ''}
          <b>1</b> famiglia di <b>${exceptionalFamily.members}</b> membri di cui:<br>
        `;
        for (const attendance of exceptionalFamily.attendances) {
          result += `
            &nbsp;&nbsp; - <b>${attendance.members}</b> membr${attendance.members == 1 ? 'o' : 'i'}
            con <b>${attendance.days}</b> giorn${attendance.days == 1 ? 'o' : 'i'} di presenza<br>
          `
        }
        result += exceptionalFamily.advancePayment ? '<br> <b>€' + exceptionalFamily.advancePayment + '</b> anticipo' : ''
        return result;
      }
      return '';
    },
    selectedMemberNewExceptionalFamily() {
      let vm = this;
      return parseInt(vm.newExceptionalFamily.attendances.reduce((acc, attendance) => {
        return acc + attendance.members;
      }, 0));
    },
    isMobileDevice() {
      return !!(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent));
    },
    isMobileWidth() {
      return !!(useWindowSize().width.value < 768);
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
        if (vm.isEditing !== null) {
          vm.people[vm.isEditing] = vm.newPeople;
        } else {
          vm.people.push(vm.newPeople);
        }
        vm.newPeople = {
          name: null,
          quantity: null,
          days: null,
          advancePayment: null
        };
        vm.noValidForm = false;
        vm.showAddPeopleDialog = false;
      }
    },
    addFamilies() {
      let vm = this;
      if (vm.newFamilies.quantity < 1 || vm.newFamilies.days < 1 || vm.newFamilies.members < 2) {
        vm.noValidForm = true;
        vm.showErrorSnackbar = true;
      } else {
        vm.trigger ++;
        if (vm.isEditing !== null) {
          vm.families[vm.isEditing] = vm.newFamilies;
        } else {
          vm.families.push(vm.newFamilies);
        }
        vm.newFamilies = {
          quantity: null,
          members: null,
          days: null,
          advancePayment: null
        };
        vm.noValidForm = false;
        vm.showAddFamiliesDialog = false;
      }
    },
    addExceptionalFamily() {
      let vm = this;
      if (vm.selectedMemberNewExceptionalFamily != vm.newExceptionalFamily.members) {
        vm.errorSentence = 'Aggiungi tutti i membri con le relative presenze per continuare';
        vm.noValidForm = true;
        vm.showErrorSnackbar = true;
      } else {
        vm.exceptionalFamilies.push({
          name: vm.newExceptionalFamily.name,
          members: vm.newExceptionalFamily.members,
          advancePayment: vm.newExceptionalFamily.advancePayment,
          attendances: vm.newExceptionalFamily.attendances
        });
        vm.newExceptionalFamily.members = null;
        vm.showAddAttendancesDialog = false;
      }
    },
    addAttendance() {
      let vm = this;
      if (vm.newAttendance.members < 1 || vm.newAttendance.days < 1 || (vm.newAttendance.members > (vm.newExceptionalFamily.members - vm.selectedMemberNewExceptionalFamily))) {
        vm.errorSentence = !vm.newAttendance.members || !vm.newAttendance.days ? null : 'Non puoi aggiungere un numero di membri maggiore al numero totale dei membri della famiglia';
        vm.noValidForm = true;
        vm.showErrorSnackbar = true;
      } else {
        const alreadyExist = vm.newExceptionalFamily.attendances.findIndex(attendance => attendance.days == vm.newAttendance.days);
        if (alreadyExist != -1) {
          vm.newExceptionalFamily.attendances[alreadyExist].members += parseInt(vm.newAttendance.members);
        } else {
          vm.newExceptionalFamily.attendances.push({
            days: parseInt(vm.newAttendance.days),
            members: parseInt(vm.newAttendance.members)
          });
        }
        vm.newAttendance = {
          days: null,
          members: null
        }
        vm.showAddAttendanceDialog = false;
        vm.showAddAttendancesDialog = true;
      }
    },
    deleteData() {
      let vm = this;
      vm.trigger ++;
      if (vm.deletingPeople !== null) {
        vm.people.splice(vm.deletingPeople, 1)
      } else if (vm.deletingFamilies !== null) {
        vm.families.splice(vm.deletingFamilies, 1)
      } else if (vm.deletingExceptionalFamily !== null) {
        vm.exceptionalFamilies.splice(vm.deletingExceptionalFamily, 1);
      }
      vm.deletingPeople = null;
      vm.deletingFamilies = null;
      vm.deletingExceptionalFamily = null;
      vm.showDeleteDialog = false;
    },
    resetAll() {
      let vm = this;
      vm.trigger ++;
      vm.people = [];
      vm.families = [];
      vm.exceptionalFamilies = [];
      vm.newPeople = {
        name: null,
        quantity: null,
        days: null,
        advancePayment: null
      };
      vm.newFamilies = {
        name: null,
        quantity: null,
        members: null,
        days: null,
        advancePayment: null
      };
      vm.newExceptionalFamily = {
        name: null,
        members: null,
        advancePayment: null,
        attendances: []
      };
      vm.newAttendance = {
        name: null,
        members: null,
        days: null
      };
      vm.showResetDialog = false;
    },
    openEditDialog(peopleGroup, index) {
      let vm = this;
      vm.isEditing = index;
      if (!peopleGroup.members) {
        vm.newPeople = {
          name: peopleGroup.name,
          days: peopleGroup.days,
          quantity: peopleGroup.quantity,
          advancePayment: peopleGroup.advancePayment
        };
        vm.showAddPeopleDialog = true;
      } else {
        vm.newFamilies = {
          name: peopleGroup.name,
          days: peopleGroup.days,
          quantity: peopleGroup.quantity,
          members: peopleGroup.members,
          advancePayment: peopleGroup.advancePayment
        };
        vm.showAddFamiliesDialog = true;
      }
    },
    onCloseDialog(dialog) {
      let vm = this;
      vm.noValidForm = false;
      if (vm.isEditing !== null && dialog == 'people') {
        vm.newPeople = {
          name: null,
          days: null,
          quantity: null,
          advancePayment: null
        };
      } else if (vm.isEditing !== null  && dialog == 'families') {
        vm.newFamilies = {
          name: null,
          days: null,
          quantity: null,
          members: null,
          advancePayment: null
        };
      } else if (vm.isEditing !== null && dialog == 'exceptionalFamilies') {
        vm.newExceptionalFamily = {
          name: null,
          members: null,
          attendances: [],
          advancePayment: null
        };
        vm.newAttendance = {
          days: null,
          members: null
        };
      }
      vm.isEditing = null;
    },
    checkAddAttendances() {
      let vm = this;
      if (vm.newExceptionalFamily.members < 2) {
        vm.noValidForm = true;
        vm.showErrorSnackbar = true;
      } else {
        vm.showAddExceptionalFamilyDialog = false;
        vm.showAddAttendancesDialog = true;
      }
    },
    copyText() {
      let vm = this;
      let result = '';
      for (let cashSize of [50,20,10,5,2,1,0.5,0.2,0.1]) {
        if (vm.cashDivision[cashSize]) {
          const cashSizeLabel = cashSize == 0.5 ? '0,50' : cashSize == 0.2 ? '0,20' : cashSize == 0.1 ? '0,10' : cashSize;
          result += cashSizeLabel + ' € => ' + vm.cashDivision[cashSize] + '\r\n';
        }
      }
      result += 'Totale => ' + vm.cashTotal;
      navigator.clipboard.writeText(result);
      vm.showCopySnackbar = true;
    },
  },
  beforeMount() {
    let vm = this;
    if (localStorage.cashDivisor) {
      let data = JSON.parse(localStorage.cashDivisor);
      if (Array.isArray(data[0]) && Array.isArray(data[1]) && Array.isArray(data[2])) {
        vm.people = data[0];
        vm.families = data[1];
        vm.exceptionalFamilies = data[2];
      } else {
        localStorage.setItem('cashDivisor', JSON.stringify([[], [], []]));
      }
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
.is-mobile-width {
  padding: 0;
}
.results-cont {
  width: 100%;
  margin-bottom: 20px;
  text-align: center;
  background: #fff;
  border-radius: 2px;
  padding: 8px 0;
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
.md-divider {
  margin: 0 8px!important;
}
.is-mobile-width .md-list-item-content>.md-icon:first-child {
  margin-right: 10px!important;
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
  max-height: 25vh!important;
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
.families-type-card {
  margin-bottom: 24px;
}
.families-type-card.first {
  margin-top: 24px;
}
.families-type-card .md-title {
  font-size: 20px!important;
}
.families-type-card-container {
  margin: 0 10px;
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}
.add-attendance-dialog .md-dialog-container {
  min-width: 350px;
}
.md-button .md-disabled .md-icon {
  color: rgba(0,0,0,0.26)!important;
}
.invalid-text {
  color: #ff1744;
}
.family-attendances {
  margin-left: 15px!important;
  padding-left: 5px;
  position: relative;
}
.is-mobile-width .family-attendances {
  margin-left: 10px!important;
}
.is-mobile-width .family-attendances:before {
  content: "";
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 1px;
  height: 65%;
  border-left: 1px solid #00000037;
}
.is-mobile-width .md-list-item-content .md-list-action:last-of-type {
  margin: auto!important;
}
.is-mobile-width .md-list-item-content .md-list-action {
  margin: 0 -15px 0 0;
}
.is-mobile-width .md-list-item-content {
  padding: 4px 8px;
}
</style>