<template>
    <div id="app">
        <div v-show="!img"><video ref="video" id="video" width="640" height="480" autoplay></video></div>
        <div v-show="!img"><button id="snap" v-on:click="capture()">Сфотографировать</button></div>
        <canvas ref="canvas" id="canvas" width="640"></canvas>
        <div v-show="img && editMode==0"><img :src="img" height="auto" /></div>

        <div v-if="img && editMode==1" id="tui-image-editor">
          <app-img-crop :blob="blob" :img="img"></app-img-crop>
        </div>

        <div v-show="img">
          <button id="snap" v-on:click="del">Переснять</button>
          <button id="snap" v-if="editMode==0" v-on:click="edit">Редактировать</button>
          <button id="snap" v-on:click="save">Сохранить</button>
        </div>
           
    </div>
</template>

<script> 
import image_crop from './image_crop'; 

    export default {    
        components: {"app-img-crop":image_crop,
          },
        name: 'app',
        data() {
            return {
                video: {},
                canvas: {},
                img: null,
                blob: '',
                editMode: 0
            }
        },
        mounted() {
        this.loadCam();
        },
    methods: {    
      cropImage(e){
        console.log('*cropImage=>',arguments)
      }, 
      imageuploaded(res) {
        if (res.errcode == 0) {
          console.log('** RES=>>',res)
        }
      },
      edit(){
                
        const b64toBlob = (b64Data, contentType='', sliceSize=512) => {
          const byteCharacters = atob(b64Data);
          const byteArrays = [];
          
          for (let offset = 0; offset < byteCharacters.length; offset += sliceSize) {
            const slice = byteCharacters.slice(offset, offset + sliceSize);
            
            const byteNumbers = new Array(slice.length);
            for (let i = 0; i < slice.length; i++) {
              byteNumbers[i] = slice.charCodeAt(i);
            }
            
            const byteArray = new Uint8Array(byteNumbers);
            
            byteArrays.push(byteArray);
          }
          
          const blob = new Blob(byteArrays, {type: contentType});
          return blob;
        }
        
        const contentType = 'image/png';

        const b64Data = this.img.replace('data:image/png;base64,','');

        const blob = b64toBlob(b64Data, contentType);
        const blobUrl = URL.createObjectURL(blob);

        const img = document.createElement('img');
        img.src = blobUrl;
        this.blob = img;  
        this.editMode=1; 

      },
    del(){
      this.editMode=0;
      this.img=''; 
      this.loadCam();
    },
    save(){
      alert(JSON.stringify(this._data))
    },
    capture() {
        this.canvas = this.$refs.canvas;
        var context = this.canvas.getContext("2d").drawImage(this.video, 0, 0, 640, 480);
        this.img=canvas.toDataURL("image/png");//push -еси сразу нескок;
    },
    loadCam(){ 
        this.video = this.$refs.video;
        if(navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
          navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
              this.video.src = window.URL.createObjectURL(stream);
              this.video.play();
          });
        }
    }
  }
}
</script>

<style>
    body {
        background-color: #F0F0F0;
    }
    #app {
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
    #video {
        background-color: #000000;
    }
    #canvas {
        display: none;
    }
    li {
        display: inline;
        padding: 5px;
    }



button {
  position: relative;
  display: inline-block;
  font-family: Arial,Helvetica,FreeSans,"Liberation Sans","Nimbus Sans L",sans-serif;
  font-size: 1.5em;
  font-weight: 700;
  color: rgb(245,245,245);
  text-shadow: 0 -1px rgba(0,0,0,.1);
  text-decoration: none;
  user-select: none;
  padding: .3em 1em;
  outline: none;
  border: none;
  border-radius: 3px;
  background: #0c9c0d linear-gradient(#82d18d, #0c9c0d);
  box-shadow: inset #72de26 0 -1px 1px, inset 0 1px 1px #98ff98, #3caa3c 0 0 0 1px, rgba(0,0,0,.3) 0 2px 5px;
  -webkit-animation: pulsate 1.2s linear infinite;
  animation: pulsate 1.2s linear infinite;
}
button:hover {
  -webkit-animation-play-state: paused;
  animation-play-state: paused;
  cursor: pointer;
}
button:active {
  top: 1px;
  color: #fff;
  text-shadow: 0 -1px rgba(0,0,0,.3), 0 0 5px #ffd, 0 0 8px #fff;
  box-shadow: 0 -1px 3px rgba(0,0,0,.3), 0 1px 1px #fff, inset 0 1px 2px rgba(0,0,0,.8), inset 0 -1px 0 rgba(0,0,0,.05);
}
@-webkit-keyframes pulsate {
  50% {color: #fff; text-shadow: 0 -1px rgba(0,0,0,.3), 0 0 5px #ffd, 0 0 8px #fff;}
}
@keyframes pulsate {
  50% {color: #fff; text-shadow: 0 -1px rgba(0,0,0,.3), 0 0 5px #ffd, 0 0 8px #fff;}
}
</style>