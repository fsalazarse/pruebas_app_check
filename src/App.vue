<script setup>

  import {onMounted, ref} from 'vue'
  import listCardView from './components/cards/listCardView.vue'
  import { initializeApp } from "firebase/app";
  import {getFirestore, collection, query, getDocs, onSnapshot} from 'firebase/firestore'
  import {initializeAppCheck, ReCaptchaEnterpriseProvider, getToken} from 'firebase/app-check';

  const ID_TOKEN = import.meta.env.VITE_ID_TOKEN

  const cards = ref([]);

  // Your web app's Firebase configuration
  const firebaseConfig = {
    apiKey: import.meta.env.VITE_API_KEY,
    authDomain: import.meta.env.VITE_AUTH_DOMAIN ,
    databaseURL: import.meta.env.VITE_DATABASE_URL,
    projectId: import.meta.env.VITE_PROJECT_ID,
    storageBucket: import.meta.env.VITE_STORAGE_BUCKET,
    messagingSenderId: import.meta.env.VITE_MESSAGIN_SENDER_ID,
    appId: import.meta.env.VITE_APP_ID
  };

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  // Initialize Cloud Firestore and get a reference to the service
  const db = getFirestore(app);

  
  const callApiWithAppCheckExample = async (appCheck) => {
    let appCheckTokenResponse;
    try {
        appCheckTokenResponse = await getToken(appCheck, /* forceRefresh= */ false);
        console.log("token!!!!", appCheckTokenResponse.token);
    } catch (err) {
        console.log("error in callApiWithAppCheckExample", err);
        // Handle any errors if the token was not retrieved.      
    }

     // Include the App Check token with requests to your server.
    const apiResponse = await fetch('https://us-central1-fabianlab-e67a4.cloudfunctions.net/plugin_03_v1_test_app_check/testToken', {
        headers: {
            'X-Firebase-AppCheck': appCheckTokenResponse.token,
        }
    });
    console.log(apiResponse);
    // Handle response from your backend.
  };




  onMounted(async () => {
    //getToken Services
    self.FIREBASE_APPCHECK_DEBUG_TOKEN = true;
    const appCheck = initializeAppCheck(app, {
      provider: new ReCaptchaEnterpriseProvider(ID_TOKEN),
      //isTokenAutoRefreshEnabled: true // Set 
    })

    await callApiWithAppCheckExample(appCheck)

 







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
