<template>
  <div id="main-app" class="container">
    <div class="row justify-content-center">
      <appointment-list :appointments="appointments" @remove="removeItem" />
    </div>
  </div>
</template>

<script>
import AppointmentList from "./components/AppointmentList";
import axios from "axios";
import _ from "lodash";

export default {
  name: "MainApp",
  data: () => {
    return {
      title: "Appointment List",
      appointments: [],
      aptIndex: 0
    };
  },
  components: {
    AppointmentList
  },
  mounted: function() {
    axios.get("./data/appointments.json").then(
      res =>
        (this.appointments = res.data.map(item => {
          item.aptId = this.aptIndex;
          this.aptIndex++;
          return item;
        }))
    );
  },
  methods: {
    removeItem: function(apt) {
      this.appointments = _.without(this.appointments, apt);
    }
  }
};
</script>
