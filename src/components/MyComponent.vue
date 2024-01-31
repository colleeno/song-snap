<template>
  <h1>playlist</h1>
  <div v-if="playlist">
    <h2>{{ playlist.name }}</h2>
    <p>{{ playlist.description }}</p>
    <ul>
      <li v-for="track in playlist.tracks.items" :key="track.track.id">
        {{ track.track.name }}
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
      clientId: import.meta.env.VITE_APP_SPOTIFY_API_ID,
      clientSecret: import.meta.env.VITE_APP_SPOTIFY_CLIENT_SECRET,
      playlist: null,
    }
  },
  mounted () {
    // todo: move this out of mounted
    // add input for id and button to fetch playlist
    // add gamification
    const client_id = this.clientId;
    const client_secret = this.clientSecret;
    const auth_token = btoa(`${client_id}:${client_secret}`);

    const getAuth = async () => {
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
    }

    const getPlaylist = async (accessToken, playlistId) => {
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
    }

    getAuth().then(accessToken => {
      const playlistId = '5FErwRYsAxu9qev9uJZGGB?si=b6460c7ed7e5410b';
      getPlaylist(accessToken, playlistId);
    });

  },
};
</script>
