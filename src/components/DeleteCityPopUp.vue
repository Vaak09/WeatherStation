<template>
    <div>
        <div class="overlay" @click="$emit('close')"></div>
        <div class="modal d-block" tabindex="-1" role="dialog" aria-label="Delete City Popup">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content shadow-lg">
                    <div class="modal-header">
                        <h5 class="modal-title">Delete City</h5>
                        <button type="button" class="btn-close" aria-label="Close" @click="$emit('close')"></button>
                    </div>
                    <div class="modal-body">
                        <form @submit.prevent="deleteCity">
                            <div class="mb-3">
                                <label for="citySelect" class="form-label">Select City</label>
                                <select
                                    id="citySelect"
                                    class="form-select"
                                    aria-label="Select City"
                                    v-model="selectedCity"
                                >
                                    <option v-for="city in cities" :key="city.name" :value="city.name">
                                        {{ city.name }}
                                    </option>
                                </select>
                            </div>
                            <div class="d-flex justify-content-end gap-2">
                                <button type="submit" class="btn btn-danger">Delete</button>
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
    name: "DeleteCityPopUp",
    props: {
        cities: {
            type: Array,
            required: true,
        },
    },
    data() {
        return {
            selectedCity: "",
        };
    },
    methods: {
        deleteCity() {
            if (this.selectedCity.trim()) {
                this.$emit("delete-city", this.selectedCity);
                this.selectedCity = ""; // Reset the selection after deletion
                this.$emit("close"); // Close the popup after deletion
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