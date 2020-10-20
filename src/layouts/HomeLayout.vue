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
                <q-route-tab to="#" label="Device" @click="showDevice();" icon="devices_other" />
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
                        <q-input v-model="newNetwork.SSID" ref="ssid" placeholder="Add New Network" class="col" />
                        <q-input v-model="newNetwork.password" ref="ssidPassword" placeholder="Password" class="col" type="password" />
                        <q-btn size="sm" color="primary" label="Save" />
                    </div>
                    <div class="row">
                        <q-list bordered separator class="col" v-if="!scrollNetworkstoTop">
                            <q-item-label header>
                                <div class="row">
                                    Saved Networks
                                    <q-space />
                                    <q-icon color="primary" name="refresh" side @click="showNetworks();" />
                                </div>
                            </q-item-label>
                            <q-item clickable v-for="(values, index) in networksArray">
                                <q-item-section avatar>
                                    <q-icon color="primary" v-if="values[3]" name="wifi" />
                                    <q-icon color="black" v-else name="signal_wifi_off" />
                                </q-item-section>
                                <q-item-section>{{values[0]}}</q-item-section>
                                <q-item-section side>
                                    <q-icon v-if="values[4]" name="link_off" color="red" @click="disconnectFromWiFi(values[0], index)"></q-icon>
                                    <!-- <q-icon v-else-if="values[3]" name="link" color="primary"></q-icon> -->
                                </q-item-section>
                                <!-- <q-item-section side>
                                                                                                                        <q-icon v-if="values[2]" name="visibility" color="green"></q-icon>
                                                                                                                    </q-item-section> -->
                                <q-item-section side>
                                    <q-icon v-if="values[2]" name="visibility" color="green" @click="showNetworkPasswords(values, index)"></q-icon>
                                    <q-icon v-else name="save" color="green" @click="startSavingNetwork(values)"></q-icon>
                                </q-item-section>
                                <!-- <q-item-section side>
                                                                                                                        <q-icon v-if="values[2]" name="delete" color="red"></q-icon>
                                                                                                                        <q-icon v-else name="save" color="green" @click="startSavingNetwork(values)"></q-icon>
                                                                                                                    </q-item-section> -->
                                <visibility v-if=" showWiFiPasswords[index]" :values="values" :showWiFiPasswords="showWiFiPasswords" :index="index" :self="self" />
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
                                    <q-icon name="close" color="red" @click="removeUser(user)"></q-icon>
                                </q-item-section>
                            </q-item>
                        </q-list>
                    </div>
                </div>
                <div v-if="showingDevice">
                    <div class=" q-mb-md text-center">
                        <q-input v-model="deviceName" ref="device" placeholder="device name" class="col" type="text" :error="showError.device" :error-message="errorMessage.device" @focus="showError.device=false" @keydown="showError.device=false" @keyup.enter="changeDeviceName()"
                        />
                        <q-btn size="sm" color="primary" label="Change Device Name" @click="changeDeviceName" />
                    </div>
                    <!-- <div class="row q-mb-md flex-center" >
                            <q-btn size="sm" color="primary" label="Pause Internet Checks" class="text-center" @click="changeDeviceName" />
                        </div>
                        <div class="row q-mb-md flex-center">
                            <q-btn size="sm" color="primary" label="Restart Internet Checks" @click="changeDeviceName" />
                        </div> -->
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
const axios = require('axios');
import visibility from 'components/visibility.vue'

