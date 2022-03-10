<template>
    <div class="p-login_content elevation-7">
        <v-row class="p-login_row">
            <v-col class="pa-0 col-sm-6 col-12">
                <div class="p-login_intro">
                    <img src="~/assets/images/img_login-background.png" class="p-login_background">
                    <img src="~/assets/images/img_members-cloud-white.png" width="225px" height="42px" class="p-login_logo">
                    <!-- <div class="p-login_intro-text">
                        <h1 class="heading">
                            <span v-html="$t('reminder.back_to_login')"></span>
                            <v-icon
                                dark
                                right
                                large
                                class="icon"
                            >
                                mdi-undo-variant
                            </v-icon>
                        </h1>
                        <p v-html="$t('reminder.sign_up')"></p>
                    </div> -->
                </div>
            </v-col>
            <v-col class="pa-0 col-sm-6 col-12">
                <!-- Reset Password -->
                <div v-if="e1 == 1" class="p-login_form">
                    <v-form
                        ref="form1"
                        v-model="valid"
                        lazy-validation
                        @submit.prevent="reminder"
                    >
                        <v-card-title class="flex-column align-start">
                            <div class="c-link mb-3">
                                <NuxtLink :to="localePath('/')" class="c-link_icon c-body_01--bold">
                                    <img src="~/assets/images/ic_arrow-left-outline.svg">
                                    {{ $t('reminder.back_to_signin') }}
                                </NuxtLink>
                            </div>
                            <h2 class="mb-2 c-heading_01--black">
                                {{$t('reminder.password_reset')}}
                            </h2>
                            <p class="c-body c-body_02--regular">{{$t('reminder.instruction')}}</p>
                        </v-card-title>
                        <v-container fluid>
                            <v-row>
                                <v-col cols="12">
                                    <div class="mb-3 c-body c-body_02--medium">
                                        <label>{{$t('reminder.email_address')}}</label>
                                    </div>
                                    <v-text-field
                                        class="v-text-field-custom"
                                        v-model="email"
                                        type="email"
                                        autocomplete="off"
                                        solo
                                    />
                                </v-col>
                            </v-row>
                            <v-row>
                                <v-col cols="12" class="text-center">
                                    <button
                                        type="submit"
                                        :loading="loading1"
                                        class="c-btn_main c-btn submit-btn"
                                    >
                                        {{$t('reminder.reset')}}
                                    </button>
                                </v-col>
                            </v-row>
                        </v-container>
                    </v-form>
                </div>
                <!-- Reset Password Success -->
                <div v-else-if="e1 == 3" class="p-reminder_message">
                    <v-card-title class="flex-column align-start">
                        <div class="c-link mb-3">
                            <NuxtLink :to="localePath('/')" class="c-link_icon c-body_01--bold">
                                <img src="~/assets/images/ic_arrow-left-outline.svg">
                                {{ $t('reminder.back_to_signin') }}
                            </NuxtLink>
                        </div>
                        <h2 class="mb-2 c-heading_01--black">
                            {{$t('reminder.forgot_your_password')}}
                        </h2>
                        <p class="c-body c-body_02--regular">{{$t('reminder.reset_success')}}</p>
                    </v-card-title>
                </div>
                <!-- Set New Password -->
                <div v-else-if="e1 == 2" class="p-login_form">
                    <v-form
                        ref="form2"
                        v-model="valid"
                        lazy-validation
                        @submit.prevent="set_password"
                    >
                        <v-card-title class="flex-column align-start">
                            <div class="c-link mb-3">
                                <NuxtLink :to="localePath('/')" class="c-link_icon c-body_01--bold">
                                    <img src="~/assets/images/ic_arrow-left-outline.svg">
                                    {{ $t('reminder.back_to_signin') }}
                                </NuxtLink>
                            </div>
                            <h2 class="mb-2 c-heading_01--black">
                                <p>{{$t('reminder.set_password')}}</p>
                            </h2>
                        </v-card-title>
                        <v-container fluid class="p-login_content-inner">
                            <v-row>
                                <v-col cols="12 py-0">
                                    <div class="mb-3 c-body c-body_02--medium">
                                        <label>{{$t('reminder.temp_password')}}</label>
                                    </div>
                                    <v-text-field class="v-text-field-custom" v-model="temp_pwd" :type="text" label="" solo />
                                </v-col>
                            </v-row>
                            <v-row>
                                <v-col cols="12 py-0">
                                    <div class="mb-3 c-body c-body_02--medium">
                                        <label>{{$t('reminder.new_password')}}</label>
                                    </div>
                                    <v-text-field
                                        class="v-text-field-custom"
                                        v-model="login_pwd"
                                        :append-icon="password_show ? 'mdi-eye' : 'mdi-eye-off'"
                                        :rules="[rules.required, rules.password_min]"
                                        :type="password_show ? 'text' : 'password'"
                                        label=""
                                        :hint="$t('reminder.rule')"
                                        counter
                                        solo
                                        @click:append="password_show = !password_show"
                                    />
                                </v-col>
                            </v-row>
                            <v-row>
                                <v-col cols="12 pt-0">
                                    <div class="mb-3 c-body c-body_02--medium">
                                        <label>{{$t('reminder.conf_password')}}</label>
                                    </div>
                                    <v-text-field
                                        class="v-text-field-custom"
                                        v-model="login_pwd2"
                                        :append-icon="password_show2 ? 'mdi-eye' : 'mdi-eye-off'"
                                        :rules="[
                                            rules.required,
                                            rules.password_min,
                                            rules.password2,
                                        ]"
                                        :type="password_show2 ? 'text' : 'password'"
                                        label=""
                                        :hint="$t('reminder.rule')"
                                        counter
                                        solo
                                        @click:append="password_show2 = !password_show2"
                                    />
                                </v-col>
                            </v-row>
                            <v-row>
                                <v-col cols="12">
                                    <button
                                        type="submit"
                                        color="success"
                                        :loading="loading2"
                                        class="c-btn_main c-btn submit-btn"
                                    >
                                        {{$t('reminder.submit')}}
                                    </button>
                                </v-col>
                            </v-row>
                        </v-container>
                    </v-form>
                </div>
                <!-- Set New Password Success -->
                <div v-else-if="e1 == 4" class="p-reminder_message">
                    <v-card-title class="flex-column align-start">
                        <div class="c-link mb-3">
                            <NuxtLink :to="localePath('/')" class="c-link_icon c-body_01--bold">
                                <img src="~/assets/images/ic_arrow-left-outline.svg">
                                {{ $t('reminder.back_to_signin') }}
                            </NuxtLink>
                        </div>
                        <h2 class="mb-2 c-heading_01--black">
                            {{$t('reminder.update_ok')}}
                        </h2>
                        <p class="c-body c-body_02--regular">{{$t('reminder.update_success')}}</p>
                    </v-card-title>
                </div>
            </v-col>
        </v-row>
    </div>
