<template>
  <div class="back" @click="closeInputs">
    <div class="row justify-between q-pa-sm">
      <h4 class="q-ma-none q-pa-md">Customer Feedback</h4>
      <q-input class="q-my-auto" outlined label="Search for Feedback" v-model="search" @keydown.esc="escapeSearch" ref='search' dense/>
    </div>
    <div class="row q-pa-md">
      <div class="col">
        <q-card>
          <q-card-section class="row justify-between">
            <div class="text-h5">Customers</div>
            <q-btn @click.stop="showCustomerEntry">add new</q-btn>
          </q-card-section>
          <q-separator inset class="q-ma-none"/>
          <q-card-section v-show="isCustomerEntryShown">
            <q-input label='New Customer' v-model="tempCustomer" bottom-slots :error="validation" error-message="Customer already exists!" @keyup="addNewCustomer" @click.stop="keepCustomerFocus" ref="customerInput"/>
          </q-card-section>
          <q-card-section v-for="(feedbacks ,customer) in data" :key="customer" class="q-pa-none">
            <p @click="showFeedbacks(customer), selectedCustomer = customer" class="cursor-pointer q-ma-none q-pa-md" :class="{active: customer == selectedCustomer}">{{customer}}</p>
            <q-separator inset class="q-ma-none"/>
          </q-card-section>
        </q-card>
      </div>
      <div class="col">
        <q-card>
          <q-card-section class="row justify-between">
            <div class="text-h5">Feedbacks</div>
            <q-btn @click.stop="showFeedbackEntry" v-if="customerSelected">add new</q-btn>
          </q-card-section>
          <q-separator inset/>
          <q-card-section v-if="isFeedbackEntryShown">
            <q-input label='New Feedback' v-model="tempFeedback" type="textarea" @keyup="addNewFeedback" @click.stop="keepFeedbackFocus" ref="feedbackInput"/>
          </q-card-section>
          <q-card-section v-if="!customerSelected">
            <p class="text-center q-my-auto">Please select a customer</p>
          </q-card-section>
          <q-card-section v-if="customerSelected" class="q-pa-none">
            <q-card-section v-for="feedback in filteredFeedback" :key="feedback" class="q-pa-none">
              <p v-html="highLightText(feedback)" class="q-ma-none q-pa-md"></p>
              <q-separator inset class="q-ma-none"/>
            </q-card-section>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      data: {},
      tempCustomer: '',
      tempFeedback:'',
      isCustomerEntryShown: false,
      isFeedbackEntryShown: false,
      customerSelected: false,
      selectedCustomer: '',
      search: '',
      validation: false,
    }
  },
  queries: [],
  methods: {
    showCustomerEntry() {
      this.isCustomerEntryShown = true
      this.customerSelected = false
      this.isFeedbackEntryShown = false
      this.$nextTick(function(){
        this.$refs.customerInput.focus()
        this.selectedCustomer = ''
      })
    },
    addNewCustomer(e){
      if (e.key === 'Enter' && this.tempCustomer){
        const arr = this.tempCustomer.split(" ")
        for (var i = 0; i < arr.length; i++){
          arr[i] = arr[i].charAt(0).toUpperCase() + arr[i].slice(1)
        }
        const customer = arr.join(" ")

        if (!this.data.hasOwnProperty(customer)){
          this.data[customer]=[]
          this.isCustomerEntryShown = false
          this.validation = false
        } else {
          this.validation = true
        }
        this.tempCustomer = ''
      } else if (e.key === 'Escape') {
        this.tempCustomer = ''
        this.validation = false
        this.isCustomerEntryShown = false
      }
    },
    showFeedbackEntry() {
      this.isFeedbackEntryShown = true
      this.$nextTick(function(){
        this.$refs.feedbackInput.focus()
      })
    },
    showFeedbacks(customer) {
      this.customerSelected = true
      this.selectedCustomer = customer
    },
    addNewFeedback(e) {
      if (e.key === 'Enter' && this.tempFeedback){
        this.data[this.selectedCustomer].push(this.tempFeedback)
        this.tempFeedback = ''
        this.isFeedbackEntryShown = false
      } else if (e.key === 'Escape') {
          this.isFeedbackEntryShown = false
          this.tempFeedback = ''
      }
    },
    closeInputs() {
      this.isCustomerEntryShown = false
      this.isFeedbackEntryShown = false
      this.tempCustomer = ''
      this.tempFeedback = ''
      this.search=''
      this.validation = false
    },
    escapeSearch() {
      this.search = ''
      this.$refs.search.blur()
    },
    keepCustomerFocus() {
      this.isCustomerEntryShown = true
    },
    keepFeedbackFocus() {
      this.isFeedbackEntryShown = true
    },
    highLightText(text){
      var reg = new RegExp('('+this.search+')', 'gi')
      return text.replaceAll(reg, `<span class="highlight">$1</span>`)
    }
  },
  computed: {
    filteredFeedback() {
      const feedbackArr = Object.values(this.data[this.selectedCustomer])
      return feedbackArr.filter(feedback => feedback.toLowerCase().includes(this.search.toLowerCase()))
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

  .active {
    background-color: #26A69A;
    color: white;
  }

  .highlight {
    background-color: #86ece2;
  }
</style>