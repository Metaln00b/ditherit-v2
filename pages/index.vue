<template>
  <div class="flex flex-col items-center max-w-full">
    <div :class="{ 'w-full flex justify-center': imageUploaded}">
      <Logo />
    </div>
    <div v-show="!imageUploaded" class="flex flex-col items-center mt-4">
      <p class="mt-8 text-2xl">An image dithering tool 🏁</p>
      <img class="mt-8" src="~/assets/earth-dither.gif" />
    </div>
    <div
      class="py-12 px-2 md:px-4 flex flex-col md:flex-row pt-8 w-full justify-center"
      :class="{ 'checkers shadow-inner': imageUploaded}"
    >
      <!-- Begin Main Toolbar -->
      <div
        v-show="imageUploaded"
        class="w-full md:w-1/3 lg:w-1/5 order-last md:order-first p-4 rounded"
      >
        <div class="flex flex-col items-center w-full">
          <div class="shadow rounded py-2 px-4 mt-0 mb-2 bg-white w-full">
            
            <!-- Dither mode Selector -->
            <div class="flex flex-row items-center justify-between pb-2">
              <label for="ditherMode" class="font-bold text-sm"><h4 class="text-sm font-bold mt-2 mb-2 uppercase">Dither Mode</h4></label>
              <span
                class="rounded-full h-4 w-4 bg-red-700 text-white flex items-center justify-center float-right text-sm cursor-pointer"
                @click="showDitherModeModal = !showDitherModeModal"
              >
                <span v-if="!showDitherModeModal">
                  ?
                </span>
                <span v-else>
                  X
                </span>
              </span>
            </div>
            <div
              v-if="!showDitherModeModal"
              class="inline-block relative w-full"
            >
              <select
                id="ditherMode"
                v-model="ditherMode"
                class="block appearance-none w-full bg-white border border-gray-300 hover:border-gray-500 px-4 py-2 pr-8 rounded leading-tight focus:outline-none focus:shadow-outline"
              >
                <template v-for="(v, i) in ditherModeOptions">
                  <option :id="v" :name="v" :value="v">{{ v }}</option>
                </template>
              </select>
              <div
                class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700"
              >
                <svg
                  class="fill-current h-4 w-4"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                >
                  <path
                    d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                  />
                </svg>
              </div>
            </div>
            <div v-else>
              <div class="mt-2 bg-red-100 p-2 rounded">
                These methods are different ways to spread around the quantization error introduced by reducing an images color palette. They look quite different, try them out!
              </div>
            </div>
            <!-- End Dither mode Selector -->            
          </div>          
          <ColorPicker
    
            :initial-palette="rgbQuantOptions.palette"
            @update-palette="onUpdatePalette"
          />
          <div class="shadow rounded p-3 bg-white w-full mt-2" v-if="selectedImage">
            <div class="flex flex-row items-center justify-between">
              
              <label for="imageSize" h4 class="text-sm  uppercase font-bold mt-2 mb-2">Image Size</label>
              <span
                class="rounded-full h-4 w-4 bg-red-700 text-white flex items-center justify-center float-right text-sm cursor-pointer"
                @click="showOptionsModalSize = !showOptionsModalSize"
              >
                <span v-if="!showOptionsModalSize">
                  ?
                </span>
                <span v-else>
                  X
                </span>
              </span>
            </div>
            <div v-if="canvasWidth > 5000" class="bg-red-100 p-2 mt-2 mb-4 rounded">
              Width must be less than 5000px
            </div>
            <div v-if="!showOptionsModalSize" class="w-full relative">
                <div class="flex flex-row space-x-4 w-full items-center">
                  <div class="w-1/2">
                    Width (px)
                    <span v-if="customWidth == false" @click="customWidth = !customWidth; focus();" class="block  bg-white border border-gray-300 hover:border-gray-500 px-4 py-2 mt-1 rounded leading-tight focus:outline-none focus:shadow-outline">{{selectedImage.naturalWidth}}</span> 
                    <input 
                      v-else class="block w-full bg-white border border-gray-300 hover:border-gray-500 px-4 py-2 mt-1 rounded leading-tight focus:outline-none focus:shadow-outline"
                      @change="validateWidth()"
                      type="number"  
                      ref="customWidthField"
                      id="customWidthField" 
                      name="customWidthField" 
                      maxlength="4" 
                      max="5000"
                      v-model="canvasWidth" 
                    />                  
                  </div>
                  <div class="w-1/2">
                  Height (px)
                    <span class="block appearance-none bg-gray-200 border border-gray-300 text-gray-500 px-4 py-2 mt-1 rounded leading-tight focus:outline-none focus:shadow-outline">
                      <template v-if="canvasWidth == 'original'">
                        {{selectedImage.height}}
                      </template>
                      <template v-else>   
                        {{(selectedImage.naturalHeight / selectedImage.naturalWidth) * canvasWidth}}
                      </template>               
                    </span>
                  </div>
                </div>           
              <!-- <select
                id="imageSize"
                v-model="canvasWidth"
                class="block appearance-none w-full bg-white border border-gray-300 hover:border-gray-500 px-4 py-2 pr-8 rounded leading-tight focus:outline-none focus:shadow-outline"
              >
                <option
                  id="originalSize"
                  name="originalSize"
                  :value="'original'"
                  >Original ({{selectedImage.naturalWidth}}px)</option
                >
                <template v-for="(v, i) in imageWidths">
                  <option :id="v" name="imageWidth" :value="v" @click="fathom('JN4RHD7N')">{{ v }}</option>
                </template>
                <option
                  @click="fathom('trackGoal', 'MHEE0ZOY', 0)"
                  id="customWidth"
                  name="customWidth"
                  :value="'custom'"
                  >Custom</option
                >                
              </select> -->
              <!-- <div 
                class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700"
              >
                <svg
                  class="fill-current h-4 w-4"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                >
                  <path
                    d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                  />
                </svg>
              </div> -->
            </div>
            <div v-else>
              <div class="mt-2 bg-red-100 p-2 rounded">
                This determines the size of the final file, and can also affect how the dither looks.
              </div>
            </div>  
            <!-- Custom Width Form -->


                
          </div>
          <div class="mt-4 text-center xl:w-64 shadow max-w-full">
            <button class="btn-red text-lg w-full " @click="ditherImage()" :disabled="isError">
              🏁 Dither
            </button>
          </div>

          <div class="shadow rounded py-2 px-4 my-4 bg-white w-full" v-show="ditherMode == 'Error Diffusion'">
            <h4 class="text-sm font-bold mt-2 mb-2 uppercase">Advanced Options</h4>
            <!-- Algorithm Selector -->
            <div class="flex flex-row items-center justify-between mt-4 pb-2">
              <label for="ditherAlgo" class="font-bold text-sm">Algorithm</label>
              <span
                class="rounded-full h-4 w-4 bg-red-700 text-white flex items-center justify-center float-right text-sm cursor-pointer"
                @click="showOptionsModalAlgo = !showOptionsModalAlgo"
              >
                <span v-if="!showOptionsModalAlgo">
                  ?
                </span>
                <span v-else>
                  X
                </span>
              </span>
            </div>
            <div
              v-if="!showOptionsModalAlgo"
              class="inline-block relative w-full"
            >
              <select
                id="ditherAlgo"
                v-model="rgbQuantOptions.dithKern"
                class="block appearance-none w-full bg-white border border-gray-300 hover:border-gray-500 px-4 py-2 pr-8 rounded leading-tight focus:outline-none focus:shadow-outline"
              >
                <template v-for="(v, i) in algorithmOptions">
                  <option :id="v" :name="v" :value="v">{{ v }}</option>
                </template>
              </select>
              <div
                class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700"
              >
                <svg
                  class="fill-current h-4 w-4"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                >
                  <path
                    d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                  />
                </svg>
              </div>
            </div>
            <div v-else>
              <div class="mt-2 bg-red-100 p-2 rounded">
                These are different ways of spreading the quantization errors
                around. Certain ones might work better than others depending on
                the image.
              </div>
            </div>
            <!-- End Algorithm Selector -->
            <!-- Serpentine Dither -->
            <div class="flex flex-col items-center justify-between mt-4">
              <div class="inline-block relative w-full">
                <input
                  id="ditherSerp"
                  v-model="rgbQuantOptions.dithSerp"
                  type="checkbox"
                  class="form-checkbox"
                />
                <label for="ditherSerp" class="ml-2 text-sm"><strong>Serpentine Dither</strong></label>
                <span
                  class="rounded-full h-4 w-4 bg-red-700 text-white flex items-center justify-center float-right text-sm cursor-pointer"
                  @click="showOptionsModalSerp = !showOptionsModalSerp"
                >
                  <span v-if="!showOptionsModalSerp">
                    ?
                  </span>
                  <span v-else>
                    X
                  </span>
                </span>
              </div>
              <div
                v-if="showOptionsModalSerp"
                class="inline-block relative w-full"
              >
                <div class="mt-2 bg-red-100 p-2 rounded">
                  <!-- This could be clarified. -->
                  This determines if the dithering just goes left to right, top
                  to bottom, or does a snake wiggle.
                </div>
              </div>
            </div>

            <!-- End Serpentine Dither -->
          </div>
          
        </div>
        <!-- This is the same as the other report block, this one is for mobile positioning -->
        <div v-show="imageUploaded"
          class="md:hidden"
        >
          <div
            v-if="showDitheredImage && !dithering"
            class="shadow rounded xl:m-0 p-4 mb-4 w-full xl:w-1/4 bg-white "
          >       
            <FilesizeResults 
              :ratio-good="ratioGood"
              :download-file-size="downloadFileSize"
              :selected-image="selectedImage"
              :dithered-height="ditheredHeight"
              :dithered-width="ditheredWidth"
              :download-filesize="downloadFileSize.toFixed(2)"
              :rgbquant="rgbQuantOptions"
            />
          </div>
          <div class="shadow rounded xl:m-0 p-4 w-full xl:w-1/4 bg-white">
            <Donate />
          </div>
        </div>
      </div>
      <!-- End Toolbar -->
      <div
        class="w-full md:w-2/3 lg:w-4/5 flex flex-col xl:flex-row order-first md:order-last items-stretch"
      >

        <!-- Begin Main Display -->
        <div class="md:px-4 flex flex-col flex-1 items-center w-full">
          <!-- Begin Top Toolbar -->
          <div class="flex flex-row justify-between space-x-2 w-full items-center py-2 px-2 mb-4 shadow rounded bg-white" v-if="showDitheredImage && !dithering"> 
            <Toggler class="flex-grow"
            @view-original="viewOriginal = !viewOriginal"
            >View Original</Toggler>  
            <ImageUpload @number-images="getNumberOfImages" @image-upload="onImageUpload" text="✨ New"/>          
            <a
              class="btn-red-outline inline-block self-center"
              target="_blank"
              :href="downloadUrl"
              :download="'dither_it_' + selectedFile.name"
              @click="downloadImage"
              
              
              >💾 Save</a
            >  
          </div>   
          <!-- End Top Toolbar -->
          <!-- Dithered Canvas Display -->
          <div id="dithered-canvas-display" class="max-w-full w-full"  :class="{ 'pt-8': showDitheredImage == true }" v-show="showDitheredImage">


            <div
              v-show="dithering"
              class="flex flex-col justify-center items-center"
            >
              <div class="loader h-20 w-20 mb-4"></div>
              <div class="">Dithering...</div>
            </div>
            <div
              class="flex flex-col justify-center items-center w-full h-full "
              v-show="!dithering && showDitheredImage && !viewOriginal"
            >
              <div
              class="max-w-full "
                v-for="n in this.numberOfImages"
                v-show="selectedImage.id == 'originalImage' + n"
              >
                <canvas :id="'dithered_originalImage' + n" class="max-w-full selectedImage"></canvas>
              </div>
            </div>
          </div>
          <!-- End Dithered Canvas Display -->
          <!-- Original Image Display -->
          
          <div class="w-full flex flex-col ">
          
            <div v-if="numberOfImages > 0">
              <img class="mx-auto max-w-full" :src="selectedImage.src" v-if="!showDitheredImage || viewOriginal"/>
            </div>
            <div class="flex flex-row flex-wrap mt-4 justify-center">
              <div v-for="n in this.numberOfImages" v-show="numberOfImages > 1">
              
                <img
                  :id="'originalImage' + n"
                  class="max-w-full m-4 shadow cursor-pointer"
                  @click="analyzeImagePalette($event.target)"
                  width="75"
                />
              </div>            
            </div>
          </div>
          <!-- End Original Image Display -->
          <!-- Toolbar Stuff -->
          <div
            class="flex flex-row justify-center"
            :class="{ 'w-full': imageUploaded, 'text-sm': imageUploaded }"
          >
            <div
              v-show="showDitheredImage && !dithering"
              class="text-center mt-4 mr-2 justify-center "
            >

            </div>
            <ImageUpload @number-images="getNumberOfImages" @image-upload="onImageUpload" text="✨ Select images" duck="true" v-if="!imageUploaded" />
            
          </div>
          <!-- End Toolbar Stuff -->
        </div>
        <!-- End Main Display -->
        <!-- Begin right sidebar -->
        <div class="w-full xl:w-1/4 hidden md:flex flex-row xl:flex-col gap-4 px-4 xl:m-0 justify-center xl:justify-start" 
        v-show="imageUploaded">
          <div
            v-if="showDitheredImage && !dithering"
            class="shadow rounded bg-white self-start w-1/2 xl:w-full"
          >       
            <FilesizeResults 
              :ratio-good="ratioGood"
              :download-file-size="downloadFileSize"
              :selected-image="selectedImage"
              :dithered-height="ditheredHeight"
              :dithered-width="ditheredWidth"
              :download-filesize="downloadFileSize.toFixed(2)"
              :rgbquant="rgbQuantOptions"
            />
            
          </div>
          <div 
            
            class="shadow rounded xl:m-0 bg-white w-full md:w-1/2 xl:w-full self-start"
          >
            <Donate />
          </div>
        </div>
      </div>
    </div>
    <BottomContent />
    <!-- Fathom - simple website analytics - https://usefathom.com -->
    <script>
      ;(function(f, a, t, h, o, m) {
        a[h] =
          a[h] ||
          function() {
            ;(a[h].q = a[h].q || []).push(arguments)
          }
        ;(o = f.createElement('script')),
          (m = f.getElementsByTagName('script')[0])
        o.async = 1
        o.src = t
        o.id = 'fathom-script'
        m.parentNode.insertBefore(o, m)
      })(document, window, '//cdn.usefathom.com/tracker.js', 'fathom')
      fathom('set', 'siteId', 'AHDLJXNJ')
      fathom('trackPageview')
    </script>
    <!-- / Fathom -->
  </div>
