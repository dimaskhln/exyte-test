<template>
  <v-card color="light-blue lighten-5" max-width="450" class="mx-auto my-12 pa-6">
    <v-container class="pa-0" elevation="3">
      <v-text-field outlined color="light-blue darken-3" class="teal-darken-4-input text-lg-h6" id="result" type="text" v-model="value" @keypress="isNumber($event)" placeholder="0"></v-text-field>
      <v-row v-if="inputType == 0" align="center" justify="space-around" class="my-5">
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('7')">7</v-btn>
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('8')">8</v-btn>
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('9')">9</v-btn>
        <v-btn color="pink darken-1 white--text" elevation="5" v-on:click="reset()">C</v-btn>
      </v-row>
      <v-row v-if="inputType == 0" align="center" justify="space-around" class="mb-5">
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('4')">4</v-btn>
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('5')">5</v-btn>
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('6')">6</v-btn>
        <v-btn color="light-blue darken-3 teal--text text--lighten-5" elevation="5" v-on:click="remember('add')"><v-icon>mdi-plus</v-icon></v-btn>
      </v-row>
      <v-row v-if="inputType == 0" align="center" justify="space-around" class="mb-5">
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('1')">1</v-btn>
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('2')">2</v-btn>
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('3')">3</v-btn>
        <v-btn color="light-blue darken-3 teal--text text--lighten-5" elevation="5" v-on:click="remember('subtract')"><v-icon>mdi-minus</v-icon></v-btn>
      </v-row>
      <v-row v-if="inputType == 0" align="center" justify="space-around" class="mb-5">
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('0')">0</v-btn>
        <v-btn color="light-blue lighten-4" elevation="5" v-on:click="write('.')">.</v-btn>
        <v-btn color="light-blue darken-3 teal--text text--lighten-5" elevation="5" v-on:click="remember('multiply')"><v-icon>mdi-multiplication</v-icon></v-btn>
        <v-btn color="light-blue darken-3 teal--text text--lighten-5" elevation="5" v-on:click="remember('divide')"><v-icon>mdi-division</v-icon></v-btn>
      </v-row>
      <v-row align="center" justify="space-around" class="mb-3">
        <v-btn color="teal darken-2 white--text" elevation="5" width="100%" max-width="385px" v-on:click="equals()"><v-icon>mdi-equal</v-icon></v-btn>
      </v-row>
      Тип ввода
      <v-radio-group v-model="inputType">
        <v-radio label="Стандартный" :value="0"></v-radio>
        <v-radio label="Формула" :value="1"></v-radio>
      </v-radio-group>
    </v-container>
    <v-card-text v-if="isFormulaError" class="red--text">Ошибка ввода</v-card-text>
  </v-card>
