<template>
  <form action="" method="post" id="reg-form" class="reg-form">
    <h3>Реєстрація</h3>
    <label>
      <input type="text"
             placeholder="Прізвище"
             name="surname"
             id="surname"
             v-model="surname"/>
    </label>
    <span class="error-message" v-show="errors['surname']" v-text="errors['surname']"></span>
    <label>
      <input type="text"
             placeholder="Ім'я"
             name="name"
             id="name"
             v-model="name"/>
    </label>
    <span class="error-message" v-show="errors['name']" v-text="errors['name']"></span>
    <label>
      <input type="text"
             placeholder="По-батькові"
             name="last-name"
             id="last-name"
             v-model="lastName"/>
    </label>
    <span class="error-message" v-show="errors['last-name']" v-text="errors['last-name']"></span>
    <div class="gender">
      <label class="label-gender">
        <input type="radio"
               placeholder="Стать"
               name="gender"
               value="Male"
               id="male"
               required
               v-model="gender"/>Чоловік
      </label>
      <label class="label-gender">
        <input type="radio"
               placeholder="Стать"
               name="gender"
               value="Female"
               id="female"
               v-model="gender"/>Жінка
      </label>
    </div>
    <span class="error-message" v-show="errors['gender']" v-text="errors['gender']"></span>
    <label>Дата народження:<br>
      <input type="date"
             name="bday"
             id="date"
             v-model="date"
             @change="validateDate()"/>
    </label>
    <span class="error-message" v-show="errors['date']" v-text="errors['date']"></span>
    <label>
      <v-select :options="groups"
                v-model="group"/>
    </label>
    <span class="error-message" v-show="errors['group']" v-text="errors['group']"></span>
    <label>
      <input type="email"
             placeholder="Email"
             name="email"
             required
             v-model="email"
             @change="validateEmail()"
             id="email"/>
    </label>
    <span class="error-message" v-show="errors['email']" v-text="errors['email']"></span>
    <label>Телефон:<br>
      <input type="tel"
             required
             name="tel"
             id="tel"
             v-mask="'+38(0##)###-##-##'"
             v-model="phone"
             @change="validatePhone()"/>
    </label>
    <span class="error-message" v-show="errors['tel']" v-text="errors['tel']"></span>
    <label>
      <input type="password"
             placeholder="Пароль"
             name="password"
             id="password"
             v-model="password"/>
    </label>
    <label>
      <input type="password"
             placeholder="Підтвердити пароль"
             name="confirm-password"
             id="confirm-password"
             v-model="confirmPassword"/>
    </label>
    <span class="error-message" v-show="errors['password']" v-text="errors['password']"></span>
    <input type="button"
           value="Створити акаунт"
           @click="processForm()"/>
  </form>
  <p>Вже маєте обліковий запис? <a href="">Увійти.</a></p>
  <form id="delete-duplicate-form">
    <table id="table">
      <thead>
      <tr>
        <th>Прізвище</th>
        <th>Ім'я</th>
        <th>По-батькові</th>
        <th>Стать</th>
        <th>Дата народження</th>
        <th>Група</th>
        <th>Email</th>
        <th>Телефон</th>
        <th>Пароль</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(user) in users">
        <td v-for="(value) in user">{{ value }}</td>
        <td><input type="checkbox" name="del-dupl-checkbox"></td>
      </tr>
      </tbody>
    </table>
    <div class="buttons">
      <input type="button"
             value="Delete"
             @click="deleteRows()"/>
      <input type="button"
             value="Duplicate"
             @click="duplicateRows()"/>
    </div>
  </form>
</template>

<script>
import {defineComponent} from 'vue'

import vSelect from 'vue-select';
import 'vue-select/dist/vue-select.css'
import {mask} from 'vue-the-mask'