</template>

<script>
import RgbQuant from 'rgbquant'
import Logo from '~/components/Logo.vue'
import ImageUpload from '~/components/ImageUpload.vue'
import ColorPicker from '~/components/ColorPicker.vue'
import InputBlock from '~/components/InputBlock.vue'
import BottomContent from '~/components/BottomContent.vue'
import Toggler from '~/components/Toggler.vue'
import FilesizeResults from '~/components/FilesizeResults.vue'
import Donate from '~/components/Donate.vue'


export default {
  components: {
    ImageUpload,
    ColorPicker,
    Logo,
    InputBlock,
    BottomContent,
    Toggler,
    FilesizeResults,
    Donate
  },
  data() {
    return {
      canvasWidth: 'original', // this is what the dithered canvas renders off of, height comes from a computed property
      fileName: '', // the name of the loaded file
      loading: false, // for the loading spinner
      dithering: false, // for the dithering spinner
      switchingImages: false,
      specsCalculated: false, // for the specs
      imageUploaded: false,
      showDitheredImage: false, // control if the dithered image is visible
      imageWidths: [320, 640, 1080, 1280],
      ditherMode: 'Error Diffusion',
      ditherModeOptions: [
        'Error Diffusion',
        'Bayer (Ordered)'
      ],
      algorithmOptions: [
        'FloydSteinberg',
        'FalseFloydSteinberg',
        'Stucki',
        'Atkinson',
        'Jarvis',
        'Burkes',
        'Sierra',
        'TwoSierra',
        'SierraLite'
      ],
      rgbQuantOptions: {
        // ---- Options ------
        colors: 8, // desired palette size
        method: 2, // histogram method, 2: min-population threshold within subregions; 1: global top-population
        boxSize: [8, 8], // subregion dims (if method = 2)
        boxPxls: 2, // min-population threshold (if method = 2)
        initColors: 4096, // # of top-occurring colors to start with (if method = 1)
        minHueCols: 2000, // # of colors per hue group to evaluate regardless of counts, to retain low-count hues
        dithKern: 'FloydSteinberg', // dithering kernel name, see available kernels in docs below
        dithDelta: 0, // dithering threshhold (0-1) e.g: 0.05 will not dither colors with <= 5% difference
        dithSerp: false, // enable serpentine pattern dithering
        palette: [], // a predefined palette to start with in r,g,b tuple format: [[r,g,b],[r,g,b]...]
        reIndex: false, // affects predefined palettes only. if true, allows compacting of sparsed palette once target palette size is reached. also enables palette sorting.
        useCache: true, // enables caching for perf usually, but can reduce perf in some cases, like pre-def palettes
        cacheFreq: 10, // min color occurance count needed to qualify for caching
        colorDist: 'euclidean' // method used to determine color distance, can also be "manhattan"
      },
      showOptionsModalSize: false,
      showOptionsModalAlgo: false,
      showOptionsModalSerp: false,
      showDitherModeModal: false,
      downloadUrl: '', // the url src thing to download the image
      downloadFileSize: '50', // the filesize of the download
      originalFileSize: '', // filesize of the original
      imageType: '',
      numberOfImages: '',
      images: [],
      selectedImage: '',
      ditheredWidth: '',
      ditheredHeight: '',
      viewOriginal: false,
      selectingImage: false,
      customWidth: false,
      isError: false,
      baseColors: [ // this is from old bayer stuff and can prob be deleted
        {
          "hex": "#FFFFFF",
          "name": "White",
        },
        {
          "hex": "#000000",
          "name": "Black",
        },
        {
          "hex": "#808080",
          "name": "Grey",
        },
        
        {
          "hex": "#ff0000",
          "name": "Red",
        },
        {
          "hex": "#ffa500",
          "name": "Orange",
        },
        {
          "hex": "#ffff00",
          "name": "Yellow",
        },
        {
          "hex": "#008000",
          "name": "Green",
        },
        {
          "hex": "#0000ff",
          "name": "Blue",
        },
        {
          "hex": "#4b0082",
          "name": "Indigo",
        },
        {
          "hex": "#ee82ee",
          "name": "Violet",
        },
      ]      
    }
  },
  computed: {
    ratioGood() {
      if (this.downloadFileSize / (Math.round(((this.selectedImage.src.length) * 3) / 4) / 1000) < 1) {
        return true
      } else {
        return false
      }
    },
    selectedFile() {
      return this.images.find(this.getSelected)
    },
    computedHeight() {
      console.log(this.selectedImage);
      console.log(this.selectedImage.width);
      console.log(this.selectedImage.height);
      console.log(this.canvasWidth)
      if(this.canvasWidth === 'original') {
        console.log('hello')
        return this.selectedImage.naturalHeight
      } else {
        console.log('goodbye')
        return (this.selectedImage.naturalHeight / this.selectedImage.naturalWidth) * this.canvasWidth
      }
      
    }
  },
  methods: {
    // ---------------------------
    // Set the image id to the id of the currently selected image
    // ----------------------------    
    getSelected(image) {
      console.log('Get selected image.')
      return image.id === this.selectedImage.id;
    },
    // ---------------------------
    // Get the number of images
    // ----------------------------        
    getNumberOfImages(number) {
      console.log('Get the number of images.')
      this.numberOfImages = number
    },
    // ---------------------------
    // Create the downloadable image
    // And get the new file specs
    // ----------------------------            
    downloadImage() {
      console.log('Function downloadImage called')

      this.ditheredWidth = document.getElementById('dithered_' + this.selectedImage.id).width
      this.ditheredHeight = document.getElementById('dithered_' + this.selectedImage.id).height

      const ditheredImageCanvas = document.getElementById('dithered_' + this.selectedImage.id) // the canvas that holds the dithered image
      const downloadUrl = ditheredImageCanvas.toDataURL(this.imageType, 0.72)
      this.downloadFileSize = Math.round((downloadUrl.length * 3) / 4) / 1000
      this.downloadUrl = downloadUrl

      fathom('trackGoal', 'UAT4LRNZ', 0)
    },
    // ---------------------------
    // This receives a palette from ColorPicker in the form of an array of hex values
    // ----------------------------        
    onUpdatePalette(palette) {
      console.log('Color palette updating:')
      console.log(palette.length)
      this.rgbQuantOptions.colors = palette.length
      this.rgbQuantOptions.palette = []
      palette.forEach((v, i) => {
        this.rgbQuantOptions.palette.push(v)
      })
    },
    // ---------------------------
    // When images get uploaded
    // ----------------------------       
    onImageUpload(images) {
      console.log('Process the uploaded images.')
      this.images = images // load the image array into data

      fathom('trackGoal', 'HORTCOPW', 0)

      this.selectedImage = document.getElementById('originalImage1') // set the selected image

      // Dithered image should not be showing yet
      this.showDitheredImage = false

      // Clear the paletter (it might be set from old uploads)
      this.rgbQuantOptions.palette = []

      // Set imageUploaded to true
      this.imageUploaded = true

      // Tell the palette selector to be set to original
      this.$children[1]._data.presetPaletteSelection = 'original'

      // Wait a second before analyzing the image palette (presumably to let the image load?)
      setTimeout(() => {
        this.analyzeImagePalette(document.getElementById('originalImage1'))
      }, 100)
    },
    // ---------------------------
    // Dither the images
    // ----------------------------
    ditherImage() {
      console.log('Dither the images')
      fathom('trackGoal', 'SFMGAORY', 0)

      // When dithering starts, go back to the top
      window.scrollTo(0, 0) 
      
      // Dithering has started
      this.dithering = true 

      // Hide the original images
      this.viewOriginal = false

      // Show the dithered image
      this.showDitheredImage = true

      // Dont show stats while dithering is happening
      this.selectingImage = true

      setTimeout(() => {

        // Go through each of the images and...
        for (let i = 1; i < this.numberOfImages + 1; i++) {
          // Get the original image
          const originalImage = document.getElementById('originalImage' + i)
          // Get the canvas where the dithered image will go
          const ditheredImageCanvas = document.getElementById('dithered_originalImage' + i)
          // Get the canvas context
          const ctx = ditheredImageCanvas.getContext('2d')
          // Set an arbitrary initial width
          var width = 1
          // If the canvas width param is set to 'Original'
          if (this.canvasWidth === 'original') {
            // Set the canvas width to the original image width
            // eslint-disable-next-line no-const-assign
            width = originalImage.naturalWidth
          } else if (this.canvasWidth === 'custom') { 
            width = this.customWidth
          } else {
            // Otherwise, set it to whatever is selected
            // eslint-disable-next-line no-const-assign
            width = this.canvasWidth
          }
          // Set the height based on the original ratio and the determined width
          const height = (originalImage.naturalHeight / originalImage.naturalWidth) * width
          // Tell the canvas which width and height to be
          ctx.canvas.width = width
          ctx.canvas.height = height
          // Put the image on the canvas
          ctx.drawImage(originalImage, 0, 0, width, height)

          if(this.ditherMode != 'Error Diffusion') {

            this.bayerDither(ctx, ctx.getImageData(0, 0, width, height))
            fathom('trackGoal', 'Q3QWCGJU', 0)
            this.downloadImage()
          } else {
            // Create new RgbQuant instance
            const q = new RgbQuant(this.rgbQuantOptions)
            // Analyze histograms to get colors
            q.sample(originalImage) 
            // Dither what is on the canvas
            const ditherResult = q.reduce(ditheredImageCanvas) 
            //console.log(ditherResult) // this is a Uint8Array of all of the pixels as RGB values where every 3 values is an RGB value like 255,0,0 etc.
            // Get the newly dithered image data
            const imgData = ctx.getImageData(0, 0, width, height)
            // Set the value of imageData to the dithered image data
            imgData.data.set(ditherResult) 
            // Put the new image data on the canvas
            ctx.putImageData(imgData, 0, 0)
            // Set the data for the image download
            this.downloadImage()
          }

        }
        // Turn off dithering indicator
        this.dithering = false
        // Show stats
        this.selectingImage = false
      }, 100)
      // Set the data for the image download (again?)
      this.downloadImage()
    },

    bayerDither(ctx, imageData) {

      var bayerThresholdMap = [
        [  15, 135,  45, 165 ],
        [ 195,  75, 225, 105 ],
        [  60, 180,  30, 150 ],
        [ 240, 120, 210,  90 ]
      ];


      var imageDataLength = imageData.data.length;

      var w = imageData.width;


      const oldPalette = this.rgbQuantOptions.palette.slice()
      var newPalette = [];

      oldPalette.forEach((color, id) => {

          var newColor = [id].concat(color)
          
          newPalette.push(newColor)
        }
      )

      // Go through the RGBA data, at every 4th value, each of which corresponds to a pixel
      for (var currentPixel = 0; currentPixel <= imageDataLength - 4; currentPixel+=4) {
        
        // console.log(currentPixel + ': ' + this.nearestColor({ r: imageData.data[currentPixel], g: imageData.data[currentPixel + 1], b: imageData.data[currentPixel + 2] }).name)

        // This tells what the curent x coordinate is
        var x = currentPixel/4 % w;
        // This tells what the current y coordinate is
        var y = Math.floor(currentPixel/4 / w);
        // This finds the right Bayer matrix value based on the x/y coordinate of the current pixel
        // why is it divided by 2??
        
        // here we assign the RGBA data to the image
        // For each pixel there are three values we care about R, G, B
        // For each of those three values:
        // Add the original (imageData.data[currentPixel])
        // To the threshold map value (bayerThresholdMap[x%4][y%4])
        // Divide it by 2 to get the new R, G or B value
        // If that value is deemed to be "light", meaning it is less than 129, make it 0
        // If it is "dark", make it 255
        // Doing this, you will end up with RGB triple like 0 255 255
        // There are 8 of these possible colors
        // R | G | B |  Color
        //------------------
        // 0   0   0    Black
        // 0   0   255  Blue
        // 0   255 0    Green
        // 255 0   0    Red
        // 0   255 255  Cyan
        // 255 0   255  Magenta
        // 255 255 0    Yellow
        // 255 255 255  White
        //-------------------
        // Then you replace the old values in the imagedata array with the new values
        // This replaces all of the colors in this image with one of these eight colors
        // 
    
        // Update 08/01/22
        // Now this uses the "getClosestColor" function and the preset ditherit palette to do cusotm color bayer dithers.


        var map = Math.floor( (imageData.data[currentPixel] + bayerThresholdMap[x%4][y%4]) / 2 );
        // imageData.data[currentPixel] = (map < 129) ? 0 : 255;  
        
        var map2 = Math.floor( (imageData.data[currentPixel + 1] + bayerThresholdMap[x%4][y%4]) / 2 );    
        // imageData.data[currentPixel + 1] = (map2 < 129) ? 0 : 255;  
        
        var map3 = Math.floor( (imageData.data[currentPixel + 2] + bayerThresholdMap[x%4][y%4]) / 2 );
        // imageData.data[currentPixel + 2] = (map3 < 129) ? 0 : 255;  
        
 

        const closest_color = this.getClosestColor(newPalette, [map, map2, map3]);
       
        imageData.data[currentPixel] = closest_color[1]
        imageData.data[currentPixel + 1] = closest_color[2]
        imageData.data[currentPixel + 2] = closest_color[3]
      


      }
    
      // Put the new image data on the canvas
      ctx.putImageData(imageData, 0, 0)

    },
    // https://stackoverflow.com/a/69880443/2201446
    getClosestColor(colors, [r2, g2, b2]) {
      const [[closest_color_id]] = (
        colors
        .map(([id,r1,g1,b1]) => (
          [id, Math.sqrt((r2-r1)**2 + (g2-g1)**2 + (b2-b1)**2)]
        ))
        .sort(([, d1], [, d2]) => d1 - d2)
      );
      return colors.find(([id]) => id == closest_color_id);
    },
    // ---------------------------
    // Analyze the image palette
    // ----------------------------    
    analyzeImagePalette(e) {
      console.log('Analyze the image palette.')
      console.log('New image selected.')
       this.selectingImage = true
       setTimeout(() => {
        this.selectedImage = e
      
        

        this.rgbQuantOptions.palette = []

        // create new RgbQuant instance
        const q = new RgbQuant(this.rgbQuantOptions)
        // analyze histograms
        q.sample(e)

        this.rgbQuantOptions.palette = q.palette(true)

        this.specsCalculated = true

        if(this.showDitheredImage) {
          this.ditheredWidth = document.getElementById('dithered_' + this.selectedImage.id).width
          this.ditheredHeight = document.getElementById('dithered_' + this.selectedImage.id).height

          const ditheredImageCanvas = document.getElementById('dithered_' + this.selectedImage.id) // the canvas that holds the dithered image
          const downloadUrl = ditheredImageCanvas.toDataURL(this.imageType, 0.72)
          this.downloadFileSize = Math.round((downloadUrl.length * 3) / 4) / 1000
          this.downloadUrl = downloadUrl        
        }

        this.$children[1]._data.presetPaletteSelection = 'original'
        console.log('New image selected.')
        this.selectingImage = false
      }, 50)
      
    },
    // fathom(id) {
    //   fathom('trackGoal', id, 0)
    // },
    focus() {
      
      this.canvasWidth = this.selectedImage.naturalWidth
      this.$nextTick(() => this.$refs.customWidthField.focus())
    },
    validateWidth() {
      fathom('trackGoal', 'MHEE0ZOY', 0)
      if(this.canvasWidth > 5000) {
        this.isError = true
      } else {
        this.isError = false
      }
    },
    // FROM https://gist.github.com/Ademking/560d541e87043bfff0eb8470d3ef4894
    // from https://stackoverflow.com/a/5624139
    hexToRgb(hex) {
      var shorthandRegex = /^#?([a-f\d])([a-f\d])([a-f\d])$/i;
      hex = hex.replace(shorthandRegex, function(m, r, g, b) {
        return r + r + g + g + b + b;
      });

      var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
      return result ? {
        r: parseInt(result[1], 16),
        g: parseInt(result[2], 16),
        b: parseInt(result[3], 16)
      } : null;
    },


    // return nearest color from array
    // nearestColor(colorRGB){
    //   var lowest = Number.POSITIVE_INFINITY;
    //   var tmp;
    //   let index = 0;
    //   this.baseColors.forEach( (el, i) => {
    //     // console.log(colorRGB)
    //       tmp = this.distance(colorRGB, this.hexToRgb(el.hex))
    //       if (tmp < lowest) {
    //         lowest = tmp;
    //         index = i;
    //       };
          
    //   })
    //   // This is the palette as it is set in Dither it!
    //   // console.log(this.rgbQuantOptions.palette)
    //   // This is the default color list provided by the Gist answer being cribbed from 
    //   // console.log(this.baseColors)
    //   // This needs to be changed to search from the dither it palette
    //   return this.baseColors[index];
      
    // }
  }
}
</script>

<style >
  .checkers {
    background-color: #fcfffb;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='100%25' height='100%25'%3E%3Cdefs%3E%3Cpattern id='p' width='100' height='100' patternUnits='userSpaceOnUse' patternTransform='scale(0.36)'%3E%3Cpath data-color='outline' fill='none' stroke='%239D9D9D' stroke-width='0.25' d='M50 0v100M100 50H0'%3E%3C/path%3E%3C/pattern%3E%3C/defs%3E%3Crect fill='url(%23p)' width='100%25' height='100%25'%3E%3C/rect%3E%3C/svg%3E");
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
  }
</style>
