<template>
  <div class="container">
    <button @click.prevent="openLoginWindow">Login</button>
  </div>
</template>

<script>

import crypto from 'crypto-js';

export default {
  data() {
    return {
      email: '',
      password: '',
      state: '',
      challenge: '',
    }
  },

  computed: {
    loginUrl() {
      return 'AUTHORIZE_URL?' +
          'client_id=CLIENT_ID' +
          '&redirect_uri=http://localhost:3000/auth/callback' +
          '&response_type=code' +
          '&scope=*' +
          '&prompt=consent' +
          '&access_type=offline' +
          '&state=' + this.state + '' +
          '&code_challenge=' + this.challenge + '' +
          '&code_challenge_method=S256'
    }
  },

  mounted() {
    window.addEventListener('message', (e) => {
      console.log(e.data.access_token);
    });

    this.state = this.createRandomString(40);
    const verifier = this.createRandomString(128);

    this.challenge = this.base64Url(crypto.SHA256(verifier));
    window.localStorage.setItem('state', this.state);
    window.localStorage.setItem('verifier', verifier);
  },

  methods: {
    openLoginWindow() {
      window.open(this.loginUrl, 'popup', 'width=700,height=700');
    },

    createRandomString(num) {
      return [...Array(num)].map(() => Math.random().toString(36)[2]).join('')
    },

    base64Url(string) {
      return string.toString(crypto.enc.Base64)
        .replace(/\+/g, '-')
        .replace(/\//g, '_')
        .replace(/=/g, '');
    }
  }
}
</script>
