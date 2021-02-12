<template>
  <div id="q-app">
    <router-view />
  </div>
</template>
<script src="https://www.gstatic.com/firebasejs/8.2.6/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.2.6/firebase-analytics.js"></script>

<script>
// Firebase App (the core Firebase SDK) is always required and
// must be listed before other Firebase SDKs
import firebase from 'firebase/app'

// Add the Firebase products that you want to use
import 'firebase/auth'
import 'firebase/firestore'

const firebaseConfig = {
  apiKey: 'AIzaSyDVPrQ7K3x5_D-abkNznd_Yh0QX7EwQpL4',
  authDomain: 'testowlie.firebaseapp.com',
  databaseURL: 'https://testowlie-default-rtdb.europe-west1.firebasedatabase.app',
  projectId: 'testowlie',
  storageBucket: 'testowlie.appspot.com',
  messagingSenderId: '109574607804',
  appId: '1:109574607804:web:20256703c420cc408651b9',
  measurementId: 'G-527G6KCLC4'
}
// Initialize Firebase
firebase.initializeApp(firebaseConfig)
// firebase.analytics()

export default {
  name: 'App'
}
</script>
