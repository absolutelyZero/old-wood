<template>
  <div>
    <el-row>
      <quill-editor
        ref="myQuillEditor"
        v-model="currentContent"
        @blur="onEditorBlur($event)"
        @focus="onEditorFocus($event)"
        @change="onEditorChange($event)"
      ></quill-editor>
    </el-row>
    <el-upload
      class="uploadfile"
      style="display:none"
      action
      :http-request="uploadFileMethod"
      :show-file-list="false"
      :before-upload="newEditorbeforeupload"
      ref="uniqueId"
      id="uniqueId"
      multiple
    ></el-upload>
    <el-input v-model="currentanswer" placeholder="请输入正确答案" size="mini" style="width:50%"></el-input>
    <el-button type="primary" size="mini" style="margin-top:20px;" @click="childClick">提交</el-button>
  </div>
</template>
<script>
import Config from "../config/config.js";
import axios from "axios";
import Quill from "quill";
const apiBaseUrl = Config.apiBaseUrl;
export default {
  data: function() {
    return {
      currentContent: this.content,
      currentanswer:this.answer,
      // editorOption: {
      //   modules: {
      //     toolbar: [
      //       [{ size: ["small", false, "large"] }],
      //       ["bold", "italic"],
      //       [{ list: "ordered" }, { list: "bullet" }],
      //       ["link", "image"]
      //     ]
      //   }
      // },
      addImgRange: []
    };
  },
  props: {
    content: String,
    answer:String,
    required: true
  },
  // 页面加载后执行 为编辑器的图片图标和视频图标绑定点击事件
  mounted() {
    var imgHandler = async function(state) {
      if (state) {
        var fileInput = document.querySelector("#uniqueId input"); //隐藏的file元素
        fileInput.click(); //触发事件
      }
    };
    // 为图片ICON绑定事件  getModule 为编辑器的内部属性
    this.$refs.myQuillEditor.quill
      .getModule("toolbar")
      .addHandler("image", imgHandler);
  },
  methods: {
    uploadFileMethod(param) {
      console.log(param);
      let fileObject = param.file;
      let formData = new FormData();
      formData.append("file", fileObject);
      let courseId = window.localStorage.getItem("courseId");
      let sessionId = window.localStorage.getItem("sessionId");
      let api = {
        url: apiBaseUrl + "school/courses/" + courseId + "/upload",
        tag: 1,
        method: "post"
      };
      console.log("formData");
      console.log(formData);
      this.http(api, formData).then(res => {
        if (res.data.code == "0000") {
          // this.$message({
          //   type:'success',
          //   message:'图片上传成功'
          // })
          this.addImgRange = this.$refs.myQuillEditor.quill.getSelection();
          this.$refs.myQuillEditor.quill.insertEmbed(
            this.addImgRange != null ? this.addImgRange.index : 0,
            "image",
            res.data.data,
            Quill.sources.USER
          );
        }
      });
    },
    newEditorbeforeupload(file) {
      const isJPG = file.type === "image/jpeg" || file.type === "image/png";
      const isLt2M = file.size / 1024 / 1024 < 2;
      if (!isJPG) {
        this.$message.error("上传图片只能是 JPG或PNG 格式!");
      }
      if (!isLt2M) {
        this.$message.error("上传图片大小不能超过 2MB!");
      }
      if (isJPG && isLt2M) this.imageLoading = true;
      return isJPG && isLt2M;
    },
    childClick() {
      // childByValue是在父组件on监听的方法
      // 第二个参数this.childValue是需要传的值
      this.$emit("childByValue", this.currentContent , this.currentanswer);
      // this.$emit("childByAnswer",this.answer)
    },
    onEditorBlur(editor) {
      //失去焦点事件
    },
    onEditorFocus(editor) {
      //获得焦点事件
    },
    onEditorChange({ editor, html, text }) {}
  }
};
</script>
<style lang="less" scoped>
.el-row {
  width: 100%;
  height: 400px;
  .quill-editor {
    height: 350px;
  }
}
</style>
