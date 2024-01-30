<template>
  <h1>hello</h1>
</template>

<script>
import axios from 'axios';

export default {
  data () {
    return {
      clientId: import.meta.env.VITE_APP_SPOTIFY_API_ID,
      clientSecret: import.meta.env.VITE_APP_SPOTIFY_CLIENT_SECRET
    }
  },
  mounted () {
    axios.get('https://jsonplaceholder.typicode.com/posts/1')
      .then(response => {
        console.log(response.data);
      })
      .catch(error => {
        console.error('Error:', error);
    });
  },
};
</script>
