<template>
  <h1>playlist</h1>
  <div v-if="playlist">
    <h2>{{ playlist.name }}</h2>
    <p>{{ playlist.description }}</p>
    <ul>
      <li v-for="track in playlist.tracks.items" :key="track.track.id">
         {{ jumbleWords(track.track.name) }} | {{ track.track.name }}
      </li>
    </ul>
  </div>  
</template>

<script>
import axios from 'axios';
import qs from 'qs';

export default {
  data () {
    return {
      playlist: null,
    }
  },
  created () {
    this.fetchPlaylist();
  },
  methods: {
    async getAuth() {
      const auth_token = btoa(`${import.meta.env.VITE_APP_SPOTIFY_API_ID}:${import.meta.env.VITE_APP_SPOTIFY_CLIENT_SECRET}`);
      try {
        // request access token from spotify api
        const token_url = 'https://accounts.spotify.com/api/token';
        const data = qs.stringify({'grant_type':'client_credentials'});

        const response = await axios.post(token_url, data, {
          headers: { 
            'Authorization': `Basic ${auth_token}`,
            'Content-Type': 'application/x-www-form-urlencoded' 
          }
        })
        return response.data.access_token; 
      } catch(error){
        console.log(error);
      }
    },
    async getPlaylist(accessToken, playlistId) {
      // fetch playlist, using access token
      try {
        const response = await axios.get(`https://api.spotify.com/v1/playlists/${playlistId}`, {
          headers: { 
            'Authorization': `Bearer ${accessToken}`,
          }
        })
        this.playlist = response.data;
        console.log("playlist", response.data);
      } catch(error){
        console.log(error);
      }
    },
    fetchPlaylist () {
      this.getAuth().then(accessToken => {
        const playlistId = '5FErwRYsAxu9qev9uJZGGB?si=b6460c7ed7e5410b';
        this.getPlaylist(accessToken, playlistId);
      });
    },
    jumbleWords(str) {
      const parts = str.split(/(\(.*?\))/);
      return parts.map(part => {
        if (part.startsWith('(')) {
          // if part is in parens, remove the parens, jumble the words inside the parens, then add back
          const withoutParens = part.slice(1, -1);
          const withJumbledWords = withoutParens.split(' ').map(word => this.jumble(word)).join(' ');
          return '(' + withJumbledWords + ')';
        } else {
          return part.split(' ').map(word => this.jumble(word)).join(' ');
        }
      }).join('');
    },
    jumble(word) {
      return word.split('').sort(() => Math.random() - 0.5).join('');
    },
  }
};
</script>
