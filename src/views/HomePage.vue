<template>
  <div class="mainContainer">
    <video id="video" width="500" height="380" autoplay></video>
    <a id="download-video" download="test.mp4">Download Video</a>
    <div class="buttonContainer">
        <button class="btn" @click="startCamera()">Start Camera</button>
        <button class="btn" @click="startRecording()">Record</button>
        <button class="btn" @click="stopRecording()">Stop</button>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return{
        cameraStream: null,
        mediaRecorder: null,
    }
  },
  methods:{
    async startCamera() {
      let video = document.querySelector("#video");
      this.cameraStream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
      video.srcObject = this.cameraStream
    },
    startRecording() {
        let blobsRecorded = [];
        let download_link = document.querySelector("#download-video");
        this.mediaRecorder = new MediaRecorder(this.cameraStream, { mimeType: 'video/webm' });

        this.mediaRecorder.addEventListener('dataavailable', function(e) {
            blobsRecorded.push(e.data);
        });

        this.mediaRecorder.addEventListener('stop', function() {
            let video_local = URL.createObjectURL(new Blob(blobsRecorded, { type: 'video/webm' }));
            download_link.href = video_local;
            blobsRecorded = []
        });

        this.mediaRecorder.start(1000);
    },
    stopRecording() {
        this.cameraStream.getTracks().forEach(function(track) {
          track.stop();
        });
        this.mediaRecorder.stop(); 
    }
  },
  
  mounted() {

  }
}
</script>

<style scoped>
  .mainContainer{
    width: 500px;
    height: auto;
    margin: 0 auto;   
  }
  .videoContainer{
    height: 380px;
    width: 500px;
    border: 1px solid #ccc;
  }
  .buttonContainer{
    margin-top: 0.5rem;
  }
  .btn {
    /* height: 5rem;
    width: 5rem;
    border-radius: 50%;
    border: none; */
  }
</style>