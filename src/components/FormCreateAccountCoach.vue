<template>
  <form @submit.prevent="createAccount" enctype="multipart/form-data">
    <div>
      <label for="email">Email :</label>
      <input type="email" id="email" v-model="user.email" required>
    </div>
    <div>
      <label for="password">Mot de passe :</label>
      <input type="password" id="password" v-model="user.password" required>
    </div>
    <div>
      <label for="firstname">Prénom :</label>
      <input type="text" id="firstname" v-model="user.firstname" required>
    </div>
    <div>
      <label for="lastname">Nom :</label>
      <input type="text" id="lastname" v-model="user.lastname" required>
    </div>
    <div>
      <label for="userName">Nom d'utilisateur :</label>
      <input type="text" id="userName" v-model="user.userName" required>
    </div>
    <div>
      <label for="diploma">Diplôme (PDF) :</label>
      <input type="file" id="diploma" @change="handleFileUpload" accept=".pdf" required>
    </div>
    <!-- Affichage du message d'erreur -->
    <div v-if="error" class="error-message">
    {{ error }}
    </div>
    <button type="submit">Créer le compte</button>
    
   
  </form>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'FormCreateAccountCoach',
  setup() {
    const user = ref({
      email: '',
      password: '',
      firstname: '',
      lastname: '',
      userName: '',
      userType: 'COACH',
    });
    const error = ref('');
    const diploma = ref<File | null>(null);

    const handleFileUpload = (event: Event) => {
      const target = event.target as HTMLInputElement;
      if (target.files) {
        diploma.value = target.files[0];
      }
    };

    const createAccount = async () => {
      try {
        error.value = '';
        const formData = new FormData();
        Object.entries(user.value).forEach(([key, value]) => {
          formData.append(key, value);
        });
        if (diploma.value) {
          formData.append('diploma', diploma.value);
        }

        const response = await fetch('http://localhost:3000/auth/register', {
          method: 'POST',
          body: formData,
        });

        if (!response.ok) {
          const errorData = await response.json();
          throw new Error(errorData.message);
        }

        const data = await response.json();
        console.log('Compte créé avec succès:', data);
        // Réinitialiser le formulaire ou rediriger l'utilisateur
      } catch (err) {
        if (err instanceof Error) {
          error.value = err.message;
        } else {
          error.value = 'Une erreur est survenue lors de la création du compte.';
        }
      }
    };

    return {
      user,
      createAccount,
      error,
      handleFileUpload, // Assurez-vous d'inclure handleFileUpload ici
    };
  },
});
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  max-width: 300px;
  margin: 0 auto;
  padding-bottom: 50px;
}

label {
  font-weight: bold;
}

input, select {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  background-color: #4CAF50;
  color: white;
  padding: 0.5rem;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

.error-message {
  color: red;
  margin-top: 10px;
  padding: 10px;
  background-color: #ffeeee;
  border: 1px solid #ffcccc;
  border-radius: 4px;
}
</style>
