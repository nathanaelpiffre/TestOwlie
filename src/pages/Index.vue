<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <div class="q-pa-sm q-gutter-y-sm">
        <q-toolbar>
          <q-btn flat round dense icon="menu" class="q-mr-sm" @click="leftDrawerOpen = !leftDrawerOpen"/>
          <q-space />
          <q-btn flat round> <q-icon class="iconTop" :name=mdiBell /></q-btn>
          <q-btn flat round> <q-icon class="iconTop" :name=mdiShareVariant /></q-btn>
          <q-btn flat round> <q-icon class="iconTop" :name=mdiMagnify /></q-btn>
        </q-toolbar>
        <q-toolbar inset>
          <div v-if="!addInput">
            <q-toolbar-title>Owlie Todolist</q-toolbar-title>
          </div>
          <div v-if="addInput">
            <div class="row float-right bgcolor">
              <q-input  v-model="newTask" color="white" label="Ajouter un élément" label-color="secondary" @keyup.enter="addTask()" />
              <q-btn fab icon="add" class="q-ml-md" color="secondary" @click.native="addTask()"> </q-btn>
            </div>
          </div>
        </q-toolbar>
      </div>
    </q-header>
    <q-page-container>
      <q-page class="q-pt-lg">
        <div>
          <div v-if="!connected">
            <p class="textAccueil">Afin d'accéder à votre Todolist <br> merci de bien vouloir vous authentifier </p>
            <q-btn
              class="fixed-center text-bold"
              id="ButtonAuth"
              color="secondary"
              size="s"
              label="M'Authentifier"
              @click="leftDrawerOpen = !leftDrawerOpen"
            ></q-btn>
          </div>
          <q-drawer v-model="leftDrawerOpen" show-if-above bordered content-class="bg-grey-1">
            <div v-if="!connected">
              <q-item-section class="q-pa-md">
                <q-item-label class="text-black text-h6"> Me connecter </q-item-label>
                <q-item-label caption> Entrez vos identifiants pour vous connecter </q-item-label>
              </q-item-section>
              <q-item-section class="q-pa-md">
                <q-input filled v-model="email" placeholder="Email" class="q-pb-md" />
                <q-input filled v-model="password" placeholder="Mot de Passe" @keyup.enter="Connection()" class="q-pb-md" :type="isPwd ? 'password' : 'text'">
                  <template v-slot:append>
                    <q-icon
                      :name="isPwd ? 'visibility_off' : 'visibility'"
                      class="cursor-pointer"
                      @click="isPwd = !isPwd"
                    />
                  </template>
                </q-input>
                <q-btn color="secondary" class="q-mb-md text-left" size="s" label="Me connecter" @click.native="Connection()" ></q-btn>
                <q-btn color="grey" class="q-mb-md text-grey-14" size="s" label="Créer un compte" @click.native="CreateAccount()" ></q-btn>
              </q-item-section>
            </div>
            <div v-if="connected">
              <q-item-section class="q-pa-md">
                <q-item-label class="text-black text-h6 q-pb-md"> Bonjour {{ email }} !</q-item-label>
                <q-btn color="secondary" class="q-mb-md text-left" size="md" label="Me deconnecter" @click.native="Disconect()" ></q-btn>
              </q-item-section>
            </div>
          </q-drawer>
        </div>
        <div v-if="connected">
          <div v-if="!addInput">
            <q-page-sticky position="top-left" :offset="[0, 30]">
             <q-btn fab icon="add" class="q-ml-md" color="secondary" @click.native="addTaskInput()"> </q-btn>
            </q-page-sticky>
          </div>
          <div class="float-right">
            <div class = "q-pa-md" v-if="ListTasks.length == 0">
              <p class="text-h6">Aucun élément</p>
            </div>
            <div v-if="ListTasks.length != 0">
              <q-list highlight class="col">
                <q-item v-for="(item, index) in ListTasks" :key="index">
                  <q-expansion-item v-model="item.expanded" class="buttonTask bg-grey-3">
                    <template v-slot:header >
                      <q-item-section>
                        {{ item.taskName }}
                      </q-item-section>

                      <q-item-section side top>
                        <q-checkbox v-model="item.model" class="checkbox" @click.native="deleteTask(index, item.id)"/>
                      </q-item-section>
                    </template>
                    <q-card>
                      <q-card-section>
                        {{ item.desc }}
                        <q-popup-edit v-model="item.desc" content-class="q-ma-md bg-grey-1 text-white">
                          <q-input v-model="item.desc" dense autofocus counter @keyup.enter="editDesc(index, item.desc, item.id)"  />
                        </q-popup-edit>
                      </q-card-section>
                    </q-card>
                  </q-expansion-item>
                </q-item>
              </q-list>
            </div>
          </div>
        </div>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<style>
