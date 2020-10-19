<template>
    <q-layout view="hHh lpR fFf">
    
        <q-header elevated class="bg-primary text-white" height-hint="98">
            <q-toolbar>
    
    
                <q-toolbar-title>
                    <q-avatar>
                        <img src="~assets/wifi-config.png">
                    </q-avatar>
                    {{appName}}
                </q-toolbar-title>
                <q-btn dense flat round icon="logout" @click="logout" />
            </q-toolbar>
    
            <q-tabs align="left">
                <q-route-tab to="#" label="Networks" @click="showNetworks();" icon="wifi" />
                <q-route-tab to="#" label="Users" @click="showUsers();" icon="people" />
                <q-route-tab to="#" label="Profile" @click="showProfile();" icon="person" />
            </q-tabs>
        </q-header>
    
        <q-page-container>
            <!-- <router-view /> -->
            <div class="text-center text-grey text-bold q-mb-md q-mt-md"><span>Configurations for </span><span class="text-black">{{deviceName}}</span></div>
            <div class="q-ml-md q-mr-md">
                <div v-if="showingNetworks">
                    <div class="row q-mb-md">
                        <q-input v-model="newNetwork.SSID" placeholder="Add New Network" class="col" />
                        <q-input v-model="newNetwork.password" placeholder="Password" class="col" type="password" />
                        <q-btn size="sm" color="primary" label="Add" />
                    </div>
                    <div class="row">
                        <q-list bordered separator class="col">
                            <q-item-label header>Saved Networks</q-item-label>
                            <q-item v-for="(network, index) in networks">
                                <q-item-section avatar>
                                    <q-icon color="primary" name="signal_wifi_off" />
                                </q-item-section>
                                <q-item-section>{{network[0]}}</q-item-section>
                                <q-item-section side>
                                    <!-- <q-item-label caption>meta</q-item-label> -->
                                    <q-icon name="check"></q-icon>
                                </q-item-section>
                            </q-item>
    
                            <q-item clickable v-ripple>
                                <q-item-section>
                                    <q-item-label>Item with caption</q-item-label>
                                    <q-item-label caption>Caption</q-item-label>
                                </q-item-section>
                            </q-item>
    
                            <q-item clickable v-ripple>
                                <q-item-section>
                                    <q-item-label overline>OVERLINE</q-item-label>
                                    <q-item-label>Item with caption</q-item-label>
                                </q-item-section>
                            </q-item>
                        </q-list>
                    </div>
                </div>
                <div v-if="showingUsers">
                    <div class=" q-mb-md text-center">
                        <q-input v-model="newUser.userName" ref="username" placeholder="UserName" class="col" type="text" :error="showError.newUser.userName" :error-message="errorMessage.newUser.userName" @focus="showError.newUser.userName=false" @keydown="showError.newUser.userName=false"
                            @keyup.enter="addUser" />
                        <q-input v-model="newUser.password" ref="password" placeholder="Password" class="col" type="password" :error="showError.newUser.password" :error-message="errorMessage.newUser.password" @focus="showError.newUser.password=false" @keydown="showError.newUser.password=false"
                            @keyup.enter="addUser" />
                        <q-btn size="sm" color="primary" label="Add User" @click="addUser" />
                    </div>
                    <div class="row">
                        <q-list bordered separator highlight class="col">
                            <q-item-label header>Users</q-item-label>
                            <q-item clickable v-for="(user, index) in users">
                                <q-item-section avatar>
                                    <q-icon color="primary" name="person" />
                                </q-item-section>
                                <q-item-section>{{user}}</q-item-section>
                                <q-item-section side>
                                    <!-- <q-item-label caption>meta</q-item-label> -->
                                    <q-icon name="close" color="red" @click="removeUser(user)"></q-icon>
                                </q-item-section>
                            </q-item>
                        </q-list>
                    </div>
                </div>
                <div v-if="showingProfile">
                    <div class=" q-mb-md text-center">
                        <q-input v-model="user.name" placeholder="New UserName" class="col" readonly />
                        <q-input v-model="user.password" ref="password" placeholder="Old Password" class="col" type="password" :error="showError.user.password" :error-message="errorMessage.user.password" @focus="showError.user.password=false" @keydown="showError.user.password=false"
                            @keyup.enter="changePassword" />
                        <q-input v-model="user.newPassword" placeholder="New Password" class="col" type="password" :error="showError.user.newPassword" :error-message="errorMessage.user.newPassword" @focus="showError.user.newPassword=false" @keydown="showError.user.newPassword=false"
                            @keyup.enter="changePassword" />
                        <q-btn size="sm" color="primary" label="Change Password" @click="changePassword" />
                    </div>
                </div>
            </div>
        </q-page-container>
    
        <q-footer elevated class="bg-grey-8 text-white">
            <q-toolbar>
                <q-toolbar-title class="text-center">
                    WiFi-Configurator
                </q-toolbar-title>
            </q-toolbar>
        </q-footer>
    
    </q-layout>
