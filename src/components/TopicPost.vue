<template>
  <div class="post create">
    <div class="post-top">
      <div class="dp">
        <img src="../assets/images/girl.jpg" alt="" />
      </div>
      <input
        v-model="topic"
        type="text"
        placeholder="What's on your mind, Aashish ?"
      />
    </div>
    <div class="post-bottom">
      
      <div @click.prevent="postTopic" class="action">
        <i  class="fa fa-edit"></i>
        <span> Post</span>
      </div>
    </div>
    <div class="post-bottom">
      <div @click.prevent="postQuote" class="action">
        <i class="fa fa-quote-left"></i>
        <span> Random Quote</span>
      </div>
      <div class="action">
        <i class="fa fa-image"></i>
        <span> Photo/Video</span>
      </div>
      <div @click="startSpeechToTxt" class="action">
        <i  class="speech-to-txt fa fa-microphone"></i>
        <span> Speech</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "TopicPost",
  data() {
    return {
      topic: "",
      runtimeTranscription_: "",
      transcription_: [],
      lang_: "en-US",
    };
  },
  computed: {
    quotes () {
      return this.$store.state.quotes
    }
  },
  methods: {
    startSpeechToTxt() {
      // initialisation of voicereco

      window.SpeechRecognition =
        window.SpeechRecognition || window.webkitSpeechRecognition;
      const recognition = new window.SpeechRecognition();
      recognition.lang = this.lang_;
      recognition.interimResults = true;

      // event current voice reco word
      recognition.addEventListener("result", (event) => {
        var text = Array.from(event.results)
          .map((result) => result[0])
          .map((result) => result.transcript)
          .join("");
        this.runtimeTranscription_ = text;
        this.topic = text;
      });
      // end of transcription
      recognition.addEventListener("end", () => {
        this.transcription_.push(this.runtimeTranscription_);
        this.runtimeTranscription_ = "";
        recognition.stop();
      });
      recognition.start();
    },
    postQuote () {
      console.log(`hitten`)
      this.topic = this.quotes
      this.$store.dispatch("getQuotes")
    },
    postTopic () {
      const payload = {
        post: this.topic,
        like: 0,
        imageUrl: ""
      }
      console.log(`payload`, payload)
      this.$store.dispatch("postTopic", payload)
      .then(({data}) => {
        console.log(data, `response server onTopicPost`)
        this.$store.dispatch("fetchTopics")
      }).catch((err) => {
        console.log(err.response.data);
      });
    }
  },
  created () {
    this.$store.dispatch("getQuotes")
  }
};
</script>

<style>
</style>