</template>

<script>
export default {
    auth: false,
    data() {
        return {
            token: '',
            e1: 1,
            valid: true,
            email: '',
            password_show: false,
            login_pwd: '',
            password_show2: false,
            login_pwd2: '',
            temp_pwd: '',
            loading1: false,
            loading2: false,
            rules: {
                required: (value) => !!value || this.$i18n.t('reminder.required'),
                password_min: (v) => v.length >= 8 || this.$i18n.t('reminder.word_count'),
                password2: (v) => v === this.login_pwd || this.$i18n.t('reminder.conf_error')
            }
        };
    },
    created() {
        this.token = this.$route.query.token;
        if (this.token) {
            this.$store.dispatch(
                'snackbar/setMessage',
                this.$i18n.t('reminder.not_entered')
            );
            this.$store.dispatch('snackbar/snackOn');
            this.e1 = 2;
        }
    },
    methods: {
        login() {
            this.$router.push(this.localePath('/'));
        },
        reminder() {
            this.loading1 = true;
            const self = this;
            this.$store.$auth.ctx.$axios
                .post('/rcms-api/1/reminder', {
                    email: this.email
                })
                .then(function (response) {
                    if (response.data.errors.length === 0) {
                        self.$store.dispatch(
                            'snackbar/setMessage',
                            this.$i18n.t('reminder.password_sent')
                        );
                        self.$store.dispatch('snackbar/snackOn');
                    }
                    self.e1 = 3;
                    self.loading1 = false;
                })
                .catch(function () {
                    self.$store.dispatch('snackbar/setError', this.$i18n.t('reminder.invalid_email'));
                    self.$store.dispatch('snackbar/snackOn');
                    self.loading1 = false;
                });
        },
        set_password() {
            if (this.$refs.form2.validate() && this.token) {
                console.log('reset');
                this.loading2 = true;
                const self = this;
                self.$auth.ctx.$axios
                    .post('/rcms-api/1/reminder', {
                        token: this.token,
                        login_pwd: this.login_pwd,
                        temp_pwd: this.temp_pwd
                    })
                    .then(() => {
                        self.$store.dispatch(
                            'snackbar/setMessage',
                            this.$i18n.t('reminder.already_updated')
                        );
                        self.$store.dispatch('snackbar/snackOn');
                        self.$router.push('/');
                        self.loading2 = false;
                        self.e1 = 4;
                    })
                    .catch(function (error) {
                        self.$store.dispatch('snackbar/setError', error.response.data.errors?.[0].message);
                        self.$store.dispatch('snackbar/snackOn');
                        self.loading2 = false;
                    });
            }
        }
    }
};
</script>
