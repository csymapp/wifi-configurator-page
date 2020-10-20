
<template>
    <q-layout>
        <q-page-container>
            <q-page class="window-height window-width row justify-center items-center" style="background: linear-gradient(#8274C5, #5A4A9F);">
                <div class="column q-pa-lg">
                    <div class="row">
                        <q-card square class="shadow-24" style="width:300px;height:485px;">
                            <q-card-section class="bg-deep-purple-7">
                                <!-- <h4 class="text-h5 text-white q-my-md text-center" >WiFi Configurator</h4> -->
                                <h4 class="text-h5 text-white q-my-md text-center">{{appName}}</h4>
                                <div class="text-center">WiFi Configuration</div>
                            </q-card-section>
                            <q-card-section>
                                <div class="text-center">{{deviceName}}</div>
                                <q-form class="q-px-sm q-pt-xl">
                                    <q-input square clearable v-model="username" type="text" label="Username" @focus="showError.username=false" :error="showError.username" :error-message="errorMessage.username">
                                        <template v-slot:prepend>
                                                  <q-icon name="person" />
</template>
              </q-input>
              <q-input square clearable v-model="password" type="password" label="Password" required @keyup.enter="login();" 
              @focus="showError.password=false"
              :error="showError.password"
          :error-message="errorMessage.password">
<template v-slot:prepend>
    <q-icon name="lock" />
</template>
              </q-input>
            </q-form>
          </q-card-section>
          <q-card-actions class="q-px-lg">
            <q-btn unelevated size="lg" color="purple-4" class="full-width text-white" label="Sign In" @click="login()"/>
          </q-card-actions>
        </q-card>
      </div>
    </div>
  </q-page>
</q-page-container>
</q-layout>
</template>

<script>
const axios = require('axios');
export default {
    name: 'Login',
    data() {
        return {
            username: '',
            password: '',
            root: "",
            appName: 'No Application',
            deviceName: 'anon',
            showError: {
                username: false,
                password: false
            },
            errorMessage: {
                password: "Please enter a password",
                username: "Please enter a username"
            }
        }
    },
    created() {
        this.logedIn();
        axios.get(this.root + '/app').then(data =>
            this.appName = data.data.app
        )
        axios.get(this.root + '/device').then(data =>
            this.deviceName = data.data.device
        )
    },
    methods: {
        logedIn() { //check if token is valid
            let token = window.localStorage.getItem('wifi-config-token') || '';
            let headers = {
                'content-type': 'application/json',
                'Authorization': `bearer ${token}`
            }
            axios.post(this.root + '/token', {}, { headers: headers }).then(response => {
                // window.localStorage.setItem('wifi-config-token', token);
                window.localStorage.setItem('wifi-config-token', response.data.token);
                this.$q.notify('Logging in');
                window.location.href = '/#/home'
            }).catch((error) => {

            })
        },
        login() {
            if (this.username === '') {
                this.showError.username = true;
                return
            }
            if (this.password === '') {
                this.showError.password = true;
                return
            }
            axios.post(this.root + '/login', { username: this.username, password: this.password }).then(response => {
                window.localStorage.setItem('wifi-config-token', response.data.token);
                this.logedIn();
            }).catch((error) => {
                try {
                    error = error.response.data.error
                } catch (err) {
                    error = 'unknown error'
                }
                this.errorMessage.password = error
                this.showError.password = true;

            })
        }
    }
}
</script>

<style>

</style>