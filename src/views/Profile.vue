<template>
  <div class="pt-5">
    <div class="d-flex pt-5 justify-content-center align-items-center">
      <div class="col-4">
        <img v-if="branch_it" class="w-100" src="../assets/it.png" alt="img">
        <img v-if="branch_accounting" class="w-100" src="../assets/accounting.png" alt="img">
        <img v-if="branch_marketing" class="w-100" src="../assets/marketing.png" alt="img">
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
            <td class="font-weight-bold">Отдел</td>
            <td>{{branch}}</td>
          </tr>
          <tr>
            <td class="font-weight-bold">Должность</td>
            <td>{{profile.position}}</td>
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

export default {
  components: { Form },
  name: 'Profile',
  data () {
    return {
      profile: null,
      photo: '../assets/it.png',
      gender: 'Мужчина',
      branch: 'IT',
      branch_it: false,
      branch_accounting: false,
      branch_marketing: false,
      url: ''
    }
  },
  async created () {
    const url = `http://localhost:3000/staff/${this.$route.params.id}`
    console.log(url)
    this.url = url
    await axios.get(url).then(response => {
      this.profile = response.data
    })
    this.whatGender()
    this.whatBranch()
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
    whatBranch () {
      switch (this.profile.branch_id) {
        case 0:
          this.branch = 'IT'
          this.branch_it = true
          break
        case 1:
          this.branch = 'Бухгалтерия'
          this.branch_accounting = true
          break
        case 2:
          this.branch = 'Маркетинг'
          this.branch_marketing = true
          break
        default:
          this.branch = 'Нет информации'
      }
    },
    submit (data) {
      let genderText = ''
      let branchName = ''
      let updatedProfile = {}
      if (data.gender_id === 1) {
        genderText = 'Мужчина'
      } else if (data.gender_id === 0) {
        genderText = 'Женщина'
      }
      switch (Number(data.branch_id)) {
        case 0:
          branchName = 'ИТ'
          break
        case 1:
          branchName = 'Бухгалтерия'
          break
        case 2:
          branchName = 'Маркетинг'
          break
        default:
          branchName = 'Нет информации'
      }
      updatedProfile = {
        name: data.name,
        position: data.position,
        gender_id: data.gender_id,
        gender: genderText,
        branch_id: Number(data.branch_id),
        branch_name: branchName,
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
