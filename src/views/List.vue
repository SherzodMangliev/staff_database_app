<template>
  <div class="pt-5">
    <h1 class="pt-3 px-3">Список всех сотрудников</h1>
    <div class="gender-sort d-flex align-items-center px-3">
      <h5 class="mr-5 ">Сортировать по полу:</h5>
      <div class="d-flex ml-4 pl-2 mr-5">
        <toggle-button class="mb-0" color="#82C7EB" :sync="true" v-model="maleGenderToggle" @change="onChangeMale"/>
        <p class="mb-0">Показать только мужчины</p>
      </div>
      <div class="d-flex ml-4">
        <toggle-button class="mb-0" color="#fa8cab" :sync="true" v-model="femaleGenderToggle" @change="onChangeFemale"/>
        <p class="mb-0">Показать только женщины</p>
      </div>
    </div>
    <div class="branch-sort d-flex align-items-center px-3">
      <h5 class="mr-4">Сортировать по факултету:</h5>
      <div class="d-flex ml-1 mr-5">
        <toggle-button class="mb-0" color="#82C7EB" @change="onChangeBranchIt"/>
        <p class="mb-0">Показать факултет ИТ</p>
      </div>
      <div class="d-flex mx-5">
        <toggle-button class="mb-0 ml-1" color="#82C7EB" @change="onChangeBranchAccounting"/>
        <p class="mb-0">Показать факултет бухгалтерия</p>
      </div>
      <div class="d-flex mr-5">
        <toggle-button class="mb-0" color="#82C7EB" @change="onChangeBranchMarketing"/>
        <p class="mb-0">Показать факултет маркетинг</p>
      </div>
    </div>
    <div class="px-3 mb-2">
      <button class="btn btn-success" data-toggle="modal" data-target="#formModal">Добавить новый студент</button>
      <div class="modal fade" id="formModal" tabindex="-1" aria-labelledby="formModal" aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">Добаление нового студента</h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              <Form :formData="newStudent" @submit="addNewStudent" />
            </div>
          </div>
        </div>
      </div>
    </div>
    <table class="table table-striped table-hover px-2">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">ФИО</th>
          <th scope="col">Факултет</th>
          <th scope="col">Курс</th>
          <th scope="col">Пол</th>
          <th scope="col">Дата рождения</th>
          <th scope="col">Действия</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="profile in displayedProfiles" :key="profile.id">
          <th scope="row">{{(displayedProfiles.indexOf(profile)) + 1}}</th>
          <td>{{profile.name}}</td>
          <td v-if="profile.branch_id === 0">ИТ</td>
          <td v-if="profile.branch_id === 1">Бухгалтерия</td>
          <td v-if="profile.branch_id === 2">Маркетинг</td>
          <td>{{profile.level}}</td>
          <td v-if="profile.gender_id === 1">Мужчина</td>
          <td v-if="profile.gender_id === 0">Женщина</td>
          <td>{{dateFormatter(profile.date)}}</td>
          <td>
            <router-link :to="{ name: 'profile', params: { id: profile.id }}">
              <button class="btn-primary btn"><i class="fas fa-eye"></i></button>
            </router-link>
            <button class="btn btn-danger mx-2" @click="deleteProfile(profile.id)">
              <i class="fas fa-trash"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li v-if="this.page > 1" class="page-item">
          <button @click="previousPage()" class="page-link" href="#">Previous</button>
        </li>
        <li v-for="page in pages" :key="page" class="page-item">
          <button @click="currentPage(page)" class="page-link">{{page}}</button>
        </li>
        <li v-if="this.page < pages" class="page-item">
          <button @click="nextPage()" class="page-link" href="#">Next</button>
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import axios from 'axios'
import { ToggleButton } from 'vue-js-toggle-button'
import Form from '../components/Form.vue'
import moment from 'moment'
export default {
  name: 'List',
  components: {
    ToggleButton,
    Form
  },
  data () {
    return {
      profiles: [],
      pages: [],
      page: 1,
      gender: true,
      perPage: 10,
      startPoint: 0,
      femaleGenderToggle: false,
      maleGenderToggle: false,
      urlQuery: '',
      newStudent: {
        id: null,
        name: '',
        level: null,
        branch_id: null,
        branch_name: '',
        gender_id: null,
        date: ''
      }
    }
  },
  created () {
    this.getProfiles()
    console.log(this.profiles)
    console.log(this.pages.length)
    this.newStudent.id = this.profiles[0].id
    for (let i = 0; i <= this.profiles.length; i++) {
      if (this.profiles[i].id > this.newStudent.id) {
        this.newStudent.id = this.profiles[i].id
      }
    }
  },
  computed: {
    ...mapGetters(['returnData', 'returnFilteredProfiles']),
    displayedProfiles () {
      return this.pagination(this.profiles)
    }
  },

  methods: {
    ...mapActions(['fetchProfiles', 'fetchFilteredProfiles']),
    pagination (profiles) {
      const startPoint = (this.page * this.perPage) - this.perPage
      this.startPoint = startPoint
      const endPoint = startPoint + this.perPage
      return profiles.slice(startPoint, endPoint)
    },
    async getProfiles () {
      await this.fetchProfiles()
      this.profiles = this.returnData
      this.newStudent.id = this.profiles.length
      this.pages = this.numberOfPages(this.returnData)
    },
    currentPage (pageNumber) {
      this.page = pageNumber
      console.log(this.page)
    },
    previousPage () {
      if (this.page > 1) {
        this.page = this.page - 1
      }
    },
    nextPage () {
      this.page = this.page + 1
    },
    deleteProfile (id) {
      const url = `http://localhost:3000/students/${id}`
      console.log(url)
      axios.delete(url).then(response => {
        console.log(response)
        this.getProfiles()
      }).catch(error => {
        console.log(error)
      })
    },
    numberOfPages (profiles) {
      const pages = Math.ceil(profiles.length / this.perPage)
      return pages
    },
    async onChangeMale (value) {
      if (value.value === true) {
        const newUrlQuery = this.urlQuery.replace('&gender_id=0', '')
        // console.log(newUrlQuery)
        this.urlQuery = newUrlQuery
        // console.log(this.urlQuery)
        const url = this.urlQuery.concat('&gender_id=1')
        this.urlQuery = url
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
        this.maleGenderToggle = true
        this.femaleGenderToggle = false
        console.log(this.profiles)
        console.log(this.pages)
      } else {
        const newUrlQuery = this.urlQuery.replace('&gender_id=1', '')
        // console.log(newUrlQuery)
        this.urlQuery = newUrlQuery
        // console.log(this.urlQuery)
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
        this.maleGenderToggle = false
      }
    },
    async onChangeFemale (value) {
      if (value.value === true) {
        const newUrlQuery = this.urlQuery.replace('&gender_id=1', '')
        // console.log(newUrlQuery)
        this.urlQuery = newUrlQuery
        // console.log(this.urlQuery)
        const url = this.urlQuery.concat('&gender_id=0')
        this.urlQuery = url
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
        this.femaleGenderToggle = true
        this.maleGenderToggle = false
        console.log(this.profiles)
        console.log(this.pages)
      } else {
        const newUrlQuery = this.urlQuery.replace('&gender_id=0', '')
        // console.log(newUrlQuery)
        this.urlQuery = newUrlQuery
        // console.log(this.urlQuery)
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
        this.femaleGenderToggle = false
      }
    },
    async onChangeBranchIt (value) {
      if (value.value === true) {
        const url = this.urlQuery.concat('&branch_id=0')
        this.urlQuery = url
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
        console.log(this.profiles)
        console.log(this.pages)
      } else {
        const newUrlQuery = this.urlQuery.replace('&branch_id=0', '')
        // console.log(newUrlQuery)
        this.urlQuery = newUrlQuery
        // console.log(this.urlQuery)
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
      }
    },
    async onChangeBranchAccounting (value) {
      if (value.value === true) {
        const url = this.urlQuery.concat('&branch_id=1')
        this.urlQuery = url
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
      } else {
        const newUrlQuery = this.urlQuery.replace('&branch_id=1', '')
        // console.log(newUrlQuery)
        this.urlQuery = newUrlQuery
        // console.log(this.urlQuery)
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
      }
    },
    async onChangeBranchMarketing (value) {
      if (value.value === true) {
        const url = this.urlQuery.concat('&branch_id=2')
        this.urlQuery = url
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
      } else {
        const newUrlQuery = this.urlQuery.replace('&branch_id=2', '')
        // console.log(newUrlQuery)
        this.urlQuery = newUrlQuery
        // console.log(this.urlQuery)
        await this.fetchFilteredProfiles(this.urlQuery)
        this.pages = this.numberOfPages(this.returnFilteredProfiles)
        this.profiles = this.returnFilteredProfiles
      }
    },
    dateFormatter (date) {
      return moment(date).format('yyyy/MM/DD')
    },
    async addNewStudent (data) {
      console.log(data)
      console.log(this.dateFormatter(data.date))
      const newStudent = {
        id: this.id,
        name: data.name,
        branch_id: Number(data.branch_id),
        level: Number(data.level),
        gender_id: Number(data.gender_id),
        date: this.dateFormatter(data.date)
      }
      await axios.post('http://localhost:3000/students', newStudent).then(response => {
        console.log(response.data)
      }).catch(error => {
        console.log(error)
      })
      this.getProfiles()
      window.location.reload()
    }
  }
}
</script>

<style>

</style>
