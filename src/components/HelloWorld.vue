<template>
  <div>
    <button @click="start_reco">开始录音</button>
    <button @click="stop_reco">结束录音</button>
    <button @click="up_audio">上传录音</button>
    {{message}}
    <audio :src="audioSrc" autoplay controls></audio>
    <input type="file" ref="myfile">

    <button @click="uploader">上传图片</button>
    <p v-if="img_info.age">这是一个{{img_info.age}}岁{{img_info.expression}} {{img_info.emotion}}的小{{img_info.gender}}颜值{{img_info.beauty}}</p>
  </div>
</template>
<script>
var _self,rc;
export default {
  name:"HelloWorld",
  data(){
    return{
      img_info:{},
      message:"", 
      audioSrc:""
    }
  },
  created(){
    _self = this
  },
  methods:{
    uploader:function(){
      var b = this.$refs.myfile.files[0];
      var fb = new FormData();
      fb.append("img",b)
      console.log(b)
      this.$axios.post("http://127.0.0.1:8000/upimage/",fb).then(
        function(res){
          console.log(res);
          _self.img_info = res.data
          console.log(_self.img_info.gender);
          if (_self.img_info.gender == "female"){
            _self.img_info.gender = "姐姐";
          }else{
            _self.img_info.gender = "哥哥";
          }
      })
    },
    start_reco:function(){
      rc = new this.$recorder({
        sampleRate :16000,
      })
      rc.start().then(function(){
        console.log("开始录音")
     })
    },
    stop_reco:function(){
      rc.stop()
      console.log("stop")
    },
    up_audio:function(){
      var pcmblob = rc.getPCMBlob();
      console.log(pcmblob);
      var fd = new FormData();
      fd.append('recofile',pcmblob);
      this.$axios.post("http://127.0.0.1:8000/upaudio/",fd).then(function(res){
        console.log(res)
        _self.message = res.data.msg;
        _self.audioSrc = "http://127.0.0.1:8000/static/"+res.data.filename
      })
      // rc.play();
      // console.log('play')
    }
  }
}
</script>  