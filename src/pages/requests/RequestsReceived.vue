<template>
    <div>
        <base-dialog v-bind:show="!!error" title="An error occurred!" v-on:close="handleError">
            <p>{{error}}</p>
        </base-dialog>
        <section>
            <base-card>
                <header>
                    <h2>Requests Received</h2>
                </header>
                <div v-if="isLoading">
                    <base-spinner></base-spinner>
                </div>
                <ul v-else-if="hasRequests && !isLoading">
                    <request-item v-for="req in receivedRequests"
                    v-bind:key="req.id"
                    v-bind:email="req.userEmail"
                    v-bind:message="req.message"
                    ></request-item>
                </ul>
                <h3 v-else>You haven't received any requests yet!</h3>
            </base-card>
        </section>
    </div>
</template>

<script>
import RequestItem from '../../components/requests/RequestItem.vue';

export default {
    components:{
        RequestItem
    },
    data(){
        return {
            isLoading:false,
            error:null
        };
    },
    computed:{
        receivedRequests(){
            return this.$store.getters['requests/requests'];
        },
        hasRequests(){
            return this.$store.getters['requests/hasRequests'];
        }
    },
    created(){
        this.loadRequests();
    },
    methods:{
        async loadRequests(){
            this.isLoading=true;
            try{
                await this.$store.dispatch('requests/fetchRequests');
            }catch(error){
                this.error = error.message || 'Something failed!';
            }
            this.isLoading=false;
        },
        handleError(){
            this.error = null;
        }
    }
}
</script>

<style scoped>
header {
  text-align: center;
}

ul {
  list-style: none;
  margin: 2rem auto;
  padding: 0;
  max-width: 30rem;
}

h3 {
  text-align: center;
}
</style>