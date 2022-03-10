<template>
    <client-only>
        <!-- Main Login -->
        <div v-if="!auth.loggedIn" class="p-login_content">
            <v-row class="p-login_row">
                <v-col class="pa-0 col-sm-6 col-12">
                    <div class="p-login_intro">
                        <img src="~/assets/images/img_login-background.png" class="p-login_background">
                        <img src="~/assets/images/img_members-cloud-logo.svg" width="225px" height="42px" class="p-login_logo">
                        <!-- <div class="p-login_intro-text">
                            <h1 class="heading">
                                <span v-html="$t('login.welcomeback')"></span>
                            </h1>
                            <p>{{$t('login.message')}}</p>
                            <p>
                                {{$t('login.demoaccount')}}<br>
                                <strong>{{$t('common.sitekey')}}：</strong> dev-nuxt-auth<br>
                                <strong>{{$t('common.id')}}：</strong>demo@kuroco-mail.app<br>
                                <strong>{{$t('common.password')}}：</strong>demo0512<br>
                            </p>
                        </div> -->
                    </div>
                </v-col>
                <v-col class="pa-0 col-sm-6 col-12">
                    <div class="p-login_form">
                        <form @submit.prevent="login">
                            <div class="login-screen lgn-left">
                                <v-card-title>
                                    <h2 class="mb-2 c-heading_01--black">
                                        {{ $t('login.greeting') }}
                                    </h2>
                                    <p class="c-body c-body_02--regular">{{$t('login.message')}}</p>
                                </v-card-title>
                                <v-card-text class="inner">
                                    <form @submit.prevent="login">
                                        <div class="mb-3 c-body c-body_02--medium">
                                            <label>{{$t('login.site_key')}}</label>
                                        </div>
                                        <v-text-field
                                            class="v-text-field-custom"
                                            v-model="sitekey"
                                            type="text"
                                            solo
                                        />
                                        <div class="mb-3 c-body c-body_02--medium">
                                            <label>{{$t('login.id_or_email')}}</label>
                                        </div>
                                        <v-text-field
                                            class="v-text-field-custom"
                                            v-model="form.email"
                                            type="email"
                                            solo
                                        />
                                        <div class="mb-3 c-body c-body_02--medium">
                                            <label>{{$t('common.password')}}</label>
                                        </div>
                                        <v-text-field
                                            class="v-text-field-custom"
                                            v-model="form.password"
                                            :type="show_pwd1 ? 'text' : 'password'"
                                            :append-icon="show_pwd1 ? 'mdi-eye' : 'mdi-eye-off'"
                                            solo
                                            @click:append="show_pwd1 = !show_pwd1"
                                        />
                                        <div class="mb-5" align="right">
                                            <NuxtLink :to="localePath('/reminder')" class="c-body_03--regular">
                                                {{ $t('login.forget_password') }}
                                            </NuxtLink>
                                        </div>
                                        <div class="text-center">
                                            <button
                                                type="submit"
                                                :loading="loading"
                                                class="c-btn_main c-btn submit-btn"
                                            >
                                                {{ $t('common.sign_in') }}
                                            </button>
                                        </div>
                                        <div class="pt-10 c-caption c-caption_01--regular">
                                            <span>{{$t('login.note')}}</span>
                                        </div>
                                    </form>
                                </v-card-text>
                            </div>
                        </form>
                    </div>
                </v-col>
            </v-row>
        </div>
        <!-- Main Dashboard -->
        <div v-else class="mypage">
            <v-carousel class="p-dashboard_banner">
                <v-carousel-item
                    v-for="(item, i) in items"
                    :key="i"
                    :src="item.src"
                    reverse-transition="fade-transition"
                    transition="fade-transition"
                />
            </v-carousel>

            <h1 class="text-left mt-5 pt-4">
                {{ $t('top.latest_articles') }}
            </h1>
            <v-topics :topics="topics" />

            <div class="text-center py-5 mt-4">
                <a
                    type="submit"
                    block
                    x-large
                    color="success"
                    class="c-btn c-btn_main c-btn_md c-btn_icon"
                    @click="back()"
                >
                    {{ $t('top.more_articles') }}
                    <v-icon
                        dark
                        right
                        class="icon"
                    >
                        mdi-arrow-right-drop-circle
                    </v-icon>

                </a>
            </div>

            <h1 class="text-left mt-5 pt-4">
                {{ $t('top.starred') }}
            </h1>
            <v-favourite :topics="favourite" />
            <div class="text-center py-5">
                <a
                    type="submit"
                    block
                    x-large
                    color="success"
                    class="c-btn c-btn_main c-btn_md c-btn_icon"
                    @click="linkFav()"
                >
                    {{ $t('top.more_starred') }}
                    <v-icon
                        dark
                        right
                        class="icon"
                    >
                        mdi-arrow-right-drop-circle
                    </v-icon>

                </a>
            </div>
        </div>
    </client-only>
</template>

<script>
import topicList from '../components/topics';
import topicGrid from '../components/topics_grid';

