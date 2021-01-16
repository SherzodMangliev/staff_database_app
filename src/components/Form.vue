<template>
  <div class="about">
    <form @submit.prevent="submitForm()">
      <div class="form-group" :class="{ 'form-group--error': $v.name.$error }">
        <label for="exampleInputname">ФИО</label>
        <input v-model.trim="$v.name.$model" type="text" class="form-control" id="exampleInputname" aria-describedby="nameHelp">
        <small id="nameHelp" v-if="!$v.name.required" class="form-text text-danger">Это обязательное поля</small>
      </div>
      <div class="form-group">
        <label for="exampleInputSelect">Факултет</label>
        <select class="custom-select" v-model="branch_id">
          <option :selected="formData.branch_id === 0" value="0">ИТ</option>
          <option :selected="formData.branch_id === 1" value="1">Бухгалтерия</option>
          <option :selected="formData.branch_id === 2" value="2">Маркетинг</option>
        </select>
      </div>
      <div class="form-group">
        <label for="exampleInputPosition">Курс</label>
        <select class="custom-select" v-model="level">
          <option :selected="formData.level === 1" value="1">1</option>
          <option :selected="formData.level === 2" value="2">2</option>
          <option :selected="formData.level === 3" value="3">3</option>
          <option :selected="formData.level === 4" value="4">4</option>
        </select>
      </div>
      <div class="form-group">
        <label for="exampleInputRadio">Выберите свой пол</label>
        <div id="exampleInputRadio">
          <div class="custom-control custom-radio custom-control-inline">
            <input :checked="gender" v-model="gender_id" value="1" type="radio" id="customRadioInline1" name="customRadioInline1" class="custom-control-input">
            <label class="custom-control-label" for="customRadioInline1">Мужчина</label>
          </div>
          <div class="custom-control custom-radio custom-control-inline">
            <input :checked="!gender" v-model="gender_id" value="0" type="radio" id="customRadioInline2" name="customRadioInline1" class="custom-control-input">
            <label class="custom-control-label" for="customRadioInline2">Женщина</label>
          </div>
        </div>
      </div>
      <div class="form-group">
        <label for="exampleInputDate">
          Дата рождения
        </label>
        <datepicker
          :bootstrap-styling="true"
          :value="date"
          :calendar-button="true"
          name="birthdate"
          :language="ru"
          format="yyyy MMMM dd"
          :typeable="false"
          v-model="date"
          >
        </datepicker>
        <small id="positionHelp" v-if="!$v.date.required" class="form-text text-danger">Это обязательное поля</small>
      </div>
      <hr />
      <div class="form-group">
        <button aria-label="Close" class="btn btn-primary" type="submit">Сохранить</button>
      </div>
    </form>
  </div>
</template>

<script>
import VueTypes from 'vue-types'
import Datepicker from 'vuejs-datepicker'
import { en, ru } from 'vuejs-datepicker/dist/locale'
import { required } from 'vuelidate/lib/validators'

export default {
  created () {
    this.whatGender()
    console.log(this.formData)
  },
  props: {
    formData: VueTypes.shape({
      id: VueTypes.number,
      name: VueTypes.string.def(''),
      position: VueTypes.string.def(''),
      branch_id: VueTypes.number,
      gender_id: VueTypes.number,
      level: VueTypes.number,
      date: VueTypes.string
    }),
    url: VueTypes.string
  },
  components: {
    Datepicker
  },
  data () {
    return {
      en: en,
      ru: ru,
      gender: true,
      name: this.formData.name,
      level: this.formData.level,
      gender_id: this.formData.gender_id,
      branch_id: this.formData.branch_id,
      date: this.formData.date
    }
  },
  methods: {
    whatGender () {
      if (this.formData.gender_id === 0) {
        this.gender = false
      } else if (this.formData.gender_id === 1) {
        this.gender = true
      }
    },
    submitForm () {
      console.log('submit!')
      this.$v.$touch()
      if (!this.$v.$invalid) {
        // console.log(this.name)
        console.log(this.level)
        // console.log(this.gender_id)
        // console.log(this.branch_id)
        // console.log(this.date)
        const submittedData = {
          name: this.name,
          level: this.level,
          gender_id: this.gender_id,
          branch_id: this.branch_id,
          date: this.date
        }
        // console.log(submittedData)
        this.$emit('submit', submittedData)
      }
    }
  },
  validations: {
    name: {
      required
    },
    date: {
      required
    }
  }
}
</script>

<style>

</style>
