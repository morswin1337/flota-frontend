<template>
  <div>
    <h2>ADD CAR</h2>
    <form @submit.prevent="addCar">
      <input v-model="newCar.make" placeholder="Make">
      <input v-model="newCar.model" placeholder="Model">
      <input v-model.number="newCar.mileage" placeholder="Mileage">
      <button type="submit">Add Car</button>
    </form>

    <h2>LIST OF CARS</h2>
    <ul>
      <li v-for="car in cars" :key="car.uuid">
        <div>
          ID: {{ car.uuid }} <!-- Displaying car UUID -->
          Make: {{ car.make }}
          Model: {{ car.model }}
          Mileage: {{ car.mileage }}
          <button @click="enableEditing(car)">Edit</button>
          <button @click="deleteCar(car.uuid)">Delete</button>
        </div>
        <div v-if="car.isEditing">
          <input v-model="car.editData.make" placeholder="Make">
          <input v-model="car.editData.model" placeholder="Model">
          <input v-model.number="car.editData.mileage" placeholder="Mileage">
          <button @click="updateCar(car)">Save</button>
          <button @click="cancelEditing(car)">Cancel</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import CarService from '@/services/CarService';

export default {
  data() {
    return {
      cars: [],
      newCar: {
        make: '',
        model: '',
        mileage: 0
      }
    };
  },
  mounted() {
    this.fetchCars();
  },
  methods: {
    fetchCars() {
      CarService.getAllCars()
        .then(response => {
          this.cars = response.data.map(car => ({ ...car, isEditing: false }));
        })
        .catch(error => {
          console.error('Error fetching cars:', error);
        });
    },

    deleteCar(uuid) {
      CarService.deleteCar(uuid)
        .then(() => {
          this.cars = this.cars.filter(car => car.uuid !== uuid);
        })
        .catch(error => {
          console.error('Error deleting the car:', error);
        });
    },

    addCar() {
      CarService.addCar(this.newCar)
        .then(response => {
          this.cars.push({ ...response.data, isEditing: false });
          this.newCar = { make: '', model: '', mileage: 0 };
        })
        .catch(error => {
          console.error('Error adding the car:', error);
        });
    },

    enableEditing(car) {
      car.isEditing = true;
      car.editData = { make: car.make, model: car.model, mileage: car.mileage }; // Create a copy for editing
    },

    cancelEditing(car) {
      car.isEditing = false;
    },

    updateCar(car) {
      CarService.updateCar(car.uuid, car.editData)
        .then(() => {
          car.isEditing = false;
          Object.assign(car, car.editData); // Update the car data in the list
        })
        .catch(error => {
          console.error('Error updating the car:', error);
          this.fetchCars(); // Reload cars to reset edited data
        });
    }
  }
};
</script>

<style>
/* General Styles */
body {
  background-color: #121212;
  color: #e0e0e0; /* Brighter font color */
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  background-color: #1e1e1e;
  border: 1px solid #333;
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 5px;
  color: #f0f0f0; /* Even brighter font color for list items */
}

/* Form Styles */
form {
  margin-bottom: 20px;
}

input[type="text"], input[type="number"] {
  background-color: #333;
  border: 1px solid #444;
  border-radius: 4px;
  padding: 8px;
  margin: 5px;
  width: 200px;
  color: #fff; /* White font color for input fields */
}

button {
  background-color: #3a3a3a;
  color: white;
  padding: 10px 15px;
  border: 1px solid #444;
  border-radius: 4px;
  cursor: pointer;
  margin: 5px;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #555;
}

/* Editing Mode Styles */
div[v-if="car.isEditing"] {
  background-color: #292929;
  padding: 10px;
  margin-top: 10px;
  border-radius: 5px;
}

/* Display Section */
div {
  margin: 5px 0;
}

/* Headings Styles */
h2 {
  color: #e0e0e0; /* Bright font color for better visibility in dark mode */
  text-align: center;
  margin-bottom: 15px;
}
</style>
