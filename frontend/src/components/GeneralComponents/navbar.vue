<template>
  <v-layout class="overflow-visible" style="height: 56px">
    <v-bottom-navigation
      class="bg-deep-orange-darken-1"
      color="yellow-lighten-5"
      grow
    >
      <template v-if="role == 0">
        <v-btn to="/home">
          <v-icon>mdi-home-outline</v-icon>
          Search Trains
        </v-btn>

        <v-btn to="/myBooking">
          <v-icon>mdi-book-account</v-icon>
          Booking
        </v-btn>

        <v-btn to="/buses">
          <v-icon>mdi-bus</v-icon>
          Bus
        </v-btn>

        <v-btn to="/askYourQuery">
          <v-icon>mdi-help-circle-outline</v-icon>
          Ask Your Query
        </v-btn>
        <v-btn to="/userProfile"> 
          <v-icon>
            mdi-account
          </v-icon>
          My Profile
        </v-btn>
      </template>

      <template v-if="role == 1">
        <v-btn to="/dashboard">
          <v-icon>mdi-view-dashboard</v-icon>
          DashBoard
        </v-btn>
        <v-btn to="/adminAllBuses">
          <v-icon>mdi-history</v-icon>

          All Buses
        </v-btn>

        <v-btn to="/addBusDetail">
          <v-icon>mdi-heart</v-icon>

          Add Bus
        </v-btn>

        <v-btn to="/addConductorDetails"> 
          <v-icon>mdi-map-marker</v-icon>

          Add Conductors
        </v-btn>
        <v-btn to="/userDetails">
          <v-icon>mdi-account-group-outline</v-icon>
          Users
        </v-btn>
        <v-btn to="/listOfCanceledTickets" stacked="">
          <v-badge color="error"
          :content="totalCancelledTicket">
            <v-icon>mdi-toy-brick-remove</v-icon>
          </v-badge>
          Canceled Tickets
        </v-btn>
        <v-btn to="/listOfUsersHavingQuery">
          <v-icon >
            mdi-help-circle-outline
          </v-icon>
          List Of Users Query
        </v-btn>
        <v-btn>
          <v-icon>mdi-account</v-icon>
          My Profile
        </v-btn>
      </template>
      <template v-if="role==3">
        <v-btn to="/buses">
          <v-icon>mdi-bus-multiple</v-icon>
          All Buses
        </v-btn>
      <v-btn to="/assignedBuses">
        <v-icon>mdi-bus</v-icon>
        Buses Assigned
      </v-btn>
     <v-btn class="text-center" to="/listOfCanceledTicketsByBusId">
      <v-icon>mdi-list-status</v-icon>
      List Of Canceled Tickets
     </v-btn>
     <v-btn>
      <v-icon>mdi-account</v-icon>
      My Profile
     </v-btn>
      </template>
    </v-bottom-navigation>
  </v-layout>
</template>

<script setup>
import { useStore } from "vuex";
import { computed,ref, onMounted } from "vue";
import Pusher from "pusher-js";
const store = useStore();
const role = computed(() => {
  return store.state.users.role;
});
const totalCancelledTicket = ref(0);
onMounted(async()=>{
const pusher = new Pusher("5255d55b22b71ccf514f", { cluster: "ap2" });
totalCancelledTicket.value = await store.dispatch("triggerGetTotalCancelledTicketsWithPendingRefund")
pusher.subscribe('ticketCancelled')
pusher.bind('isCancelled',(data)=>{
  console.log("sdfsdfsdfs cancelled",data);
 totalCancelledTicket.value = totalCancelledTicket.value++;
})
pusher.subscribe('refundPaid')
pusher.bind('refundData',(data)=>{
  console.log(data);
  console.log("refund received ");
  totalCancelledTicket.value= totalCancelledTicket.value--;
})
})
</script>
<style scoped></style>
