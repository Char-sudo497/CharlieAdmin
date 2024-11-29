<template>
    <v-container>
      <!-- Card with Star Ratings -->
      <v-card class="mb-4 elevation-2">
        <v-card-title class="headline text-center">Ratings Overview</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12" sm="6" md="2" v-for="star in [1, 2, 3, 4, 5]" :key="star">
              <v-card outlined class="text-center">
                <v-card-text>
                  <v-icon color="yellow" class="mr-1" v-for="n in star" :key="n">mdi-star</v-icon>
                  <span>{{ star }} Star</span>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
  
      <!-- Data Table -->
      <v-card class="elevation-2">
        <v-card-title class="headline text-center">
          Ratings Management
        </v-card-title>
        <v-data-table
          :headers="headers"
          :items="ratings"
          item-value="id"
          class="elevation-1"
        >
          <!-- Actions Slot -->
          <template v-slot:item.actions="{ item }">
            <v-btn icon color="blue" @click="viewRating(item)">
              <v-icon>mdi-eye</v-icon>
            </v-btn>
            <v-btn icon color="red" @click="deleteRating(item.id)">
              <v-icon>mdi-delete</v-icon>
            </v-btn>
          </template>
        </v-data-table>
      </v-card>
    </v-container>
  </template>
  
  <script>
  export default {
    data() {
      return {
        headers: [
          { text: 'Customer Name', value: 'customerName' },
          { text: 'Product Name', value: 'productName' },
          { text: 'Ratings', value: 'ratings' },
          { text: 'Rating Description', value: 'ratingDescription' },
          { text: 'Actions', value: 'actions', sortable: false }
        ],
        ratings: [
          // Example data
          {
            id: 1,
            customerName: 'John Doe',
            productName: 'Sample Product',
            ratings: 4,
            ratingDescription: 'Great product!',
          },
          {
            id: 2,
            customerName: 'Jane Smith',
            productName: 'Another Product',
            ratings: 5,
            ratingDescription: 'Excellent quality!',
          },
        ]
      };
    },
    methods: {
      viewRating(item) {
        console.log('View Rating:', item);
        // Logic to view the rating details
      },
      deleteRating(id) {
        console.log('Delete Rating ID:', id);
        // Logic to delete the rating
        this.ratings = this.ratings.filter(rating => rating.id !== id);
      }
    }
  };
  </script>
  
  <style>
  /* Style for star icons */
  </style>
  