export default defineComponent({
  props: {},
  components: {
    vSelect,
  },
  directives: {
    mask
  },
  data: () => ({
    errors: [],
    name: '',
    surname: '',
    lastName: '',
    gender: '',
    date: '',
    group: '',
    email: '',
    phone: '+38(0',
    password: '',
    confirmPassword: '',
    groups: [
      'ІА-01',
      'ІА-02',
      'ІА-03',
      'ІА-04',
      'ІА-11',
      'ІА-12',
      'ІА-13',
      'ІА-14',
      'ІА-21',
      'ІА-22',
      'ІА-23',
      'ІА-24',
    ],
    users: []
  }),
  watch: {
    group(newValue) {
      if (newValue !== null && newValue !== '')
        this.errors['group'] = ''
    },
    surname(newValue) {
      if (newValue.toLowerCase().match(/[^абвгґдеєжзиіїйклмнопрстуфхцчшщьюя-]/))
        this.errors['surname'] = 'Дозволені тільки українські символи та знак -.'
      else if (!ALLOWED_SYMBOLS.toUpperCase().includes(newValue.charAt(0)))
        this.errors['surname'] = 'Перша буква повинна бути великою.'
      else
        this.errors['surname'] = ''
    },
    name(newValue) {
      if (newValue.toLowerCase().match(/[^абвгґдеєжзиіїйклмнопрстуфхцчшщьюя-]/))
        this.errors['name'] = 'Дозволені тільки українські символи та знак -.'
      else if (!ALLOWED_SYMBOLS.toUpperCase().includes(newValue.charAt(0)))
        this.errors['name'] = 'Перша буква повинна бути великою.'
      else
        this.errors['name'] = ''
    },
    lastName(newValue) {
      if (newValue.toLowerCase().match(/[^абвгґдеєжзиіїйклмнопрстуфхцчшщьюя-]/))
        this.errors['last-name'] = 'Дозволені тільки українські символи та знак -.'
      else if (!ALLOWED_SYMBOLS.toUpperCase().includes(newValue.charAt(0)))
        this.errors['last-name'] = 'Перша буква повинна бути великою.'
      else
        this.errors['last-name'] = ''
    },
    gender(newValue) {
      if (newValue !== null && newValue !== '')
        this.errors['gender'] = ''
    }
  },
  methods: {
    validateSurname() {
      if (this.surname === null || this.surname === '')
        this.errors['surname'] = 'Введіть прізвище.'
      return !this.errors['surname']
    },
    validateName() {
      if (this.name === null || this.name === '')
        this.errors['name'] = 'Введіть ім\'я.'
      return !this.errors['name']
    },
    validateLastName() {
      if (this.lastName === null || this.lastName === '')
        this.errors['last-name'] = 'Введіть по-батькові.'
      return !this.errors['last-name']
    },
    validateGender() {
      if (this.gender === null || this.gender === '')
        this.errors['gender'] = 'Оберіть стать.'
      return !this.errors['gender']
    },
    validateDate() {
      if (this.date === '' || this.date === null)
        this.errors['date'] = 'Введіть дату.'
      else if (new Date(this.date) > new Date())
        this.errors['date'] = 'Дата не повинна бути майбутня.'
      else
        this.errors['date'] = ''
      return !this.errors['date']
    },
    validateGroup() {
      if (this.group === null || this.group === '')
        this.errors['group'] = 'Оберіть групу.'
      return !this.errors['group']
    },
    validateEmail() {
      if (this.email.toLowerCase().match(
          /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
      ))
        this.errors['email'] = ''
      else
        this.errors['email'] = 'Некоректна адреса електронної пошти.'
      return !this.errors['email']
    },
    validatePhone() {
      if (this.phone.length < 17)
        this.errors['tel'] = 'Заповніть телефон.'
      else
        this.errors['tel'] = ''
      return !this.errors['tel']
    },
    validatePassword() {
      if (this.password !== this.confirmPassword)
        this.errors['password'] = 'Паролі не збігаються'
      else
        this.errors['password'] = ''
      return !this.errors['password']
    },
    processForm() {
      if (this.validateForm()) {
        this.fillTable()
        this.flushForm()
      }
    },
    validateForm() {
      return this.validateSurname() & this.validateName() & this.validateLastName() & this.validateGender()
          & this.validateDate() & this.validateGroup() & this.validateEmail() & this.validatePhone() & this.validatePassword()
    },
    fillTable() {
      this.users.push({
        surname: this.surname,
        name: this.name,
        lastName: this.lastName,
        gender: this.gender,
        date: this.date,
        group: this.group,
        email: this.email,
        phone: this.phone,
        password: this.password
      })
    },
    flushForm() {
      this.errors = []
      this.name = ''
      this.surname = ''
      this.lastName = ''
      this.gender = ''
      this.date = ''
      this.group = ''
      this.email = ''
      this.phone = '+38(0'
      this.password = ''
      this.confirmPassword = ''
    },
    duplicateRows() {
      let rows = document.getElementById('table').rows
      const length = rows.length
      for (let i = 1; i < length; i++) {
        if (rows[i].lastChild.lastChild.checked) {
          rows[i].lastChild.lastChild.checked = false
          this.users.push(this.users[i - 1])
        }
      }
    },
    deleteRows() {
      let table = document.getElementById('table')
      let rows = table.rows
      for (let i = rows.length - 1; i >= 1; i--) {
        if (rows[i].lastChild.lastChild.checked) {
          rows[i].lastChild.lastChild.checked = false
          this.users.splice(i - 1, 1)
        }
      }
    }
  },
})
const ALLOWED_SYMBOLS = 'абвгґдеєжзиіїйклмнопрстуфхцчшщьюя'
</script>