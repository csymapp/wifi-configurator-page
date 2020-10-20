<template>
    <div class="q-pa-md q-gutter-sm">
        <!-- <q-dialog v-model="basic" transition-show="rotate" transition-hide="rotate">
                    <q-card>
                        <q-card-section>
                            <div class="text-h6">Terms of Agreement basic</div>
                        </q-card-section>
            
                        <q-card-section class="q-pt-none">
                          {{values}}
                        </q-card-section>
            
                        <q-card-actions align="right">
                            <q-btn flat label="Decline" color="primary" v-close-popup />
                            <q-btn flat label="Accept" color="primary" v-close-popup />
                        </q-card-actions>
                    </q-card>
                </q-dialog> -->
    
        <q-dialog persistent v-model="fixed">
            <q-card>
                <q-card-section>
                    <div class="text-h6">{{values[0]}}</div>
                </q-card-section>
                <q-card-section class="q-pt-none">
                    {{values}}
                </q-card-section>
    
                <q-separator />
                <div class="row">
                    <q-list bordered separator highlight class="col">
                        <q-item-label header>Saved Passwords</q-item-label>
                        <q-item clickable v-for="(password, index) in values[1]">
                            <q-item-section avatar>
                                <q-icon color="primary" name="lock" />
                            </q-item-section>
                            <q-item-section>{{password}}</q-item-section>
                            <q-item-section side>
                                <q-icon v-if="values[4]" name="link_off" color="red " @click="self.disconnectFromWiFi(values[0], index)"></q-icon>
                                <q-icon v-else-if="values[3]" name="link" color="primary" @click="self.connectToWiFi(values[0], password, index)"></q-icon>
                            </q-item-section>
                            <q-item-section side>
                                <q-icon name="delete" color="red" @click="self.deleteWiFiPassword(values[0], password, index)"></q-icon>
                            </q-item-section>
                        </q-item>
                    </q-list>
                </div>
                <q-card-section style="max-height: 50vh" class="scroll">
                </q-card-section>
    
                <q-separator />
    
                <q-card-actions align="right">
                    <q-btn flat label="Close" color="primary" @click="closeDialog" />
                </q-card-actions>
            </q-card>
        </q-dialog>
    </div>
</template>

<script>
export default {
    props: {
        values: {
            type: Array,
            required: true
        },
        showWiFiPasswords: {
            type: Array,
            required: true
        },
        index: {
            type: Number
        },
        self: {
            type: Object
        }
    },
    data() {
        return {
            fixed: true
        }
    },
    methods: {
        closeDialog() {
            console.log(this.showWiFiPasswords)
            console.log(this.values)
            this.showWiFiPasswords.splice(this.index, 1, false)
            console.log(this.showWiFiPasswords)
        }
    }
}
</script>