export default {
    name: 'HomeLayout',
    components: { visibility },
    created() {
        this.self = this;
        this.logedIn();
        axios.get(this.root + '/app').then(data =>
            this.appName = data.data.app
        )
        this.getDeviceName();
        this.showDevice();
        if (this.root === '') {
            this.root = window.location.origin
        }
        let url = this.root.replace(/http[s]?:\/\//, '')
        let socket;

        const sendNotification = (options = {}) => {
            let position = options.position || 'bottom-left'
            let message = options.msg || '...'
            let type = options.type || 'positive'
            message = {
                position,
                message,
                type
            }
            if (message.message !== '...') {
                this.$q.notify(message)
            }
        }

        const createSocketConnection = () => {
            socket = new WebSocket(`ws://${url}`, "protocolOne");
            socket.addEventListener('message', (event) => {
                let data;
                try {
                    data = JSON.parse(event.data);
                    if (data.msg === 'connected to wifi') {
                        this.showNetworks(false)
                    }
                    if (data.msg === 'disconnected from wifi') {
                        this.showNetworks(false)
                    }
                    // mounted 
                    if (new RegExp('^mounted').test(data.msg)) {
                        this.showNetworks(false)
                        this.fetchUsers()
                        this.logedIn();
                        this.getDeviceName()
                    }
                    sendNotification(data);
                } catch (error) {}
            });
        }
        const isOpen = (ws) => { return ws.readyState === ws.OPEN }
        createSocketConnection();

        setInterval(() => {
            if (!isOpen(socket)) {
                return createSocketConnection();
            }
            socket.send("ping");
        }, 2000)
    },
    data() {
        return {
            self: null,
            root: "",
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
            showingDevice: true,
            showingNetworks: false,
            showingUsers: false,
            showingProfile: false,
            newNetwork: {
                SSID: '',
                password: ''
            },
            networks: {

            },
            scrollNetworkstoTop: false,
            showWiFiFlag: false,
            showWiFiPasswords: [],
            networksArray: [],
            users: [],
            showError: {
                user: {
                    password: false,
                    newPassword: false,
                },
                newUser: {
                    password: false,
                    userName: false,
                },
                device: false
            },
            errorMessage: {
                user: {
                    password: '',
                    newPassword: '',
                },
                newUser: {
                    password: '',
                    userName: '',
                },
                device: ''
            },
        }
    },


    methods: {
        deleteWiFiPassword(ssid, password, index) {
            axios.delete(this.root + `/network/${ssid}/${password}`, { headers: this.headers() }).then(response => {
                this.$q.notify({
                    type: 'positive',
                    message: response.data.msg || response.data.message
                })
                this.showNetworks();
                this.showWiFiPasswords.splice(index, 1, false)

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
        },
        connectToWiFi(ssid, password, index) {
            axios.post(this.root + `/connection`, { ssid: ssid, password: password }, { headers: this.headers() }).then(response => {
                this.$q.notify({
                    type: 'positive',
                    message: response.data.msg || response.data.message
                })
                this.showNetworks();
                this.showWiFiPasswords.splice(index, 1, false)

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
        },
        disconnectFromWiFi(ssid, index) {
            axios.delete(this.root + `/connection`, { headers: this.headers() }).then(response => {
                this.$q.notify({
                    type: 'positive',
                    message: response.data.msg || response.data.message
                })
                this.showNetworks();
                this.showWiFiPasswords.splice(index, 1, false)

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
        },
        startSavingNetwork(values) {
            let ssid = values[0];
            this.newNetwork.SSID = ssid;
            this.$refs.ssidPassword.focus();
            this.scrollNetworkstoTop = true;
            setTimeout(() => { this.scrollNetworkstoTop = false }, 1000)
        },
        showNetworkPasswords(values, index) {
            let ssid = values[0];
            let passwords = values[1];
            this.showWiFiPasswords.splice(index, 1, true)
            this.showWiFiFlag = true;
        },

        getDeviceName() {
            axios.get(this.root + '/device').then(data =>
                this.deviceName = data.data.device
            )
        },
        showNetworks(refresh = true) {
            if (refresh) {
                this.showingUsers = false
                this.showingProfile = false
                this.showingDevice = false
                this.showingNetworks = true
            }
            this.networks = {}
            axios.get(this.root + '/savednetworks', { headers: this.headers() }).then((response) => {
                let ret = [];
                this.networksArray = [];
                let networks = response.data.networks
                let connectedNetworkSSID = response.data.connected;
                for (let i in networks) {
                    let network = networks[i];
                    let ssid = Object.keys(network)[0]
                    if (!this.networks[ssid]) {
                        this.networks[ssid] = {
                            saved: true,
                            passwords: [],
                            connected: ssid === connectedNetworkSSID ? true : false
                        }
                    }

                    let password = Object.values(network)[0]
                    this.networks[ssid].passwords.push(password);
                }
                this.scanNetworks();
            })

            //
        },
        scanNetworks() {
            axios.get(this.root + '/availablenetworks', { headers: this.headers() }).then((response) => {
                let ret = [];
                let networks = response.data.networks
                let connectedNetworkSSID = response.data.connected;
                for (let i in networks) {
                    let network = networks[i]
                    let ssid = network.ssid
                    if (!this.networks[ssid]) {
                        this.networks[ssid] = {
                            saved: false,
                            passwords: [],
                            connected: ssid === connectedNetworkSSID ? true : false
                        }
                    }
                    this.networks[ssid].available = true;
                    this.networks[ssid].connected = ssid === connectedNetworkSSID ? true : false
                }
                let tmp = JSON.parse(JSON.stringify(this.networksArray))
                let tmp1 = [];

                for (let network in this.networks) {
                    let passwords = this.networks[network].passwords
                    let saved = this.networks[network].saved
                    let available = this.networks[network].available || false;
                    let connected = this.networks[network].connected || false;
                    tmp1.push([network, passwords, saved, available, connected]);
                    tmp1.sort()
                }
                let compare = JSON.parse(JSON.stringify(tmp1));
                if (compare.toString() !== tmp.toString() && this.showingNetworks) {
                    this.networksArray = [];
                    this.networksArray = JSON.parse(JSON.stringify(tmp1))
                } else {

                }
                for (let i in this.networksArray) {
                    this.showWiFiPasswords[i] = false;
                }
            })
        },
        showUsers() {
            this.showingDevice = false
            this.showingProfile = false
            this.showingNetworks = false
            this.showingUsers = true
            this.fetchUsers()
        },
        fetchUsers() {
            axios.get(this.root + '/users', { headers: this.headers() }).then((response) => {
                this.users = response.data.users.filter(user => user !== this.user.name)
            })
        },
        showProfile() {
            this.showingDevice = false
            this.showingUsers = false
            this.showingNetworks = false
            this.showingProfile = true
            this.$nextTick(() => this.$refs.password.focus());
        },
        showDevice() {
            this.showingUsers = false
            this.showingNetworks = false
            this.showingProfile = false
            this.showingDevice = true
            this.getDeviceName();
            this.$nextTick(() => this.$refs.device.focus());
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
        changeDeviceName() {
            let hasError = false;
            if (this.deviceName === '') {
                this.showError.device = true;
                this.errorMessage.device = 'Device name is required'
                hasError = true;
            }
            if (hasError) {
                return
            }
            this.showError.device = false;
            this.showError.device = false;
            let params = {
                devicename: this.deviceName
            }

            axios.post(this.root + '/device', params, { headers: this.headers() }).then(response => {
                this.$q.notify({
                    type: 'positive',
                    message: response.data.msg || response.data.message
                })
                // this.user.password = ''
                // this.user.newPassword = ''

            }).catch((error) => {
                let errMsg = 'unknown error'
                try {
                    errMsg = error.response.data.error;
                } catch (error) {

                }
                if (!errMsg) {
                    errMsg = 'unknown error'
                }
                this.showError.device = true;
                this.errorMessage.device = errMsg
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
                for (let i in this.newUser) {
                    this.newUser[i] = ''
                }
            }).catch((error) => {
                let errMsg = 'unknown error'
                try {
                    errMsg = error.response.data.error;
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
            }).onCancel(() => {}).onDismiss(() => {})
        }
    }
}
</script>