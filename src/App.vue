<script setup>

  import {onMounted, ref} from 'vue'
  import listCardView from './components/cards/listCardView.vue'
  import { initializeApp } from "firebase/app";
  import {getFirestore, collection, query, getDocs, onSnapshot} from 'firebase/firestore'
  import {initializeAppCheck, ReCaptchaEnterpriseProvider} from 'firebase/app-check';

  const ID_TOKEN = import.meta.env.ID_TOKEN

  const cards = ref([]);

  // Your web app's Firebase configuration
  const firebaseConfig = {

  };

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  // Initialize Cloud Firestore and get a reference to the service
  const db = getFirestore(app);

  
  onMounted(async () => {
    self.FIREBASE_APPCHECK_DEBUG_TOKEN = true;
    initializeAppCheck(app, {
      provider: new ReCaptchaEnterpriseProvider(ID_TOKEN),
      //isTokenAutoRefreshEnabled: true // Set 
    })
    const q = query(collection(db, "cards"));
    const querySnapshot = await getDocs(q);
    querySnapshot.forEach((doc) => {
      cards.value.push(doc.data())
    });


    const unsubscribe = onSnapshot(q, (querySnapshot) => {
      const cities = [];
      querySnapshot.forEach((doc) => {
          cities.push(doc.data().name);
      });
      console.log("Current cities in CA: ", cities.join(", "));
    });
      })


</script>

<template>
  <h1 class="text-red-500">Hola mundo!!!</h1>

  <listCardView
    :cards="cards"
  />

</template>

<style scoped>

</style>
