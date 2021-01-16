<template>
  <div class="pt-5">
    <div class="d-flex pt-5 justify-content-center align-items-center">
      <div class="col-4">
        <img v-if="profile.branch_id === 0" class="w-100" src="../assets/it.png" alt="img">
        <img v-if="profile.branch_id === 1" class="w-100" src="../assets/accounting.png" alt="img">
        <img v-if="profile.branch_id === 2" class="w-100" src="../assets/marketing.png" alt="img">
      </div>
      <div class="col-8">
        <div class="mb-2">
          <button class="btn btn-warning text-white" data-toggle="modal" data-target="#formModal">
            Изменить данные <i class="fas fa-edit"></i>
          </button>
          <div class="modal fade" id="formModal" tabindex="-1" aria-labelledby="formModal"
            aria-hidden="true">
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title" id="exampleModalLabel">Изменение данных</h5>
                  <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                  </button>
                </div>
                <div class="modal-body">
                  <Form :formData="profile" @submit="submit" />
                </div>
              </div>
            </div>
          </div>
        </div>
        <table class="table table-striped table-hovered table-bordered">
          <tr>
            <td class="font-weight-bold">ФИО</td>
            <td>{{profile.name}}</td>
          </tr>
          <tr>
            <td class="font-weight-bold">Факултет</td>
            <td v-if="profile.branch_id === 0">ИТ</td>
            <td v-if="profile.branch_id === 1">Бухгалтерия</td>
            <td v-if="profile.branch_id === 2">Маркетинг</td>
          </tr>
          <tr>
            <td class="font-weight-bold">Курс</td>
            <td>{{profile.level}}</td>
          </tr>
          <tr>
            <td class="font-weight-bold">Пол</td>
            <td>{{gender}}</td>
          </tr>
          <tr>
            <td class="font-weight-bold">Дата рождения</td>
            <td>{{profile.date}}</td>
          </tr>
        </table>
      </div>
    </div>
  </div>

</template>

<script>
import axios from 'axios'
import Form from '../components/Form.vue'
import moment from 'moment'

export default {
  components: { Form },
  name: 'Profile',
  data () {
    return {
      profile: null,
      photo: '../assets/it.png',
      gender: 'Мужчина',
      branch: 'IT',
      url: ''
    }
  },
  async created () {
    const url = `http://localhost:3000/students/${this.$route.params.id}`
    console.log(url)
    this.url = url
    await axios.get(url).then(response => {
      this.profile = response.data
    })
    this.whatGender()
  },
  methods: {
    whatGender () {
      if (this.profile.gender_id === 0) {
        this.gender = 'Женщина'
      } else if (this.profile.gender_id === 1) {
        this.gender = 'Мужчина'
      } else {
        this.gender = 'Нет информации'
      }
    },
    dateFormatter (date) {
      return moment(date).format('yyyy/MM/DD')
    },
    submit (data) {
      let updatedProfile = {}
      updatedProfile = {
        name: data.name,
        level: data.level,
        gender_id: data.gender_id,
        branch_id: Number(data.branch_id),
        date: data.date
      }
      axios.put(this.url, updatedProfile).then(response => {
        console.log(response.data)
      }).catch(error => {
        console.log(error)
      })
      console.log(updatedProfile)
      window.location.reload()
    }
  }
}
</script>

<style>

</style>
