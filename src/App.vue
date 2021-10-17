<template>
<div class="back" @click="closeInputs">
  <div class="row justify-between q-pa-sm">
    <h4 class="q-ma-none q-pa-md">Customer Feedback</h4>
    <q-input class="q-my-auto" outlined label="Search for Feedback" v-model="search" @keydown.esc="escapeSearch" ref='search' dense/>
  </div>
  <div class="row q-pa-md">
    <div class="col">
      <Customer @customerSelected="chosenCustomer" @noCustomerSelected="unselectCustomer" :closeInput="closeInput"/>
    </div>
    <div class="col">
      <Feedback :customersData="customersData" :selectedCustomer="selectedCustomer" :customerSelected="customerSelected" :search="search" :closeInput="closeInput"/>
    </div>
  </div>
</div>
</template>

<script>
import Customer from './components/Customer.vue'
import Feedback from './components/Feedback.vue'

export default {
  name: 'App',
  components: {Customer, Feedback},
  data () {
    return {
      customersData: {},
      customerSelected: false,
      selectedCustomer: '',
      search: '',
      closeInput: false,
    }
  },
  methods: {
    chosenCustomer(customer, customersData) {
      this.customersData = customersData
      this.customerSelected = true
      this.selectedCustomer = customer
    },
    escapeSearch() {
      this.search = ''
      this.$refs.search.blur()
    },
    unselectCustomer() {
      this.customerSelected = false
    },
    closeInputs() {
      this.closeInput = !this.closeInput
    },
  },
}
</script>

<style>

  .back {
    top: 0;
    position: fixed;
    width: 100%;
    height: 100%;
    overflow-y: scroll;
  }

  p {
    text-overflow: ellipsis;
    overflow: hidden;
    width: inherit;
  }

  .highlight {
    background-color: #86ece2;
  }
</style>