<template lang="html">
  <div class="editor">
    <div ref="toolbar" class="toolbar">
    </div>
    <div ref="editor" class="text">
    </div>
  </div>
</template>

<script>
import E from "wangeditor";
import request from "@/utils/request";
export default {
  name: "WangEditor",
  data() {
    return {
      // uploadPath,
      editor: null,
      info_: null,
      qntokens: "",
      //tokens:"2qvZqxY40jmIKg5eLip0NCNd0pV4H3PDcPYuDA5M:tc3c7fb2IZM2pD0F4NTZpv45dkk=:eyJzY29wZSI6InBpYy1zZXQiLCJkZWFkbGluZSI6MTYwNTA5MDgzN30="
    };
  },
  model: {
    prop: "value",
    event: "change",
  },
  props: {
    value: {
      type: String,
      default: "",
    },
    isClear: {
      type: Boolean,
      default: false,
    },
    tokens: {
      type: String,
    },
  },
  watch: {
    isClear(val) {
      // 触发清除文本域内容
      if (val) {
        this.editor.txt.clear();
        this.info_ = null;
      }
    },
    value: function (value) {
      if (value !== this.editor.txt.html()) {
        this.editor.txt.html(this.value);
      }
    },
    // tokens:function(val){
    // //   if(val != undefined && val != "" && val != null){
    // //     this.seteditor()
    // // this.editor.txt.html(this.value)
    // //   }
    // }
    //value为编辑框输入的内容，这里我监听了一下值，当父组件调用得时候，如果给value赋值了，子组件将会显示父组件赋给的值
  },
  created() {
    // this.getEditToken()
  },
  mounted() {
    this.getEditToken();
  },
  methods: {
    getEditToken() {
      const url = "http://qnpic.haolist.cn";
      request({
        url,
        method: "get",
        params: "",
      }).then((res) => {
        this.qntokens = res;
        this.seteditor();
        this.editor.txt.html(this.value);
      });
    },
    seteditor() {
      // http://192.168.2.125:8080/admin/storage/create
      this.editor = new E(this.$refs.toolbar, this.$refs.editor);
      this.editor.config.uploadImgShowBase64 = false; // base 64 存储图片
      this.editor.config.uploadImgServer =
        "http://up-z0.qiniup.com/?token=" + this.qntokens; // 配置服务器端地址
      this.editor.config.uploadImgHeaders = {}; // 自定义 header
      this.editor.config.uploadFileName = "file"; // 后端接受上传文件的参数名
      // this.editor.config.uploadImgMaxSize = 2 * 1024 * 1024 // 将图片大小限制为 2M
      // this.editor.config.uploadImgMaxLength = 3; // 限制一次最多上传 10 张图片
      // this.editor.config.uploadImgTimeout =   10000 * 1000 // 设置超时时间
      this.editor.config.qiniu = true; // 允许上传七牛云
      this.editor.config.showLinkImg = false; // 隐藏“网络图片”tab

      // 配置菜单
      this.editor.config.menus = [
        "head", // 标题
        "bold", // 粗体
        "fontSize", // 字号
        "fontName", // 字体
        // 'italic', // 斜体
        // 'underline', // 下划线
        // 'strikeThrough', // 删除线
        "foreColor", // 文字颜色
        //'backColor', // 背景颜色
        //'link', // 插入链接
        // 'list', // 列表
        "justify", // 对齐方式
        //'quote', // 引用
        //'emoticon', // 表情
        "image", // 插入图片
        // 'table', // 表格
        //'video', // 插入视频
        //'code', // 插入代码
        "undo", // 撤销
        "redo", // 重复
      ];

      // this.editor.config.uploadImgHooks = {
      //   fail: (xhr, editor, result) => {
      //     // 插入图片失败回调
      //     console.log("fail", result);
      //   },
      //   success: (xhr, editor, result) => {
      //     // 图片上传成功回调
      //     console.log("success", result);
      //   },
      //   timeout: (xhr, editor) => {
      //     // 网络超时的回调
      //     console.log("timeout", result);
      //   },
      //   error: (xhr, editor) => {
      //     // 图片上传错误的回调
      //     console.log("error", result);
      //   },
      //   customInsert: (insertImg, result, editor) => {
      //     // 图片上传成功，插入图片的回调
      //     //result为上传图片成功的时候返回的数据，这里我打印了一下发现后台返回的是data：[{url:"路径的形式"},...]
      //     console.log(result, editor, "1111111111111");
      //     let url = "https://cdn.shangwulink.com/" + result.key;
      //     insertImg(url);
      //   },
      // };

      // 使用自己写的上传方法 选择多个文件 循环请求七牛云 然后把key值insert进去 避开七牛云单次只能上传一张图片的问题
      this.editor.config.customUploadImg = (files, insert) => {
        var url = "http://up-z0.qiniup.com/?token=" + this.qntokens;
        for (var i = 0; i < files.length; i++) {
          var formData = new FormData();
          formData.append("file", files[i], files[i].name);
          request({
            url,
            method: "post",
            data: formData,
          }).then((res) => {
            insert("https://cdn.shangwulink.com/" + res.key);
          });
        }
      };

      this.editor.config.onchange = (html) => {
        this.info_ = html; // 绑定当前逐渐地值
        this.$emit("change", this.info_); // 将内容同步到父组件中
      };
      // 创建富文本编辑器
      this.editor.create();
    },
  },
};
</script>

<style lang="css">
.editor {
  width: 100%;
  /* height: 250px;
    overflow: scroll; */
  margin: 0 auto;
  position: relative;
  z-index: 0;
}
.toolbar {
  border: 1px solid #ccc;
}
.text {
  border: 1px solid #ccc;
  min-height: 250px;
}
</style>