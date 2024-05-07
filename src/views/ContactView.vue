<script setup>
import { ref } from 'vue';
import { useForm, Field, ErrorMessage, defineRule } from 'vee-validate';
import { required, email, min, max } from '@vee-validate/rules';
import * as yup from 'yup';
import axios from 'axios';
import { useRouter } from 'vue-router'; // Router importálása

defineRule('required', required);
defineRule('email', email);
defineRule('min', min);
defineRule('max', max);

const schema = yup.object({
  name: yup.string()
      .required('A nevet kötelező kitölteni!')
      .min(8, 'A név mezőnek legalább 8 karakter hosszúnak kell lennie!')
      .max(100, 'A név mező legfeljebb 100 karakter hosszú lehet!'),
  email: yup.string()
      .email('Nem megfelelő formátum!')
      .required('Kötelező kitölteni'),
  telephone: yup.string().required('A telefonszámot kötelező kitölteni!'),
  message: yup.string().required('Kérlek add meg, mit szeretnél!')
});

const form = ref({
  name: '',
  email: '',
  telephone: '',
  message: ''
});

const router = useRouter(); // Router használata

const { handleSubmit, errors, meta } = useForm({
  validationSchema: schema
});

const onSubmit = handleSubmit(values => {
  const link = "https://formspree.io/f/mrgnjwqg";
  axios.post(link, values, {
    headers: {
      'Accept': 'application/json',
    },
  }).then(response => {
    alert('Köszönjük, üzenetét sikeresen elküldtük!');
    form.value = { name: '', email: '', telephone: '', message: '' }; // Forma mezők kiürítése
    router.push('/'); // Átirányítás a főoldalra
  }).catch(error => {
    console.error("Error sending form:", error);
  });
});
</script>

<template>
  <h2 class="display-10">Kapcsolat</h2>
  <div class="w-75 mt-5 m-auto p-5 bg-danger bg-opacity-25">
    <form @submit.prevent="onSubmit">
      <div class="input-group">
        <label class="input-group-text" for="name">Név*</label>
        <Field type="text" name="name" id="name" v-model="form.name" class="form-control"/>
        <ErrorMessage name="name" class="alert alert-danger m-1"/>
      </div>
      <div class="input-group">
        <label class="input-group-text" for="email">Email*</label>
        <Field type="text" name="email" id="email" v-model="form.email" class="form-control"/>
        <ErrorMessage name="email" class="alert alert-danger m-1"/>
      </div>
      <div class="input-group">
        <label class="input-group-text" for="telephone">Telefonszám*</label>
        <Field type="text" name="telephone" id="telephone" v-model="form.telephone" class="form-control"/>
        <ErrorMessage name="telephone" class="alert alert-danger m-1"/>
      </div>
      <div class="input-group">
        <label class="input-group-text" for="message">Üzenet*</label>
        <Field type="textarea" name="message" id="message" v-model="form.message" class="form-control"/>
        <ErrorMessage name="message" class="alert alert-danger m-1"/>
      </div>
      <button type="submit" class="btn btn-primary mt-3" :disabled="meta.pending">Küldés</button>
    </form>
  </div>
</template>

<style scoped>
/* A saját stílusok itt helyezkednek el */
</style>