export default {
    components: {
        'v-topics': topicGrid,
        'v-favourite': topicList
    },
    middleware: 'auth',
    auth: false,
    created() {
        const myBody = document.getElementsByTagName('body')[0];
        if (this.$auth.loggedIn) {
            myBody.classList.add('p-dashboard');
            myBody.classList.remove('p-login');
        } else {
            myBody.classList.remove('p-dashboard');
            myBody.classList.add('p-login');
        }
        if (this.$route.name === 'index') {
            myBody.classList.add('p-home');
        }
    },
    data: () => ({
        sitekey: '',
        loading: false,
        show_pwd1: false,
        show_pwd2: false,
        form: {
            email: '',
            password: ''
        },
        items: [],
        thumbnail: [],
        topics: [],
        favourite: [],
        maxFavPerPage: 5 // This is fav list, for latest topic, go to below updateTopics() section, search for cnt=6
    }),
    computed: {
        user() {
            return this.$auth.user;
        },
        auth() {
            return this.$store.$auth;
        }
    },
    mounted() {
        this.topics = [];
        this.favourite = [];
        if (this.$auth.loggedIn) {
            this.updateTopics();
            this.updateFavourite();
        }
        const sitekey = localStorage.getItem('sitekey');
        if (sitekey != '') {
            this.sitekey = sitekey;
        } else {
            this.sitekey = 'dev-nuxt-auth';
        }
    },
    methods: {
        back() {
            this.$router.push(this.localePath('/topics_list/'));;
        },
        linkFav() {
            this.$router.push(this.localePath('/favourite/'));;
        },
        updateDesign() {
            const myBody = document.getElementsByTagName('body')[0];
            myBody.classList.add('p-dashboard');
            myBody.classList.remove('p-login');
        },
        updateTopics() {
            const url =
        '/rcms-api/1/topics?cnt=6';
            const self = this;
            this.$store.$auth.ctx.$axios
                .get(url)
                .then(function (response) {
                    const topics = [];
                    self.items = [];
                    for (const key in response.data.list) {
                        const item = response.data.list[key];
                        let fileurl = '';
                        let linkurl = '';
                        let thumbnail = '';
                        if (
                            item.hasOwnProperty('ext_col_02') &&
              item.ext_col_02.hasOwnProperty('url')
                        ) {
                            fileurl = item.ext_col_02.url;
                        }
                        if (
                            item.hasOwnProperty('ext_col_03') &&
              item.ext_col_03.hasOwnProperty('url')
                        ) {
                            linkurl = item.ext_col_03.url;
                        }
                        if (
                            item.hasOwnProperty('ext_col_08')
                        ) {
                            thumbnail = item.ext_col_08.url;
                        }
                        if (
                            item.hasOwnProperty('ext_col_08') &&
              item.ext_col_08.hasOwnProperty('url')
                        ) {
                            self.items.push({ src: item.ext_col_08.url + '?height=500px' });
                        }
                        topics.push({
                            date: item.ymd.substring(0, 10).replaceAll('-', '/'),
                            label: item.contents_type_nm,
                            link: item.subject,
                            id: item.topics_id,
                            icon: item.ext_col_01.key,
                            fileurl,
                            linkurl,
                            thumbnail,
                            edit: false
                        });
                    }
                    self.topics = topics;
                })
                .catch(function (error) {
                    self.$store.dispatch('snackbar/setError', error.response.data.errors?.[0].message);
                    self.$store.dispatch('snackbar/snackOn');
                });
        },
        updateFavourite() {
            const self = this;
            const favoritesUrl =
          '/rcms-api/1/favorites?member_id=' +
          this.$auth.user.member_id +
          '&module_type=topics';
            this.$store.$auth.ctx.$axios
                .get(favoritesUrl)
                .then(function (response) {
                    const topicIds = [];
                    for (const key in response.data.list) {
                        const item = response.data.list[key];
                        if (item.hasOwnProperty('module_id')) {
                            topicIds.push(item.module_id);
                        }
                    }
                    let url =
            '/rcms-api/1/topics?pageID=' +
            self.page +
            '&cnt=' +
            self.maxFavPerPage;

                    if (topicIds.length > 0) {
                        for (let i = 0; i < topicIds.length; ++i) {
                            url += '&id[]=' + topicIds[i];
                        }

                        self.$store.$auth.ctx.$axios
                            .get(url)
                            .then(function (response) {
                                const topics = [];
                                self.totalCnt = response.data.pageInfo.totalCnt;
                                for (const key in response.data.list) {
                                    const item = response.data.list[key];
                                    topics.push({
                                        date: item.inst_ymdhi
                                            .substring(0, 10)
                                            .replaceAll('-', '/'),
                                        label: item.contents_type_nm,
                                        link: item.subject,
                                        icon: '',
                                        id: item.topics_id,
                                        edit: false
                                    });
                                }
                                self.favourite = topics;
                            })
                            .catch(function (error) {
                                self.$store.dispatch('snackbar/setError', error.response.data.errors?.[0].message);
                                self.$store.dispatch('snackbar/snackOn');
                            });
                    }
                })
                .catch(function (error) {
                    self.$store.dispatch('snackbar/setError', error.response.data.errors?.[0].message);
                    self.$store.dispatch('snackbar/snackOn');
                });
        },
        async login() {
            this.loading = true;
            localStorage.setItem('sitekey', this.sitekey); // save sitekey
            if (this.sitekey === 'dev-nuxt-auth') {
                this.$store.$auth.ctx.$axios.defaults.baseURL = 'https://dev-nuxt-auth.a.kuroco.app';
            } else {
                this.$store.$auth.ctx.$axios.defaults.baseURL = 'https://' + this.sitekey + '.g.kuroco.app';
            }
            await this.$auth
                .loginWith('local', { data: this.form })
                .then(() => {
                    this.updateTopics();
                    this.updateDesign();
                    this.$router.push(this.localePath('/'));
                    this.$store.dispatch('snackbar/setMessage', this.$i18n.t('slackbar.logged_in'));
                    this.$store.dispatch('snackbar/snackOn');
                    this.loading = false;
                })
                .catch(() => {
                    this.$store.dispatch('snackbar/setError', this.$i18n.t('slackbar.login_fail'));
                    this.$store.dispatch('snackbar/snackOn');
                    this.loading = false;
                });
        }
    }
};
</script>

