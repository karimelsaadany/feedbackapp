<template>
    <q-card>
        <q-card-section class="row justify-between">
            <div class="text-h5">Feedbacks</div>
            <q-btn @click.stop="showFeedbackEntry" v-if="customerSelected">add new</q-btn>
        </q-card-section>
        <q-separator inset/>
        <q-card-section v-if="isFeedbackEntryShown">
            <q-input label='New Feedback' v-model="tempFeedback" type="text" @keyup="addNewFeedback" @click.stop="keepFeedbackFocus" ref="feedbackInput"/>
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
</template>

<script>
export default {
    props: ['customersData', 'selectedCustomer', 'customerSelected', 'search', 'closeInput'],
    data() {
        return {
            tempFeedback:'',
            isFeedbackEntryShown: false,
        }
    },
    methods: {
        showFeedbackEntry() {
            this.isFeedbackEntryShown = true
            this.$nextTick(function(){
                this.$refs.feedbackInput.focus()
            })
        },
        addNewFeedback(e) {
            if (e.key === 'Enter' && this.tempFeedback){
                this.customersData[this.selectedCustomer].push(this.tempFeedback)
                this.tempFeedback = ''
                this.isFeedbackEntryShown = false
            } else if (e.key === 'Escape') {
                this.isFeedbackEntryShown = false
                this.tempFeedback = ''
                }
        },
        keepFeedbackFocus() {
            this.isFeedbackEntryShown = true
        },
        highLightText(text){
            var reg = new RegExp('('+this.search+')', 'gi')
            return text.replaceAll(reg, `<span class="highlight">$1</span>`)
        },
    },
    computed: {
        filteredFeedback() {
            const feedbackArr = Object.values(this.customersData[this.selectedCustomer])
            return feedbackArr.filter(feedback => feedback.toLowerCase().includes(this.search.toLowerCase()))
        },
    },
    watch: {
        closeInput(){
            if (this.isFeedbackEntryShown){
                this.tempFeedback = ''
                this.isFeedbackEntryShown = false
            }
        }
    }
}
</script>

<style>

</style>