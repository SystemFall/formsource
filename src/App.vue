<template>
  <div id="wrapper">
    <form>
      <h1>Регистрация клиента</h1>
      <h2>Информация о клиенте</h2>
      <hr />
      <div class="block">
        <v-input label="Фамилия" :v="$v.surname">
          <input class="text" type="text" v-model.trim="surname" @blur="$v.surname.$touch" />
        </v-input>

        <v-input label="Имя" :v="$v.name">
          <input class="text" type="text" v-model.trim="name" @blur="$v.name.$touch" />
        </v-input>

        <v-input label="Отчество" :v="$v.patronymic">
          <input class="text" type="text" v-model.trim="patronymic" @blur="$v.patronymic.$touch" />
        </v-input>

        <v-input label="Дата рождения" :v="$v.birthday">
          <input class="text" type="text" v-model.trim="birthday" @blur="$v.birthday.$touch" />
        </v-input>

        <v-input label="Номер телефона" :v="$v.phone">
          <input class="text" type="text" v-model.trim="phone" @blur="$v.phone.$touch" />
        </v-input>

        <div class="input-field">
          <p for>Пол</p>
          <div class="radio">
            <span>
              <input name="gender" type="radio" />Муж.
            </span>
            <span>
              <input name="gender" type="radio" />Жен.
            </span>
          </div>
        </div>

        <v-input
          label="Группа клиентов"
          v-model="client_group_data"
          :v="$v.client_group_data"
          :class="{ error: ($v.client_group_data.$dirty && ($v.client_group_data.$model == [] || $v.client_group_data.$model.id == null)), required: $v.client_group_data.$params.id }"
        >
          <select
            multiple
            class="text"
            v-model="client_group_data"
            @blur="$v.client_group_data.$touch"
            @change="$v.client_group_data.$touch"
          >
            <option :key="c.id" v-for="c in client_group" :value="c">{{c.name}}</option>
          </select>
          <div
            class="error_message"
            v-if="$v.client_group_data.$model.length == 0"
          >Это поле обязательное</div>
        </v-input>

        <v-input label="Лечащий врач" :v="$v.doctor">
          <select class="text" @change="$v.doctor.$touch" @blur="$v.doctor.$touch">
            <option :key="0">Выберите врача</option>
            <option :key="d.id" :value="d" v-for="d in doctor">{{d.name}}</option>
          </select>
        </v-input>

        <div class="input-field sms">
          <div class="check">
            <input type="checkbox" />
            <span>Не отправлять СМС</span>
          </div>
        </div>
      </div>
      <h2>Адрес</h2>
      <hr />
      <div class="block">
        <v-input label="Индекс" :v="$v.index">
          <input class="text" type="text" v-model.trim="index" @blur="$v.index.$touch" />
        </v-input>

        <v-input label="Страна" :v="$v.country">
          <input class="text" type="text" v-model.trim="country" @blur="$v.country.$touch" />
        </v-input>

        <v-input label="Область" :v="$v.region">
          <input class="text" type="text" v-model.trim="region" @blur="$v.region.$touch" />
        </v-input>

        <v-input label="Город" :v="$v.city">
          <input class="text" type="text" v-model.trim="city" @blur="$v.city.$touch" />
        </v-input>

        <v-input label="Улица" :v="$v.street">
          <input class="text" type="text" v-model.trim="street" @blur="$v.street.$touch" />
        </v-input>

        <v-input label="Дом" :v="$v.house">
          <input class="text" type="text" v-model.trim="house" @blur="$v.house.$touch" />
        </v-input>
      </div>
      <h2>Паспорт</h2>
      <hr />
      <div class="block">
        <v-input
          label="Тип документа"
          v-model="type_data"
          :v="$v.type_data"
          :class="{ error: ($v.type_data.$dirty && ($v.type_data.$model == [] || $v.type_data.$model.id == null)), required: $v.type_data.$params.id }"
        >
          <select
            class="text"
            v-model="type_data"
            @change="$v.type_data.$touch"
            @blur="$v.type_data.$touch"
          >
            <option :key="t.id" :value="t" v-for="t in type">{{t.name}}</option>
          </select>
          <div class="error_message" v-if="$v.type_data.$error">Это поле обязательное</div>
        </v-input>

        <v-input label="Серия" :v="$v.series">
          <input class="text" type="text" v-model.trim="series" @blur="$v.series.$touch" />
        </v-input>

        <v-input label="Номер" :v="$v.number">
          <input class="text" type="text" v-model.trim="number" @blur="$v.number.$touch" />
        </v-input>

        <v-input label="Кем выдан" :v="$v.issued_by">
          <input class="text" type="text" v-model.trim="issued_by" @blur="$v.issued_by.$touch" />
        </v-input>

        <v-input label="Дата выдачи" :v="$v.date">
          <input class="text" type="text" v-model.trim="date" @blur="$v.date.$touch" />
        </v-input>
      </div>
      <hr class="invisible" />
      <button type="button" @click="submit()">Зарегистрироваться</button>
    </form>
  </div>
