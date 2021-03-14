<template>
  <div id="main-app" class="container">
    <div class="row justify-content-center">
      <add-appointment @add="addItem" />
      <search-appointments
        :myKey="filterKey"
        :myDir="filterDir"
        @searchRecords="searchAppointments"
        @requestKey="changeKey"
        @requestDir="changeDir"
      />
      <appointment-list
        :appointments="filteredApts"
        @remove="removeItem"
        @edit="editItem"
      />
    </div>
  </div>
</template>

<script>
import AddAppointment from "./components/AddAppointment";
import SearchAppointments from "./components/SearchAppointments";
import AppointmentList from "./components/AppointmentList";
import axios from "axios";
import _ from "lodash";

export default {
  name: "MainApp",
  data: () => {
    return {
      title: "Appointment List",
      appointments: [],
      aptIndex: 0,
      searchTerms: "",
      filterKey: "petName",
      filterDir: "asc"
    };
  },
  components: {
    AddAppointment,
    AppointmentList,
    SearchAppointments
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
  computed: {
    searchedApts() {
      return this.appointments.filter(item => {
        return (
          item.petName.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.petOwner.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.aptNotes.toLowerCase().match(this.searchTerms.toLowerCase())
        );
      });
    },
    filteredApts() {
      return _.orderBy(
        this.searchedApts,
        item => {
          return item[this.filterKey].toLowerCase();
        },
        this.filterDir
      );
    }
  },
  methods: {
    searchAppointments(terms) {
      this.searchTerms = terms;
    },
    removeItem(apt) {
      this.appointments = _.without(this.appointments, apt);
    },
    editItem(id, field, text) {
      const aptIndex = _.findIndex(this.appointments, {
        aptId: id
      });
      this.appointments[aptIndex][field] = text;
    },
    addItem(apt) {
      apt.aptId = this.aptIndex;
      this.aptIndex++;
      this.appointments.push(apt);
    },
    changeKey(value) {
      this.filterKey = value;
    },
    changeDir(value) {
      this.filterDir = value;
    }
  }
};
</script>
