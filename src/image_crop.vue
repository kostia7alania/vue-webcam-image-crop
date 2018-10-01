<template>

  <div id="app">  
    <hr/> 
    <div>
      <vue-cropper
          ref='cropper'
          :guides="true"
          :view-mode="1"
          drag-mode="crop"
          :auto-crop-area="0.5"
          :min-container-width="640"
          :min-container-height="480"
          :background="true"
          :rotatable="true"
          :src="src"
          alt="Исходное фото"
          :img-style="{ 'width': '640px', 'height': '480px' }">
      </vue-cropper>
    </div>
    <img v-if="cropImg" :src="cropImg" style="width: 400px; height: auto; " alt="Обработанное фото" />
    <br/>
    <br/>

    <button @click="$emit('cancel')" v-if="src != '' ">Отменить</button>
    <button @click="cropImage" v-if="src != '' ">Применить</button>
    <button @click="rotate" v-if="src != ''  ">Повернуть</button>
    
  </div>

</template>

<script>
  import VueCropper from 'vue-cropperjs';
  export default {
    name:'image_crop',
    components: {
      VueCropper,
    },
    props:['img','blob','src'], 
    data() {
      return {  
        cropImg: ''
      };
    },
    mounted(){ 
        if (typeof FileReader === 'function') {
          const reader = new FileReader();
          reader.onload = (event) => {  };
            window.cropper = this.$refs.cropper;
            this.$refs.cropper.replace(this.img);// rebuild cropperjs with the updated source 
            //window.blob = this.blob;  reader.readAsDataURL(this.blob);  
        } else { alert('Sorry, FileReader API not supported'); }
      },
    methods: { 
      cropImage() { 
        this.cropImg = this.$refs.cropper.getCroppedCanvas().toDataURL(); // get image data for post processing, e.g. upload or setting image src
        this.$emit('apply', this.cropImg)
      },
      rotate() { 
        this.$refs.cropper.rotate(90);// guess what this does :)
      },
    },
  };
</script>
