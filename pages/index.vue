<template>
 <v-card>
  <v-container>
    <v-tabs color="deep-purple accent-4" centered>
    <v-tab>AMC Jobs</v-tab>
    <v-tab>Ticket</v-tab>
    <v-tab-item>
      <AMC status="Pending" />
    </v-tab-item>
    <v-tab-item>
      <Ticket status="Pending" />
    </v-tab-item>
  </v-tabs>
  </v-container>
 </v-card>
</template>
