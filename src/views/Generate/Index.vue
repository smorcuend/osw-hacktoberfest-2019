<template>
  <div class="generate mb-5">
    <header class="text-center mb-5">
      <h2> Generate JSON </h2>
      <p class="lead text-muted">Fill the form in to generate your JSON. Ready to copy & paste!</p>
      <ShareTwitterBtn/>
    </header>

    <form @submit.prevent="generateJson">
      <b-row>
        <b-col sm="6">
          <b-form-group
            id="input-group-1"
            label="GitHub username:"
            label-for="input-1"
            description="Your github username"
          >
            <b-form-input
              id="input-1"
              v-model="form.username"
              type="text"
              required
              placeholder="Username"
            ></b-form-input>
          </b-form-group>
        </b-col>
        <b-col sm="6">
          <b-form-group
            id="input-group-2"
            label="Favourite color:"
            label-for="input-2"
            :description="yourColor"
          >
            <b-form-input
              id="input-2"
              v-model="form.color"
              type="color"
              required
              placeholder="Color"
            ></b-form-input>
          </b-form-group>
        </b-col>
        <b-col sm="12" class="d-flex justify-content-end">
          <b-button class="button-custom-width" type="submit" variant="primary" :disabled="buttonIsLoading">
            <span v-if="buttonIsLoading"><b-spinner label="Loading..."></b-spinner></span>
            <span v-else>Generate JSON!</span>
          </b-button>
        </b-col>
      </b-row>
    </form>

    <hr class="my-5">

    <h5>Result: </h5>
    <div class="result bg-light p-3 mb-2 rounded">
      <pre v-if="showCopyModel" class="mb-0" >{{copyModel}}</pre>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import ShareTwitterBtn from '@/components/ShareTwitterBtn'

export default {
  name: 'Generate',
  components: { ShareTwitterBtn },
  data () {
    return {
      form: {
        color: '#fafafa',
        username: ''
      },
      copyModel: {
        name: '',
        color: '',
        username: '',
        avatarUrl: '',
        location: ''
      },
      buttonIsLoading: false
    }
  },
  mounted () {
    let twitterScript = document.createElement('script')
    twitterScript.setAttribute('async', '')
    twitterScript.setAttribute('src', 'https://platform.twitter.com/widgets.js')
    twitterScript.setAttribute('charset', 'utf-8')
    document.body.appendChild(twitterScript)
  },
  computed: {
    yourColor () {
      return this.form.color ? `Selected color: ${this.form.color}` : ''
    },
    showCopyModel () {
      return this.copyModel.name !== ''
    }
  },
  methods: {
    generateJson (ev) {
      this.buttonIsLoading = true
      this.getGithubUser(this.form.username)
        .then((res) => {
          this.buttonIsLoading = false
          console.log(res)
          this.copyModel.name = res.data.name
          this.copyModel.color = this.form.color
          this.copyModel.username = this.form.username
          this.copyModel.avatarUrl = res.data.avatar_url
          this.copyModel.location = res.data.location
        })
        .catch((err) => {
          this.buttonIsLoading = false
          console.log(JSON.stringify(err.code))
          if (err.response) {
            /*
             * The request was made and the server responded with a
             * status code that falls out of the range of 2xx
             */
            console.log(err.response.data)
            console.log(err.response.status)
            console.log(err.response.headers)
          } else if (err.request) {
            /*
             * The request was made but no response was received, `err.request`
             * is an instance of XMLHttpRequest in the browser and an instance
             * of http.ClientRequest in Node.js
             */
            console.log(err.request)
          } else {
            // Something happened in setting up the request and triggered an err
            console.log('err', err.message)
          }
          this.$bvToast.toast(`${err.message} 😅`, {
            title: 'Wooops!',
            variant: 'danger',
            autoHideDelay: 5000
          })
          this.copyModel = { name: '', color: '', username: '', avatarUrl: '', location: '' }
        })
    },
    getGithubUser (user) {
      const url = `https://api.github.com/users/${user}`
      return axios.get(url)
    }
  }
}
</script>

<style lang="stylus">
  .generate
    .result
      min-height 158px

    .button-custom-width
      width 145px
</style>
