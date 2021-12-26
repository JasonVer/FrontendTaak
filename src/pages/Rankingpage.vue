<!--<template>
  <div>
    <button @click="goToPage('home')">Go to home</button>
    <h1>Ranking</h1>
    <div v-for="song in songs" :key="song.id">
      {{ song.title }} - {{ song.artist.name }} - {{ count }}
      <div v-for="vote in votes" :key="vote.id">
        <div v-if="vote.songID == song.id">
        </div>
      </div>
    </div>
  </div>
</template>-->
<template>
  <div>
    <button @click="goToPage('home')">Go to home</button>
    <h1>Ranking</h1>

    <table>
    <tr>
    <td>Ranking</td>
    <td>Artist</td>
    <td>Title</td>   
    <td>Points</td>
    </tr>
    <tr v-for="(song,i) in songs" :key="i">
    <td>{{i+1}}</td>
    <td>{{song.artist}}</td>
    <td>{{song.title}}</td>  
    <td>{{song.allPoints}}</td>
    </tr>
    </table>

  </div>
  
</template>
<script>
    export default {
        name: "Rankingpage",
        methods: {
            goToPage(page) {
                this.$emit("change-page", page);
            }
        }
    }
</script>
<script>
export default {
  name: "Rankingpage",
  data() {
    return {
      songs: [],
    };
  },
  
  computed: {
    total: function(){
      return this.items.reduce(function(prev, item){
   return sum + item.price; 
     },);
    },
  },

  mounted() {
    this.fetchSongs();
  },
  methods: {
    goToPage(page) {
      this.$emit("change-page", page);
    },
    //data methods
    fetchSongs() {
      //get all songs
      const url = "http://webservies.be/eurosong/Songs";
      fetch(url)
        .then((response) => {
          return response.json();
        })
        .then((songs) => {
          this.fetchVotes(songs);
          this.fetchArtists(songs);
        });
    },
    fetchArtists(songs) {
      //get all artist
      const url = "http://webservies.be/eurosong/Artists";
      fetch(url)
        .then((response) => {
          // response is text, so convert to json
          return response.json();
        })
        .then((artists) => {
          // loop over array songs with forEach method
          songs.map((song) => {
             // find the artist in an array
            const artist = artists.find((artist) => artist.id == song.artist);
            song.artist = artist.name;
             // return the new object
            return song;
          });
          // change data of songs, so everything will get rerenderd;
          this.songs = songs;
        });
    },
    /*fetchVO() {
      // Get all songs
      const url = "http://webservies.be/eurosong/Votes";
      fetch(url)
        .then((response) => {
          return response.json();
        })
        .then((response) => (this.votes = response));
    },*/

    fetchVotes(songs) {
      // Get all artist
      const url = "http://webservies.be/eurosong/Votes";
      
      fetch(url)
        .then((response) => {
           // response is text, so convert to json
          return response.json();
        })
        .then((votes) => {
          // loop over array songs with forEach method
          let allvotes = 0;
          songs.map((song) => {
            song.allPoints = 0;
            votes
              .filter((vote) => vote.songID == song.id)
              .forEach((element) => {
                if (element.songID == song.id) {
                  song.allPoints += element.points;
                  allvotes += element.points;
                }
              });
            // return the new object
            return song;
          });
          // change data of songs, so everything will get rerenderd;
          this.songs = songs.sort((a, b) => b.allPoints - a.allPoints);
        });
    },
  },
};
</script>