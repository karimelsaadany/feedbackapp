<template>
    <q-card>
        <q-card-section class="row justify-between">
            <div class="text-h5">Customers</div>
            <q-btn @click.stop="showCustomerEntry">add new</q-btn>
        </q-card-section>
        <q-separator inset class="q-ma-none"/>
        <q-card-section v-show="isCustomerEntryShown">
            <q-input label='New Customer' v-model="tempCustomer" bottom-slots :error="validation" error-message="Customer already exists!" @keyup="addNewCustomer" @click.stop="keepCustomerFocus" ref="customerInput"/>
        </q-card-section>
        <q-card-section v-for="(feedbacks ,customer) in customersData" :key="customer" class="q-pa-none">
            <p @click="showFeedbacks(customer), selectedCustomer = customer" class="cursor-pointer q-ma-none q-pa-md" :class="{active: customer == selectedCustomer}">{{customer}}</p>
            <q-separator inset class="q-ma-none"/>
        </q-card-section>
    </q-card>
</template>

<script>
export default {
    props: ['closeInput'],
    data() {
        return {
            customersData: {},
            tempCustomer: '',
            isCustomerEntryShown: false,
            customerSelected: false,
            selectedCustomer: '',
            validation: false,
        }
    },
    methods: {
        showCustomerEntry() {
            this.isCustomerEntryShown = true
            this.customerSelected = false
            this.$emit('noCustomerSelected')
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

                if (!this.customersData.hasOwnProperty(customer)){
                    this.customersData[customer]=[]
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
        keepCustomerFocus() {
            this.isCustomerEntryShown = true
        },
        showFeedbacks(customer) {
            this.customerSelected = true
            this.selectedCustomer = customer
            this.isCustomerEntryShown = false
            this.$emit('customerSelected', customer, this.customersData)
        },
    },
    watch: {
        closeInput(){
            if (this.isCustomerEntryShown){
                this.tempCustomer = ''
                this.isCustomerEntryShown = false
                this.validation = false
            }
        }
    }
}
</script>

<style>


    .active {
        background-color: #26A69A;
        color: white;
    }
</style>