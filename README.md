# zmber-upload-base64
emementui组件获取图片Base64

使用：
 ```
 <template>
     <Upload v-on:getFileBase64="getFileBase64" ref="fileUpload" />

  import Upload from "@/***/components/Upload";
  
  export default {
    name: "OrgForm",
    components: { Upload },
    methods: {
      getRequestFileBase64: function(e) {
        console.log(e); //图片base64
      }, 
  }
