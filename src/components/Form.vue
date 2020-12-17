<template>
  <div class="about">
    <form @submit.prevent="submitForm()">
      <div class="form-group" :class="{ 'form-group--error': $v.name.$error }">
        <label for="exampleInputname">ФИО</label>
        <input v-model.trim="$v.name.$model" type="text" class="form-control" id="exampleInputname" aria-describedby="nameHelp">
        <small id="nameHelp" v-if="!$v.name.required" class="form-text text-danger">Это обязательное поля</small>
      </div>
      <div class="form-group">
        <label for="exampleInputSelect">Отдел</label>
        <select class="custom-select" v-model="branch_id">
          <option :selected="branch_it" value="0">ИТ</option>
          <option :selected="branch_accounting" value="1">Бухгалтерия</option>
          <option :selected="branch_marketing" value="2">Маркетинг</option>
        </select>
      </div>
      <div class="form-group">
        <label for="exampleInputPosition">Должность</label>
        <input v-model.trim="$v.position.$model" type="text" class="form-control" id="exampleInputPosition">
        <small id="positionHelp" v-if="!$v.position.required" class="form-text text-danger">Это обязательное поля</small>
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
    this.whatBranch()
  },
  props: {
    formData: VueTypes.shape({
      id: VueTypes.number,
      name: VueTypes.string,
      position: VueTypes.string,
      branch_id: VueTypes.number,
      branch_name: VueTypes.string,
      gender_id: VueTypes.number,
      gender: VueTypes.string,
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
      branch_it: false,
      branch_accounting: false,
      branch_marketing: false,
      name: this.formData.name,
      position: this.formData.position,
      gender_id: this.formData.gender_id,
      branch_id: this.formData.branch_id,
      branch_name: this.formData.branch_name,
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
    whatBranch () {
      switch (this.formData.branch_id) {
        case 0:
          this.branch_it = true
          break
        case 1:
          this.branch_accounting = true
          break
        case 2:
          this.branch_marketing = true
          break
        default:
          this.branch_it = this.branch_accounting = this.branch_marketing = false
      }
    },
    submitForm () {
      console.log('submit!')
      this.$v.$touch()
      if (!this.$v.$invalid) {
        // console.log(this.name)
        // console.log(this.position)
        // console.log(this.gender_id)
        // console.log(this.branch_id)
        // console.log(this.date)
        const submittedData = {
          name: this.name,
          position: this.position,
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
    position: {
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
