<template>
  <div>
    <el-upload
      action="#"
      accept=".png,.jpg"
      list-type="picture-card"
      :show-file-list="showList"
      :auto-upload="false"
      :on-preview="handlePictureCardPreview"
      :on-change="handleChange"
      :on-remove="handleRemove"
      :limit="limitCount"
      :class="{ hide: hideUpload }"
    >
      <i class="el-icon-plus"></i>
    </el-upload>
    <el-dialog :visible.sync="dialogVisible">
      <img width="100%" :src="dialogImageUrl" alt="" />
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "Upload",
  data() {
    return {
      dialogImageUrl: undefined,
      dialogVisible: false,
      showList: true,
      limitCount: 1,
      hideUpload: false
    };
  },
  methods: {
    //图片改变
    async handleChange(file, fileList) {
      let flag = this.beforeUpload(file);
      if (!flag) {
        //失败不显示印章列表
        this.showList = false;
      } else {
        this.showList = true;
        this.hideUpload = fileList.length >= this.limitCount;
        this.$emit("getFileBase64", await this.getBase64(file));
      }
    },
    //图片移除
    async handleRemove(file, fileList) {
      this.hideUpload = fileList.length >= this.limitCount;
      this.$emit("getFileBase64", undefined);
    },
    //图片预览
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },

    // 将file转base64
    getBase64(file) {
      return new Promise(function(resolve, reject) {
        let reader = new FileReader();
        let imgResult = "";
        reader.readAsDataURL(file.raw);
        reader.onload = function() {
          imgResult = reader.result;
        };
        reader.onerror = function(error) {
          reject(error);
        };
        reader.onloadend = function() {
          resolve(imgResult);
        };
      });
    },

    //格式大小校验
    beforeUpload(file) {
      let type = file.raw.type;
      const isJPG = type === "image/jpeg";
      const isPng = type === "image/png";
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG && !isPng) {
        this.msgError("文件格式错误，请上传图片类型,如：JPG，PNG后缀的文件。");
        return false;
      }
      if (!isLt2M) {
        this.$message.error("上传图片大小不能超过 2MB!");
        return false;
      }

      return true;
    }
  }
};
</script>

<style>
.hide .el-upload--picture-card {
  display: none;
}
</style>
