<template>
    <div>
        <form @submit.prevent="submitForm">
            <label for="email">Email:</label>
            <input type="email" id="email" v-model="formData.email" required>

            <label for="name">Name:</label>
            <input type="text" id="name" v-model="formData.name" required>

            <label for="password">Password:</label>
            <input type="password" id="password" v-model="formData.password" required>

            <button type="submit">Submit</button>
        </form>
    </div>
</template>

<script lang="ts">
import { auth, database } from "../../firebaseConfig";


export default {
    data() {
        return {
            formData: {
                email: "",
                name: "",
                password: "",
            },
        };
    },
    methods: {
        async submitForm() {
            try {
                // Create a user with the provided email and password
                const userCredential = await auth.createUserWithEmailAndPassword(
                    this.formData.email,
                    this.formData.password
                );

                // Get the user's unique ID from the userCredential
                const userId = userCredential.user.uid;

                // Store additional user data in the Firebase Database
                await database.ref(`users/${userId}`).set({
                    name: this.formData.name,
                });

                // Clear the form fields
                this.formData.email = "";
                this.formData.name = "";
                this.formData.password = "";

                console.log("User registered successfully.");
            } catch (error) {
                console.error("Error registering user:", error.message);
            }
        },
    },
};
</script>