</style>

<script>
import firebase from 'firebase/app'
// eslint-disable-next-line no-unused-vars
// import { mdiPlus } from '@quasar/extras/mdi-v5'
import { mdiBell, mdiMagnify, mdiShareVariant, mdiPlus } from '@quasar/extras/mdi-v5'

var db = firebase.firestore()

export default {
  name: 'PageIndex',
  data () {
    return {
      ListTasks: [],
      newTask: '',
      email: '',
      password: '',
      addInput: false,
      leftDrawerOpen: false,
      isPwd: true,
      connected: false
    }
  },
  created () {
    this.mdiPlus = mdiPlus
    this.mdiBell = mdiBell
    this.mdiMagnify = mdiMagnify
    this.mdiShareVariant = mdiShareVariant
  },
  methods: {
    addTask: function () {
      // var idInsert = 0
      var _this = this
      var task = {
        email: this.email,
        taskName: this.newTask,
        model: false,
        desc: 'Remplir la description',
        expanded: false
      }
      db.collection('tasks').add(task).then(function (returnedTask) {
        task.id = returnedTask.id
        _this.ListTasks.push(task)
        console.log(task.id)
      })
      this.newTask = ''
      this.addInput = false
    },
    editDesc (index, newdesc, id) {
      this.ListTasks[index].desc = newdesc
      db.collection('tasks').doc(id).update({
        desc: newdesc
      })
    },
    deleteTask (index, id) {
      console.log(id)
      db.collection('tasks').doc(id).delete()
      this.ListTasks.splice(index, 1)
    },
    Connection () {
      firebase.auth().signInWithEmailAndPassword(this.email, this.password)
        .then((userCredential) => {
          // eslint-disable-next-line no-unused-vars
          var user = userCredential.user
          this.connected = true
          console.log(true)

          db.collection('tasks').where('email', '==', this.email).get().then((snapshot) => {
            snapshot.docs.forEach(doc => {
              this.ListTasks.push({
                email: doc.data().email,
                taskName: doc.data().taskName,
                model: doc.data().model,
                desc: doc.data().desc,
                expanded: doc.data().expanded,
                id: doc.id
              })
            })
          })
        })
        .catch((error) => {
          // eslint-disable-next-line no-unused-vars
          var errorCode = error.code
          // eslint-disable-next-line no-unused-vars
          var errorMessage = error.message
        })
    },
    CreateAccount () {
      firebase.auth().createUserWithEmailAndPassword(this.email, this.password)
        .then((userCredential) => {
          // eslint-disable-next-line no-unused-vars
          var user = userCredential.user
          this.connected = true
        })
        .catch((error) => {
          // eslint-disable-next-line no-unused-vars
          var errorCode = error.code
          // eslint-disable-next-line no-unused-vars
          var errorMessage = error.message
        })
    },
    Disconect () {
      firebase.auth().signOut().then(() => {
        // Sign-out successful.
        this.email = ''
        this.password = ''
        this.connected = false
        this.ListTasks = []
        this.addInput = false
        // eslint-disable-next-line handle-callback-err
      }).catch((error) => {
        // An error happened.
      })
    },
    addTaskInput () {
      this.addInput = true
    }
  }
}
</script>
