<template>
  <div id="app">
    <header>
      <h1>Moderato</h1>
    </header>
    <main>
      <section class="player">
        <h2 class="song-title">{{ current.title }} - <span>{{ current.artist }}</span></h2>
        <div class="controls">
          <button class="prev" @click="prev">Previous</button>
          <button class="play" v-if="!isPlaying" @click="play">Play</button>
          <button class="pause" v-else @click="pause">Pause</button>
          <button class="next" @click="next">Next</button>
          <button class="mute" @click="mute">Mute</button><h3>Volume</h3> <input type="range" id="volume-slider" min="0" max="100" v-model="vSlider" v-on:change="setVolumeValue"><h1>{{ vSlider }}</h1>
        </div>
      </section>
      <section class="playlist">
        <h3>My Playlist</h3>
        <button v-for="song in songs" :key="song.src" @click="play(song)" :class="(song.src == current.src) ? 'song playing' : 'song'">
          {{ song.title }} - {{ song.artist }}
        </button>
      </section>
    </main>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return{
      current:{},
      index: 0,
      vSlider: "50",
      previousVolume: 0,
      isPlaying: false,
      songs: [
        {
          title: 'Castille Soap',
          artist: 'Slyrax',
          src: require('./assets/music/castille-soap.mp3')
        },
        {
          title: '19',
          artist: 'Oddwin',
          src: require('./assets/music/oddwin-19.mp3')
        }
      ],
      player: new Audio()
    }
  },
  methods:{
    play(song){
      if (typeof song.src != "undefined"){
        this.current = song;
        this.player.src = this.current.src;
      }
      this.player.play();

      this.player.addEventListener('ended', function(){
        this.index++;
        if(this.index > this.songs.length - 1){
          this.index = 0;
        }

        this.current = this.songs[this.index];
        this.play(this.current);
      }.bind(this));

      this.isPlaying = true;
    },
    pause(){
      this.player.pause();
      this.isPlaying = false;
    },
    prev(){
      this.index--;
      if(this.index < 0){
        this.index = this.songs.length - 1;
      }

      this.current = this.songs[this.index];
      this.play(this.current);
    },
    next(){
      this.index++;
      if(this.index > this.songs.length - 1){
        this.index = 0;
      }

      this.current = this.songs[this.index];
      this.play(this.current);
    },
    setPreviousVolume(prevVolume){
      this.previousVolume = prevVolume;
    },
    getCurrentVolume(){
      return this.vSlider;
    },
    setToVolume(newVolume){
      this.vSlider = newVolume;
    },
    muteVolume(){
      this.player.muted = true;
    },
    unmuteVolume(){
      this.player.muted = false;
    },
    mute(){
      if(this.getCurrentVolume() === 0){
        this.setToVolume(this.previousVolume);
        this.unmuteVolume();
      }
      else{
        this.setPreviousVolume(this.getCurrentVolume());
        this.setToVolume(0);
        this.muteVolume();
      }
    },
    setVolumeValue(){
      var volValue = this.vSlider / 100;
      this.player.volume = volValue;
    }
  },
  created () {
    this.current = this.songs[this.index];
    this.player.src = this.current.src;
  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body{
  font-family: sans-serif;
}
header{
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px;
  background-color: #212121;
  color: #ffffff;
}
main{
  width: 100%;
  max-width: 768px;
  margin: 0 auto;
  padding: 25px;
}
.song-title{
  color: #212121;
  font-size: 32px;
  font-weight: 700;
  text-transform: uppercase;
  text-align: center;
}
.song-title span{
  font-weight: 400;
  font-style: italic;
}

.controls{
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 30px 50px;
}
button{
  appearance: none;
  background: none;
  border: none;
  outline: none;
  cursor: pointer;
  transition: 300ms;
}
button:hover{
  opacity: .7;
}
.play, .pause{
  font-size: 20px; 
  font-weight: 700;
  padding: 15px 25px;
  margin: 0 15px;
  border-radius: 8px;
  color: #ffffff;
  background-color: #9F38FF;
}
.next, .prev{
  font-size: 16px; 
  font-weight: 700;
  padding: 10px 20px;
  margin: 0 15px;
  border-radius: 6px;
  color: #ffffff;
  background-color: #56099E;
}
.mute{
  font-size: 13px;
  font-weight: 700;
  padding: 5px 10px;
  margin: 0 6px;
  border-radius: 6px;
  color: #ffffff;
  background-color: #000000;
}
#volume-slider{
}

.playlist{
  padding: 0 30px;
}
.playlist h3{
  color: #212121;
  font-size: 28px;
  font-weight: 400;
  margin-bottom: 30px;
  text-align: center;
}
.playlist .song{
  display: block;
  width: 100%;
  padding: 15px;
  font-size: 20px;
  font-weight: 700;
  cursor: pointer;
}
.playlist .song:hover{
  color: #ff5858;
}
.playlist .song.playing{
  color: #ffffff;
  background-image: linear-gradient(to right, #56099E, #9F38FF);
  border-radius: 8px;
}
</style>