<template>
  <div class="container">
    <input type="file"  @change="handleSelectImage" name="Select image"/>
    <div id="image-container" class="pinturaContainer" ref="canvas" style="height: 100%">
<!--      <button type="button" v-on:click="handleButtonClick($event)">-->
<!--        Load new image-->
<!--      </button>-->
      <PinturaEditor
          ref="editor"
          v-bind="editorDefaults"
          :src="editorSrc"
          :stickers="stickers"
          v-on:pintura:load="handleEditorLoad($event)"
          v-on:pintura:addshape="handleEditorAddshape($event)"
          v-on:pintura:updateshape="handleEditorUpdateshape($event)"
          v-on:pintura:process="handleEditorProcess($event)"
          :imageAnnotation="imageAnnotation"

      ></PinturaEditor>

    </div>
  </div>
</template>
<script>
// Import the editor default configuration
import { PinturaEditor } from '@pqina/vue-pintura';
import { getEditorDefaults,  plugin_frame_defaults, overlayEditor} from '@pqina/pintura';
import * as faceapi from 'face-api.js'


// get defaults
const { frameOptions, frameStyles } = plugin_frame_defaults;

overlayEditor('.my-editor', {
  src: 'image.jpeg',
  /* options here */
});


export default {
  name: 'App',
  components: {
    PinturaEditor,
  },
  data() {
    return {
      // Show frame util
      util: 'frame',
      editorResult: undefined,

      // These are the options in the frame list
      frameOptions: [
        // Default frame options
        ...frameOptions,


        // Custom 'myFrame'
        ['myFrame', 'My Frame'],
      ],

      // These are the styles available
      frameStyles: {
        // Default styles
        ...frameStyles,

        // Our custom frame style
        myFrame: {
          // The default shape styles for our frame
          imageFrame: {
            frameStyle: 'nine',
            frameImage: 'src/assets/frame.png',
            frameSlices: {
              x1: 0.2, // red
              y1: 0.2, // green
              x2: 0.8, // blue
              y2: 0.8, // yellow
            },

          },

          // The thumbnail to show in the toolbar
        },
      },


      // The shape preprocessor expands shapes to more shapes, we can only use pixel values in the shape preprocessor
      // shapePreprocessor: [
      //   // Our custom shape preprocessor
      //   (shape, options) => {
      //     // Should return undefined if shape is not a match
      //     if (shape.frameStyle !== 'my-custom-frame') return;
      //
      //     const f = Math.max(shape.width, shape.height) * 0.05;
      //
      //     return [
      //       // Overlay a line on the inside of the image
      //       {
      //         x: f,
      //         y: f,
      //         width: shape.width - f * 2,
      //         height: shape.height - f * 2,
      //         strokeWidth: 2,
      //
      //         // Use shape property as style
      //         strokeColor: shape.frameColor,
      //       },
      //       // Add a text shape
      //       {
      //         x: f,
      //         y: f,
      //         fontSize: f,
      //         text: 'Hello World',
      //
      //         // Wse shape property as style
      //         color: shape.frameColor,
      //       },
      //       // Add other shapes if needed
      //       // ...
      //     ];
      //   },
      // ],

        image: null,
        imageHeight: 100, // Set the initial sticker width
        imageWidth: 100, // Set the initial sticker height
      editorSrc: undefined,
      // Pass the editor default configuration options
      editorDefaults: getEditorDefaults({
        imageWriter: {
          targetSize: {
            width: 1024,
            height: 768,
            fit: 'contain'
          },
          postprocessImageData: (imageData) =>
              new Promise((resolve, reject) => {
                // Create a canvas element to handle the imageData

                const canvas = document.createElement('canvas');
                canvas.width = imageData.width;
                canvas.height = imageData.height;
                const ctx = canvas.getContext('2d');
                ctx.putImageData(imageData, 0, 0);

                // Draw our watermark on top
                const watermark = new Image();
                watermark.onload = () => {
                  // how to draw the image to the canvas
                  // ctx.globalCompositeOperation = 'screen';

                  // draw the watermark in the top right corner
                  ctx.drawImage(
                      watermark,

                      // the watermark x and y position
                      350,
                      140,

                      // the watermark width and height
                      400,
                      400
                  );

                  // Get and return the modified imageData
                  resolve(
                      ctx.getImageData(
                          0,
                          0,
                          imageData.width,
                          imageData.height
                      )
                  );
                };
                watermark.onerror = reject;
                watermark.crossOrigin = 'Anonymous';
                watermark.src = '/img/watermark.svg';
              }),
        },
      }),
      imageAnnotation: [
        // a red square
        {
          x: 0,
          y: 0,
          width: '100%',
          height: '100%',
          backgroundImage: 'src/assets/frame-final.png',
          alwaysOnTop:true,
          backgroundSize:'contain'
        },
        
      ],

      imageFrame: {
        frameStyle: 'nine',
        frameImage: 'src/assets/frame.png',

      },

      // The source image to load
      stickers: [
        {
          src: 'src/assets/stickers/sticker1.png',
          width: 400,
          height: 400,
          alt: 'sticker-one',
        },
        {
          src: 'src/assets/stickers/sticker2.png',
          width: 400,
          height: 400,
          alt: 'sticker-two',
        },
        {
          src: 'src/assets/stickers/sticker3.png',
          width: 400,
          height: 400,
          alt: 'sticker-three',
        },
        {
          src: 'src/assets/stickers/sticker4.png',
          width: 400,
          height: 400,
          alt: 'sticker-four',
        },
        {
          src: 'src/assets/stickers/sticker5.png',
          width: 400,
          height: 400,
          alt: 'sticker-five',
        },
        {
          src: 'src/assets/stickers/sticker6.png',
          width: 400,
          height: 400,
          alt: 'sticker-six',
        },
        {
          src: 'src/assets/stickers/sticker7.png',
          width: 400,
          height: 400,
          alt: 'sticker-seven',
        },
        {
          src: 'src/assets/stickers/sticker8.png',
          width: 400,
          height: 400,
          alt: 'sticker-eight',
        },
        {
          src: 'src/assets/stickers/sticker9.png',
          width: 400,
          height: 400,
          alt: 'sticker-nine',
        },
        {
          src: 'src/assets/stickers/sticker10.png',
          width: 400,
          height: 400,
          alt: 'sticker-ten',
        },
        {
          src: 'src/assets/frame.png',
          width: 400,
          height: 400,
          alt: 'sticker-11',
        },
      ],
      stickerImage: 'src/assets/rug.png',
      imageCropAspectRatio: 1,
      faceDetections:[],


    };

  },
  methods: {

    handleEditorLoad: async function (imageReaderResult) {
      const imageFile = await imageReaderResult;
      console.log(imageFile,'291');
      this.image = URL.createObjectURL(imageFile.detail.dest);
      // const canvas = this.$refs.canvas;
      // const ctx = canvas.getContext('2d');
      // Load the image
      const img = await faceapi.fetchImage(this.image);
      const displaySize = { width: img.width, height: img.height }



      // faceapi.matchDimensions(finalCanvas, displaySize)

      // Load the face-api.js models (you may need to specify the path to the models)
      await faceapi.nets.faceLandmark68Net.loadFromUri('/data/weights/');
      await faceapi.nets.ssdMobilenetv1.loadFromUri('/data/weights/');
      await faceapi.nets.tinyFaceDetector.loadFromUri('/data/weights/');
      await faceapi.nets.ageGenderNet.loadFromUri('/data/weights/');
      const detections = await faceapi.detectSingleFace(img, new faceapi.SsdMobilenetv1Options).withAgeAndGender();
      const resizedDetections = faceapi.resizeResults(detections, displaySize)
      console.log(resizedDetections,'resized')
      // faceapi.draw.drawDetections(finalCanvas, resizedDetections);

      this.faceDetections = resizedDetections;

    },
    handleEditorAddshape: function (shape) {
      console.log('addshape', shape);
      shape.detail.x = this.faceDetections.detection.box.x - 45;
      shape.detail.y = this.faceDetections.detection.box.y - 100;
      shape.detail.height =  this.faceDetections.detection.box.height *2;
      shape.detail.width =  this.faceDetections.detection.box.width *2;
      return shape;
    },
    handleEditorUpdateshape: function (shape) {
      console.log('updateshape', shape);
    },
    handleEditorProcess: function (imageState) {
      console.log(imageState, '313');
      this.editorResult = URL.createObjectURL(imageState.detail.dest);
      this.downloadFile(imageState.detail.dest);

    },
    handleSelectImage: function (event) {
      const file = event.target.files[0];
      console.log(file);
      this.editorSrc = file;
    },
    downloadFile: function (file) {
      // Create a hidden link and set the URL using createObjectURL
      const link = document.createElement('a');
      link.style.display = 'none';
      link.href = URL.createObjectURL(file);
      link.download = `watermark_${file.name}`;

      // We need to add the link to the DOM for "click()" to work
      document.body.appendChild(link);
      link.click();

      // To make this work on Firefox we need to wait a short moment before clean up
      setTimeout(() => {
        URL.revokeObjectURL(link.href);
        link.parentNode.removeChild(link);
      }, 0);
    }


  },
  mounted() {
    const blankImage = document.createElement('canvas');
    blankImage.width = 1024;
    blankImage.height = 768;

    // The default <canvas> is transparent, let's make it white
    const imageContext = blankImage.getContext('2d');
    imageContext.fillStyle = 'white';
    imageContext.fillRect(0, 0, blankImage.width, blankImage.height);


    // Set editor source to the canvas
    this.editorSrc = blankImage;
  }

};


// create editor
</script>


<style>
@import "../node_modules/@pqina/pintura/pintura.css";

.container {
  width: 100vh;
  height: 100vh;
}
.hidden{
  display: none;
}

.canvasOverlay {
  height: 85%;
  width: 100%;
  position: absolute;
  z-index: 99;
  border: 1px solid red;

}
.pinturaContainer {

  height: 100vh;
  width: 100vh;
}

.pintura-editor {
  height: 100vh;
  width: 100%;
}
.my-editor{
  width: 1024px;
  height: 768px;
}
</style>