</template>

<script>
const axios = require('axios')
export default {
    name: 'HomeLayout',
    components: {},
    created() {
        this.logedIn();
        axios.get(this.root + '/app').then(data =>
            this.appName = data.data.app
        )
        axios.get(this.root + '/device').then(data =>
            this.deviceName = data.data.device
        )
        this.showNetworks();

    },
    data() {
        return {
            root: "http://localhost:3074",
            appName: 'No Application',
            deviceName: 'anon',
            user: {
                name: '',
                password: '',
                newPassword: ''
            },
            newUser: {
                userName: '',
                password: ''
            },
            showingNetworks: true,
            showingUsers: false,
            showingProfile: false,
            newNetwork: {
                SSID: '',
                password: ''
            },
            networks: [],
            users: [],
            showError: {
                user: {
                    password: false,
                    newPassword: false,
                },
                newUser: {
                    password: false,
                    userName: false,
                }
            },
            errorMessage: {
                user: {
                    password: '',
                    newPassword: '',
                },
                newUser: {
                    password: '',
                    userName: '',
                }
            },
        }
    },
    methods: {
        showNetworks() {
            this.showingUsers = false
            this.showingProfile = false
            this.showingNetworks = true
            axios.get(this.root + '/savednetworks', { headers: this.headers() }).then((response) => {
                let ret = [];
                let networks = response.data.networks
                for (let i in networks) {
                    let network = networks[i]
                    ret.push([Object.keys(network)[0], Object.values(network)[0]])
                }
                ret.sort() //(a, b) => a[0] - b[0])
                console.log(ret)
                this.networks = ret
            })
            this.scanNetworks();
            //
        },
        scanNetworks() {
            axios.get(this.root + '/availablenetworks', { headers: this.headers() }).then((response) => {
                let ret = [];
                let networks = response.data.networks
                for (let i in networks) {
                    let network = networks[i]
                    console.log(network)
                    // ret.push([Object.keys(network)[0], Object.values(network)[0]])
                }
                // ret.sort((a, b) => a - b)
                // this.networks = ret
            })
        },
        showUsers() {
            this.showingProfile = false
            this.showingNetworks = false
            this.showingUsers = true
            axios.get(this.root + '/users', { headers: this.headers() }).then((response) => {
                this.users = response.data.users.filter(user => user !== this.user.name)
            })
        },
        showProfile() {
            this.showingUsers = false
            this.showingNetworks = false
            this.showingProfile = true
            this.$nextTick(() => this.$refs.password.focus());
        },
        headers() {
            let token = window.localStorage.getItem('wifi-config-token') || '';
            let headers = {
                'content-type': 'application/json',
                'Authorization': `bearer ${token}`
            }
            return headers
        },
        logedIn() {
            axios.post(this.root + '/token', {}, { headers: this.headers() }).then(response => {
                console.log(response.data)
                window.localStorage.setItem('wifi-config-token', response.data.token);
                this.user.name = response.data.username;
            }).catch((error) => {
                this.logout();
            })
        },
        logout() {
            window.localStorage.removeItem('wifi-config-token');
            this.$q.notify('Logging out');
            window.location.href = '/'
        },
        changePassword() {
            let hasError = false;
            if (this.user.password === '') {
                this.showError.user.password = true;
                this.errorMessage.user.password = 'Old Password is required'
                hasError = true;
            }
            if (this.user.newPassword === '') {
                this.showError.user.newPassword = true;
                this.errorMessage.user.newPassword = 'New Password is required'
                hasError = true;
            }
            if (hasError) {
                return
            }
            this.showError.user.password = false;
            this.showError.user.newPassword = false;
            let params = {
                password: this.user.password,
                newPassword: this.user.newPassword,
            }

            axios.post(this.root + '/password', params, { headers: this.headers() }).then(response => {
                // window.localStorage.setItem('wifi-config-token', token);
                console.log(response.data.msg)
                this.$q.notify({
                    type: 'positive',
                    message: response.data.msg || response.data.message
                })
                this.user.password = ''
                this.user.newPassword = ''

            }).catch((error) => {
                let errMsg = 'unknown error'
                try {
                    errMsg = error.response.data.error;
                    // console.log(errMsg)
                } catch (error) {

                }
                if (!errMsg) {
                    errMsg = 'unknown error'
                }
                this.showError.user.password = true;
                this.errorMessage.user.password = errMsg
                this.$q.notify({
                    type: 'negative',
                    message: errMsg
                })
            })

        },
        addUser() {
            let hasError = false;
            if (this.newUser.password === '') {
                this.showError.newUser.password = true;
                this.errorMessage.newUser.password = 'Password is required'
                hasError = true;
            }
            if (this.newUser.userName === '') {
                this.showError.newUser.userName = true;
                this.errorMessage.newUser.userName = 'Username is required'
                hasError = true;
            }
            if (hasError) {
                return
            }
            this.showError.newUser.password = false;
            this.showError.newUser.userName = false;

            let params = {
                username: this.newUser.userName,
                password: this.newUser.password,
            }

            axios.post(this.root + '/user', params, { headers: this.headers() }).then(response => {
                this.$q.notify({
                    type: 'positive',
                    message: response.data.msg || response.data.message
                })
                this.showUsers();
                for(let i in this.newUser){
                    this.newUser[i] = ''
                }
            }).catch((error) => {
                let errMsg = 'unknown error'
                try {
                    errMsg = error.response.data.error;
                    // console.log(errMsg)
                } catch (error) {

                }
                if (!errMsg) {
                    errMsg = 'unknown error'
                }
                this.showError.user.password = true;
                this.errorMessage.user.password = errMsg
                this.$q.notify({
                    type: 'negative',
                    message: errMsg
                })
            })

        },
        removeUser(userName) {
            this.$q.dialog({
                parent: this,
                title: 'Remove Users',
                message: `Are you sure you want to delete ${userName}`,
                ok: 'Yes',
                cancel: 'No',
            }).onOk(() => {
                axios.delete(this.root + `/user/${userName}`, { headers: this.headers() }).then(response => {
                    this.$q.notify({
                        type: 'positive',
                        message: response.data.msg || response.data.message
                    })
                    this.showUsers();

                }).catch((error) => {
                    let errMsg = 'unknown error'
                    try {
                        errMsg = error.response.data.error;
                    } catch (error) {

                    }
                    if (!errMsg || errMsg == 'undefined' || errMsg === undefined || Object.keys(errMsg).length === 0) {
                        errMsg = 'unknown error'
                    }
                    this.$q.notify({
                        type: 'negative',
                        message: errMsg
                    })
                })
            }).onCancel(() => {
            }).onDismiss(() => {
            })


        }
    }
}
</script>