<template>
  <div class="inner">

    <div class="inputLink">
      <input type="text" @change="setLink($event, link)" placeholder="Video link ...">
    </div>
    
    <div class="getButton">
      <button @click="randomizeComments(apikey, link, results, maxResults, randomComment, sampnicknamesUse, sampComments, videoId)">Get</button>
    </div>

    <div class="error hide">
      <p class="error__text">Write link and range of results!</p>
    </div>

    <div class="sort">
      <p class="sort_title">Sort by Max Result:</p>
      <p class="sort_subtitle">
        <label for="input_maxresults">Your Number (1-500):</label>
      </p>
      <input placeholder="Your value ..." type="text" id="input_maxresults" @change="setResult($event.target.value, maxResults)">
      <button @click="setResult(50, maxResults)">50</button>
      <button @click="setResult(100, maxResults)">100</button>
    </div>

    <div class="settings">
      <div class="otherSettings">
        <label for="checkbox_nicknames">Samp Nicknames</label>
        <input type="checkbox" id="checkbox_nicknames" @change="checkboxHandler(checkboxNicknames, sampnicknamesUse)" v-model="checkboxNicknames">
      </div>
    </div>
    
    <div class="resultsInfo">
      <p class="total_results">Total results: <span>{{ results }}</span></p>
      <p class="random_comment">Random Comment is:  <span>{{ randomComment }}</span></p>
      <iframe class="result_video" 
      width="380" 
      height="220" 
      v-bind:src="videoId" 
      frameborder="0" 
      autoplay="0" 
      controls="0"
      >
      </iframe>
    </div>

  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      apikey: 'AIzaSyBIU6dDfnIRfTqLgW7WuffEtDy4zJIVQWw',
      link: '',
      results: 0,
      maxResults: '',
      randomComment: '',
      checkboxNicknames: false,
      sampnicknamesUse: false,
      sampComments: [],
      videoId: ''
    }
  },

  methods: {
    setLink: function(e, link) {
      this.link = e.target.value
    },
    parseLink: function(link) {
        return link.split('?v=')[1].split('&')[0]
    },
    setResult: function(num, maxResults) {
        this.maxResults = num
    },
    getRandomNumber: function(max) {
      return Math.floor(Math.random() * Math.floor(max));
    },
    checkboxHandler: function(checkboxNicknames, sampnicknamesUse) {
      this.sampnicknamesUse = !this.sampnicknamesUse
    },
    async randomizeComments(apikey, link, results, maxResults, randomComment, sampnicknamesUse, sampComments, videoId) {
      if (link && maxResults != '' && link && maxResults != null) {
        await axios.get('https://www.googleapis.com/youtube/v3/commentThreads?key=' + apikey + '&textFormat=plainText&part=snippet&videoId=' + this.parseLink(link) + '&maxResults=' + maxResults + '&pageToken=').then(response => {
        this.videoId = 'http://www.youtube.com/embed/' + this.parseLink(link)
        if (!sampnicknamesUse) {
          const results = response.data.items.length
          this.results = results
          document.querySelector('.random_comment span').style.color="#93C84F"
          this.randomComment = response.data.items[this.getRandomNumber(results)].snippet.topLevelComment.snippet.textOriginal
        } else {
          this.results = 0
          this.sampComments = []
          response.data.items.forEach(obj => {
            if (obj.snippet.topLevelComment.snippet.textOriginal.match(/^[A-Z][A-Za-z]{1,16}_[A-Z][A-Za-z]{1,16}/)) {
              this.results++
              this.sampComments.push(obj.snippet.topLevelComment.snippet.textOriginal)
            }
          }) //update
          if (this.results <= 0) {
            document.querySelector('.random_comment span').style.color="#fa3443"
            this.randomComment = 'Comments is empty!'
          } else {
            this.randomComment = this.sampComments[this.getRandomNumber(sampComments.length)]
          }
        }
        })
      } else {
        const error = document.querySelector('.error')

        error.classList.add('look')
        error.classList.remove('hide')

        setTimeout(() => {
          error.classList.remove('look')
          error.classList.add('hide')
        }, 60*70);
      }
      
    } // End of method randomizeComments()

  }
}
</script>

<style>
body {
  box-sizing: border-box;
}
.inner {
  position: relative;
}
.inputLink {
  display: flex;
  justify-content: center;
}
.inputLink input {
  width: 500px;
  height: 37px;
  padding: 0 15px;
  border-radius: 20px;
  border: 1px #e1e0e2 solid;
  outline: none;
  color: rgba(0,0,0,.87);
  font-size: 16px;
  transition: box-shadow ease .2s;
}
.inputLink input::-webkit-input-placeholder {
  opacity: .5;
}
.inputLink input:hover, input:focus {
  box-shadow: 0px 0px 9px 0px rgba(50, 50, 50, .1);
}
.getButton {
  display: flex;
  justify-content: center;
  margin-top: 15px;
}
.getButton button {
  width: 100px;
  height: 36px;
  color: #fff;
  font-size: 17px;
  font-weight: 700;
  background: #F8626D;
  border: 0;
  border-radius: 12px;
  text-transform: uppercase;
  transition: all ease .2s;
  cursor: pointer;
}
.getButton button:hover {
  background: #DA5660;
  transform: translateY(-2px);
}
.getButton button:active {
  transform: scale(0.99);
}
.resultsInfo {
  position: relative;
  width: 600px;
  margin: 20px auto;
}
.resultsInfo p {
  text-align: center;
}
.total_results {
  font-size: 18px;
}
.total_results span {
  color: #DA5660;
  font-weight: 700;
}
.random_comment {
  font-size: 19px;
}
.random_comment span {
  font-weight: 700;
}
.settings {
  float: right;
}
.otherSettings {
  max-height: 200px;
  max-width: 200px;
  border: 1px #e1e0e2 solid;
  border-radius: 20px;
  padding: 10px 15px;
}
.otherSettings label {
  margin-right: 7px;
}
.otherSettings input {
  transform: scale(1.5);
}
.sort {
  position: absolute;
  width: 200px;
  border: 1px #e1e0e2 solid;
  border-radius: 20px;
  padding: 10px 15px;
}
.sort_title {
  font-size: 17px;
  font-weight: 700;
}
#input_maxresults {
  outline: none;
  border: 1px #e1e0e2 solid;
  border-radius: 20px;
  padding: 5px 10px;
  margin: 10px 0;
}
#input_maxresults::-webkit-input-placeholder {
  opacity: .5;
}
#input_maxresults:hover, #input_maxresults:focus {
  box-shadow: 0px 0px 9px 0px rgba(50, 50, 50, .1);
}
.sort button {
  padding: 5px 10px;
  background: transparent;
  border: 1px #e1e0e2 solid;
  border-radius: 20px;
  transition: all ease .3s;
}
.sort button:hover, button:focus, button:active {
  border: 1px #EC683B solid;
}
.error {
  position: relative;
  margin: 15px 0;
  z-index: 100;
  left: 50%;
  transform: translateX(-50%);
  width: 500px;
  height: 38px;
  border: 1.8px #FF7270 solid;
  border-radius: 10px;
  background: #FFAEAD;
  opacity: 0;
  transition: all ease .4s;
}
.look {
  opacity: .8;
}
.hide {
  opacity: 0;
}
.error__text {
  position: absolute;
  font-size: 18px;
  font-weight: 400;
  text-align: center;
  top: 50%;
  left: 50%;
  transform: translate3D(-50%, -50%, 0);
}
.result_video {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  border-radius: 20px;
  margin-top: 50px;
}
</style>
