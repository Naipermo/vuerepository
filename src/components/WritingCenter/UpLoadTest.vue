<template>
  <el-upload
    class="avatar-uploader"
    :data="dataObj"
    action="https://up-z2.qiniup.com"
    list-type="picture-card"
    :show-file-list="false"
    :on-error="handleError"
    :on-success="handleSuccessVideo"
    :on-progress="uploadProcess"
    :before-upload="videoBeforeUpload"
    :on-remove="handleRemove">
    <video v-if="imageUrl.length > 1 && imgFlag == false"  controls="controls" :src="[qiniuUrl + '/' + imageUrl]" class="avatar"></video>
    <i v-else-if="imageUrl.length < 1 && imgFlag == false" class="el-icon-plus avatar-uploader-icon"></i>
    <el-progress v-if="imgFlag == true" type="circle" :percentage="percent" style="margin-top: 20px"></el-progress>
  </el-upload>
</template>

<script>
  export default {
    name: "UpLoadTest",
    data() {
      return {
        qiniuUrl: 'https://up.demoworld.com/',  // 个人七牛访问前缀
        imgFlag: false,
        imageUrl: [],
        percent: 0,
        dialogImageUrl: '',
        dialogVisible: false,
        listObj: {},
        dataObj: { token: '111o8CXprRY-dhCawNZM7LQMNs-4y1pEmUlzbuMExv9:Xqu54BdpgEykaYpZf4IKGJQpaNc=:eyJzY29wZSI6ImIyZXJwIiwiZGVhZGxpbmUiOjE1NjAyMTkxMDB9' } // 此处七牛token写成常量方便调试，正式环境需要获取token
      };
    },
    methods:{
      handleRemove(file, fileList) {
        this.imageUrl = '';
      },
      handleSuccessVideo(response) {
        console.log(123, response);
        this.imgFlag = false;
        this.percent = 0;
        if (response.hash) {
          this.imageUrl = response.hash;
        } else {
          this.$message.error('视频上传失败，请重新上传！');
        }
      },
      handleError(err, file, fileList) {
        // 上传失败异常处理
        const error = JSON.parse(JSON.stringify(err));
        console.log(err)
        console.log(error)
        this.$message.error(error.status.toString());
        this.imgFlag = false;
        this.percent = 0;
      },
      videoBeforeUpload(file) {
        console.log(file);
        const _self = this;
        const isVideo = file.type === 'video/mp4' || file.type === 'video/ogg' || file.type === 'video/flv' || file.type === 'video/avi' || file.type === 'video/wmv' || file.type === 'video/rmvb' || file.type ==='video/x-flv';
        const isLt30M = file.size / 1024 / 1024 < 300;
        if (!isVideo) {
          this.$message.warning('请上传正确格式的视频！');
          return false;
        } else {
          if (!isLt30M) {
            this.$message.warning('上传视频文件大小不能超过 30MB!');
            return false;
          }
        }
        // 如果要实时获取七牛token 需要使用以下代码
        // const fileName = file.uid;
        // this.listObj[fileName] = {};
        // return new Promise((resolve, reject) => {
        //   getToken().then(response => {
        //     const data = response.data;
        //     if (data.success) {
        //       const tokens = data.data.token;
        //       _self._data.dataObj.token = tokens;
        //       // _self._data.dataObj.key = key
        //       _self.listObj[fileName] = { hasSuccess: false, uid: fileName };
        //       // _self.imgList['skuImageVideo'] = { hasSuccess: false, uid: fileName };
        //       resolve(true);
        //     } else {
        //       this.$message.warning('获取七牛token异常，请刷新后重试！');
        //     }
        //   }).catch(err => {
        //     console.log(err);
        //     reject(false);
        //   });
        // });
      },
      uploadProcess(event, file, fileList) {
        this.imgFlag = true;
        console.log(event.percent);
        this.percent = Math.floor(event.percent);
      },
    }
  }
</script>

<style scoped>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
    width: 178px;
    height: 178px;
  }

  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }

  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }

  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }

  .image-preview {
    width: 178px;
    height: 178px;
    position: relative;
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    float: left;
  }

  .image-preview .image-preview-wrapper {
    position: relative;
    width: 100%;
    height: 100%;
  }

  .image-preview .image-preview-wrapper img {
    width: 100%;
    height: 100%;
  }

  .image-preview .image-preview-action {
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    cursor: default;
    text-align: center;
    color: #fff;
    opacity: 0;
    font-size: 20px;
    background-color: rgba(0, 0, 0, .5);
    transition: opacity .3s;
    cursor: pointer;
    text-align: center;
    line-height: 200px;
  }

  .image-preview .image-preview-action .el-icon-delete {
    font-size: 32px;
  }

  .image-preview:hover .image-preview-action {
    opacity: 1;
  }


</style>