</template>
<script>
import AppVue from '../App.vue';
export default {
  data() {
    return {
      value: '',
      value1: 0,
      value2: 0,
      action: '',
      done: false,
      isEqualPressed: false,
      inputType: 0,
      isFormulaError: false,
    };
  },
  render: (createElement) => {
    const context = {
      props: { col: 'secondary' },
    };
    return createElement(AppVue, context);
  },
  methods: {
    write: function (number) {
      if (this.isEqualPressed) {
        this.value = '';
        this.isEqualPressed = false;
      }
      number == '.' ? (this.value += number) : this.value == '0' ? (this.value = number) : (this.value += number);
    },
    reset: function () {
      this.value = '0';
      this.value1 = 0;
      this.value2 = 0;
      this.action = '';
      this.done = false;
      this.isEqualPressed = false;
    },
    remember: function (act) {
      this.done = false;
      this.value1 = Number(this.value);
      this.value = '0';
      this.action = act;
    },
    equals: function () {
      try {
        if (this.inputType == 0) {
          if (!this.done) {
            this.value2 = Number(this.value);
          }

          switch (this.action) {
            case 'add':
              this.value1 = this.value = this.value1 + this.value2;
              this.done = true;
              break;
            case 'subtract':
              this.value1 = this.value = this.value1 - this.value2;
              this.done = true;
              break;
            case 'multiply':
              this.value1 = this.value = this.value1 * this.value2;
              this.done = true;
              break;
            case 'divide':
              this.value1 = this.value = this.value1 / this.value2;
              this.done = true;
              break;
          }
          this.isEqualPressed = true;
        } else {
          this.value = eval(this.value);
        }
      } catch {
        this.isFormulaError = true;
      }
    },
    isNumber: function (evt) {
      evt = evt ? evt : window.event;
      var charCode = evt.which ? evt.which : evt.keyCode;
      if (this.inputType == 0) {
        if (charCode > 31 && (charCode < 48 || charCode > 57) && charCode !== 46) {
          evt.preventDefault();
        } else {
          return true;
        }
      } else {
        if (charCode > 31 && (charCode < 48 || charCode > 57) && charCode !== 46 && charCode !== 42 && charCode !== 43 && charCode !== 45 && charCode !== 47) {
          evt.preventDefault();
        } else {
          return true;
        }
      }
    },
  },
  created() {
    window.addEventListener('keydown', (e) => {
      this.isFormulaError = false;
      if (this.inputType == 0) {
        switch (e.code) {
          case 'Numpad1':
          case 'Digit1':
            document.activeElement.blur();
            this.write('1');
            break;
          case 'Numpad2':
          case 'Digit2':
            document.activeElement.blur();
            this.write('2');
            break;
          case 'Numpad3':
          case 'Digit3':
            document.activeElement.blur();
            this.write('3');
            break;
          case 'Numpad4':
          case 'Digit4':
            document.activeElement.blur();
            this.write('4');
            break;
          case 'Numpad5':
          case 'Digit5':
            document.activeElement.blur();
            this.write('5');
            break;
          case 'Numpad6':
          case 'Digit6':
            document.activeElement.blur();
            this.write('6');
            break;
          case 'Numpad7':
          case 'Digit7':
            document.activeElement.blur();
            this.write('7');
            break;
          case 'Numpad8':
          case 'Digit8':
            document.activeElement.blur();
            this.write('8');
            break;
          case 'Numpad9':
          case 'Digit9':
            document.activeElement.blur();
            this.write('9');
            break;
          case 'Numpad0':
          case 'Digit0':
            document.activeElement.blur();
            this.write('0');
            break;
          case 'NumpadDecimal':
          case 'Period':
            this.write('.');
            break;
          case 'NumpadDivide':
          case 'Slash':
            this.remember('divide');
            break;
          case 'NumpadMultiply':
            this.remember('multiply');
            break;
          case 'NumpadSubtract':
          case 'Minus':
            this.remember('subtract');
            break;
          case 'NumpadAdd':
            this.remember('add');
            break;
          case 'NumpadEnter':
          case 'Enter':
          case 'Equal':
            this.equals();
            break;
          case 'Escape':
            this.reset();
            break;
          case 'Backspace':
            if (this.value != '0') {
              this.value = this.value.substr(0, this.value.length - 1);
            }

            break;
        }
      } else {
        switch (e.code) {
          case 'Numpad1':
          case 'Digit1':
            document.activeElement.blur();
            this.write('1');
            break;
          case 'Numpad2':
          case 'Digit2':
            document.activeElement.blur();
            this.write('2');
            break;
          case 'Numpad3':
          case 'Digit3':
            document.activeElement.blur();
            this.write('3');
            break;
          case 'Numpad4':
          case 'Digit4':
            document.activeElement.blur();
            this.write('4');
            break;
          case 'Numpad5':
          case 'Digit5':
            document.activeElement.blur();
            this.write('5');
            break;
          case 'Numpad6':
          case 'Digit6':
            document.activeElement.blur();
            this.write('6');
            break;
          case 'Numpad7':
          case 'Digit7':
            document.activeElement.blur();
            this.write('7');
            break;
          case 'Numpad8':
          case 'Digit8':
            document.activeElement.blur();
            this.write('8');
            break;
          case 'Numpad9':
          case 'Digit9':
            document.activeElement.blur();
            this.write('9');
            break;
          case 'Numpad0':
          case 'Digit0':
            document.activeElement.blur();
            this.write('0');
            break;
          case 'NumpadDecimal':
          case 'Period':
            this.write('.');
            break;
          case 'NumpadDivide':
          case 'Slash':
            this.write('/');
            break;
          case 'NumpadMultiply':
            this.write('*');
            break;
          case 'NumpadSubtract':
          case 'Minus':
            this.write('-');
            break;
          case 'NumpadAdd':
            this.write('+');
            break;
          case 'NumpadEnter':
          case 'Enter':
          case 'Equal':
            this.equals();
            break;
          case 'Escape':
            this.reset();
            break;
          case 'Backspace':
            if (this.value != '0') {
              this.value = this.value.substr(0, this.value.length - 1);
            }

            break;
        }
      }
    });
  },
  mounted() {},
};
</script>
