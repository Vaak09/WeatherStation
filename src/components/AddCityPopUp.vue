<template>
    <div>
        <div class="overlay" @click="$emit('close')"></div>
        <div class="modal d-block" tabindex="-1" role="dialog" aria-label="Add City Popup">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content shadow-lg">
                    <div class="modal-header">
                        <h5 class="modal-title">Add City</h5>
                        <button type="button" class="btn-close" aria-label="Close" @click="$emit('close')"></button>
                    </div>
                    <div class="modal-body">
                        <form @submit.prevent="addCity">
                            <div class="mb-3">
                                <label for="cityName" class="form-label">City Name</label>
                                <input
                                    type="text"
                                    id="cityName"
                                    v-model="cityName"
                                    class="form-control"
                                    placeholder="Enter city name"
                                    aria-label="City Name"
                                />
                            </div>
                            <p v-if="errorMessage" class="text-danger">{{ errorMessage }}</p>
                            <div class="d-flex justify-content-end gap-2">
                                <button type="submit" class="btn btn-primary">Add</button>
                                <button type="button" class="btn btn-secondary" @click="$emit('close')">Close</button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "AddCityPopUp",
    data() {
        return {
            cityName: "",
            errorMessage: "",
        };
    },
    methods: {
        async validateCity(cityName) {
            var key = process.env.VUE_APP_OPENWEATHERMAP_KEY;

            try {
                const response = await fetch(
                    `https://api.openweathermap.org/data/2.5/weather?q=${cityName}&appid=${key}`
                );
                if (!response.ok) {
                    throw new Error();
                }
                return true;
            } catch (error) {
                this.errorMessage = "Invalid city name. Please try again.";
                return false;
            }
        },
        async addCity() {
            this.errorMessage = ""; // Clear previous error message
            if (this.cityName.trim()) {
                const isValid = await this.validateCity(this.cityName.trim());
                if (isValid) {
                    this.$emit("add-city", this.cityName);
                    this.cityName = "";
                    this.$emit("close"); // Close the popup after adding
                }
            } else {
                this.errorMessage = "City name cannot be empty.";
            }
        },
    },
};
</script>

<style scoped>
.overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(to bottom, rgba(135, 206, 235, 0.8), rgba(240, 248, 255, 0.8));
    z-index: 1040;
}
.modal {
    z-index: 1050;
}
.modal-content {
    background: rgba(255, 255, 255, 0.9);
    border-radius: 10px;
}
.modal-header {
    border-bottom: none;
}
</style>