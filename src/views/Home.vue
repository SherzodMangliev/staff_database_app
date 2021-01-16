<template>
  <div class="home bg-light">
    <div class="container-fluid main-body pt-5 p-1">
      <div class="row pt-3 mx-0">
        <div class="col-lg-4 px-1">
          <dashboard-card icon="fas fa-users" title="Количество всех студентов" :subtitle="numberOfProfiles" />
        </div>
        <div class="col-lg-4 px-1">
          <dashboard-card icon="fas fa-male" title="Количество мужчин" :subtitle="numberOfMales" />
        </div>
        <div class="col-lg-4 px-1">
          <dashboard-card icon="fas fa-female" title="Количество женщин" :subtitle="numberOfFemales" />
        </div>
      </div>
      <div class="row pt-3 mx-0">
        <div class="col-lg-4 px-1">
          <dashboard-card class="h-100" icon="fas fa-laptop-code" iconSize="fa-2x" title="Количество студентов в факултете ИТ" :subtitle="numberOfIt" />
        </div>
        <div class="col-lg-4 px-1">
          <dashboard-card icon="fas fa-calculator" iconSize="fa-2x" title="Количество студентов в факултете Бухгалтерия" :subtitle="numberOfAccounting" />
        </div>
        <div class="col-lg-4 px-1">
          <dashboard-card icon="fas fa-icons" iconSize="fa-2x" title="Количество студентов в факултете Маркетинг" :subtitle="numberOfMarketing" />
        </div>
      </div>
      <div class="row mt-2 mx-0">
        <div class="col-lg-4">
          <h6 class="text-center mb-0">Количество студентов в каждом факултете</h6>
          <chart-doughnut :title="chartTitle" :datasets="chartBranchDatasets" :labels="chartBranchLabels" />
        </div>
        <div class="col-lg-4">
          <h6 class="text-center mb-0">Количество мужчин и женщин</h6>
          <chart-pie :title="chartTitle" :datasets="chartGenderDatasets" :labels="chartGenderLabels" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ChartDoughnut from '../components/ChartDoughnut.vue'
// @ is an alias to /src
import DashboardCard from '../components/dashboardCard.vue'
import { mapGetters, mapActions } from 'vuex'
import ChartPie from '../components/ChartPie.vue'

export default {
  name: 'Home',
  components: {
    DashboardCard,
    ChartDoughnut,
    ChartPie
  },
  data () {
    return {
      cardSubtitle: 'staff number',
      icon: 'fas fa-cog',
      sidebar: false,
      numberOfProfiles: null,
      numberOfFemales: null,
      numberOfMales: null,
      numberOfIt: null,
      numberOfMarketing: null,
      numberOfAccounting: null,
      chartTitle: 'Количество cотрудников',
      chartBranchLabels: ['ИТ', 'Бухгалтерия', 'Маркетинг'],
      chartBranchDatasets: [
        {
          backgroundColor: ['#fff251', '#db8f21', '#b48ff3'],
          data: [],
          label: 'Количество'
        }
      ],
      chartGenderLabels: ['Мужчины', 'Женщины'],
      chartGenderDatasets: [
        {
          backgroundColor: ['#82C7EB', '#FA8CAB'],
          data: [],
          label: 'Количество'
        }
      ]
    }
  },
  async created () {
    await this.fetchProfiles()
    this.numberOfProfiles = this.returnData.length
    this.getNumberOfMales()
    this.getNumberOfFemales()
    this.getNumberOfItProfiles()
    this.getNumberOfAccountingProfiles()
    this.getNumberOfMarketingProfiles()
  },
  computed: {
    ...mapGetters(['returnData', 'returnFilteredProfiles'])
  },
  methods: {
    ...mapActions(['fetchProfiles', 'fetchFilteredProfiles']),
    async getNumberOfFemales () {
      await this.fetchFilteredProfiles('&gender_id=0')
      this.numberOfFemales = this.returnFilteredProfiles.length
      this.chartGenderDatasets[0].data.push(this.numberOfFemales)
    },
    async getNumberOfMales () {
      await this.fetchFilteredProfiles('&gender_id=1')
      this.numberOfMales = this.returnFilteredProfiles.length
      this.chartGenderDatasets[0].data.push(this.numberOfMales)
    },
    async getNumberOfItProfiles () {
      await this.fetchFilteredProfiles('&branch_id=0')
      console.log(this.returnFilteredProfiles.length)
      this.numberOfIt = this.returnFilteredProfiles.length
      this.chartBranchDatasets[0].data.push(this.returnFilteredProfiles.length)
    },
    async getNumberOfAccountingProfiles () {
      await this.fetchFilteredProfiles('&branch_id=1')
      console.log(this.returnFilteredProfiles.length)
      this.numberOfAccounting = this.returnFilteredProfiles.length
      this.chartBranchDatasets[0].data.push(this.returnFilteredProfiles.length)
    },
    async getNumberOfMarketingProfiles () {
      await this.fetchFilteredProfiles('&branch_id=2')
      console.log(this.returnFilteredProfiles.length)
      this.numberOfMarketing = this.returnFilteredProfiles.length
      this.chartBranchDatasets[0].data.push(this.returnFilteredProfiles.length)
    }
  }
}
</script>

<style>
  .main-body {
    padding-top: 70px !important;
  }

</style>