</template>

<script>
/* eslint-disable */

import Vue from "vue";
import Vuelidate from "vuelidate";
import { required, minLength, helpers } from "vuelidate/lib/validators";

Vue.use(Vuelidate);

const alpha = helpers.regex("alpha", /^[а-яА-Яa-zA-Z]+$/);
const numb = helpers.regex("numb", /^[0-9]+$/);
const date = helpers.regex(
  "date",
  /^([1-9]|[1-2][\d]|3[0-1])\-([1-9]|1[0-2])\-(\d{4})$/
);
const totalCharacter = helpers.regex("totalCharacter", /^\d{11}$/);
const phoneFirstChar = helpers.regex("phoneFirstChar", /^[7]/);
const passportSeries = helpers.regex("passportSeries", /^\d{2}\s\d{2}$/);
const passportNumber = helpers.regex("passportNumber", /^\d{6}$/);

export default {
  data() {
    return {
      name: "",
      surname: "",
      patronymic: "",
      birthday: "",
      phone: "",
      client_group: [
        {
          id: 1,
          name: "VIP",
        },
        {
          id: 2,
          name: "Проблемные",
        },
        {
          id: 3,
          name: "ОМС",
        },
      ],
      client_group_data: [{ id: null }, { name: null }],
      doctor: [
        {
          id: 1,
          name: "Иванов",
        },
        {
          id: 2,
          name: "Захаров",
        },
        {
          id: 3,
          name: "Чернышева",
        },
      ],
      type: [
        {
          id: 1,
          name: "Паспорт",
        },
        {
          id: 2,
          name: "Свидетельство о рождении",
        },
        {
          id: 3,
          name: "Вод. удостоверение",
        },
      ],
      type_data: {
        id: null,
        name: null,
      },
      gender: "",
      sms: "",
      index: "",
      country: "",
      region: "",
      city: "",
      street: "",
      house: "",
      series: "",
      number: "",
      issued_by: "",
      date: "",
    };
  },
  validations: {
    name: {
      required,
      minLength: minLength(2),
      alpha,
    },
    surname: {
      required,
      minLength: minLength(2),
      alpha,
    },
    patronymic: {
      minLength: minLength(2),
      alpha,
    },
    birthday: {
      required,
      date,
    },
    phone: {
      required,
      phoneFirstChar,
      totalCharacter,
    },
    client_group_data: {
      id: required,
      name: required,
    },
    doctor: {},
    index: {
      numb,
    },
    country: {
      alpha,
    },
    region: {
      alpha,
    },
    city: {
      required,
      alpha,
    },
    street: {},
    house: {},
    type_data: {
      id: required,
      name: required,
    },
    series: {
      passportSeries,
    },
    number: {
      passportNumber,
    },
    issued_by: {},
    date: {
      required,
      date,
    },
  },
  methods: {
    submit() {
      var check1 = true;
      var check2 = true;
      try {
        check1 = this.$v.client_group_data.$model[0].id == null;
      } catch {
        check1 = false;
      }
      try {
        check2 = this.$v.type_data.$model.id == null;
      } catch {
        check2 = false;
      }
      if (
        this.$v.$invalid ||
        this.$v.client_group_data.$model.length == 0 ||
        check1 ||
        check2
      ) {
        this.$v.$touch();
        return;
      }
      alert("Вы успешно зарегистрированы");
    },
  },
};
Vue.component("v-input", {
  template:
    "<div class='input-field' :class='{ error: v.$error, required: v.$params.required }'><p><label>{{label}}</label></p><slot></slot><p class='error_message'>{{message}}</p></div>",
  props: ["label", "v"],
  computed: {
    message() {
      if (this.v.$error) {
        for (let key in this.$options.messages) {
          if (this.v[key] === false) {
            return this.$options.messages[key](this.v);
          }
        }
      } else {
        return null;
      }
    },
    model: {
      get() {
        return this.value;
      },
      set(val) {
        this.$emit("input", val);
      },
    },
  },
  messages: {
    required: (v) => "Это поле обязательное",
    minLength: (v) => "Минимум " + v.$params.minLength.min + " символа",
    alpha: (v) => "Должен содержать только буквы",
    numb: (v) => "Должен содержать только цифры",
    date: (v) => "Формат записи: 31-12-2020",
    totalCharacter: (v) => "Должен содержать 11 цифр",
    phoneFirstChar: (v) => "Должен начинаться с цифры '7'",
    passportSeries: (v) => "Неверный формат: XX XX",
    passportNumber: (v) => "Неверный формат: YYYYYY",
  },
});
</script>
<style>
@import "assets/css/style.css";
</style>