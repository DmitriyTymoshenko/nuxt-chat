<template>
    <v-layout
            column
            justify-center
            align-center
    >
        <v-flex xs12 sm8>
            <v-card min-width="400">
                <v-snackbar
                        v-model="snackbar"
                        :timeout="6000"
                        top
                >
                    {{message}}
                    <v-btn color="pink" flat @click="snackbar = false">Close</v-btn>
                </v-snackbar>
                <v-card-title class="display-1">
                    Nuxt Chat
                </v-card-title>
                <v-card-text>
                    <v-form
                            ref="form"
                            v-model="valid"
                            lazy-validation
                    >
                        <v-text-field
                                v-model="name"
                                :counter="15"
                                :rules="nameRules"
                                label="Name"
                                required
                        ></v-text-field>

                        <v-text-field
                                v-model="room"
                                :rules="roomRules"
                                label="Room"
                                required
                        ></v-text-field>
                        <v-btn
                                :disabled="!valid"
                                color="success"
                                class="mr-4"
                                @click="submit"
                        >
                            Submit
                        </v-btn>
                        <!--                    <v-btn @click="message">Check conection</v-btn>-->
                    </v-form>
                </v-card-text>
            </v-card>

        </v-flex>
    </v-layout>
</template>

<script>
    import {mapMutations} from 'vuex'

    export default {
        name: 'index',
        layout: 'login',
        head: {
            title: 'Welcome to chat!'
        },
        data: () => ({
            snackbar: false,
            valid: false,
            message: '',
            name: '',
            room: '',
            nameRules: [
                v => !!v || 'Name is required',
                v => (v && v.length <= 10) || 'Name must be less than 15 characters',
            ],
            roomRules: [
                v => !!v || 'Room is required',

            ],

        }),
        sockets: {
            connect() {
                console.log(' IO connected on client')
            }
        },
        mounted() {
            const {message} = this.$route.query;
            if (message === 'noUser') {
                this.message = 'Enter data!'
            } else if ( message === 'leftChat') {
                this.message = 'You left chat!'
            }
            this.snackbar = !!this.message
        },
        methods: {
            ...mapMutations(['setUser']),
            submit() {
                if (this.$refs.form.validate()) {
                    const user = {
                        name: this.name,
                        room: this.room
                    };
                    this.$socket.emit('userJoined', user, data => {
                        if (typeof data === "string") {
                            console.error(data)
                        } else {
                            user.id = data.userId;
                            this.setUser(user);
                            this.$router.push('/chat')
                        }
                    })

                }
            },
        }
    }
</script>
