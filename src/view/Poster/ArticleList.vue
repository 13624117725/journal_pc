<template>
    <div class='articlePage'>
        <!-- 面包屑 -->
        <div class="bread">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item v-for="(item,index) in breadArr" :key="index" :to="{path:item.path}">{{item.name}}</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="outerLayer">
            <div class="leftList">
                <div class="article_List" :key="index" v-for="(item,index) in articleListData" v-dragging="{item:item,list:articleListData,group:'item'}" @click="articleListClick(item)">
                    <div :style="`background:url('${item.imgurl+ '?imageView2/1/w/80/h/80'}') center center / 60% no-repeat;`"></div>
                    <div><a>{{item.articlename}}</a></div>
                    <div><el-button size="mini" type="danger" circle icon="el-icon-close"  @click.stop="articleListDelete(item)"></el-button></div>
                </div>
                <el-button circle icon="el-icon-plus" type="primary" style="margin-top:.1rem;" @click="addArticleClick"></el-button>
            </div>
            <div class="rightText">
                <div class="topDiv">
                    <div class='fileInfo'>
                      <div class ="fromLine"> 
                            <el-form ref="form" :rules="rules" :model="ElectronicJournalInfo" label-width=".5rem" class='topFrom'>
                              <el-form-item style="width:50%;float:left;" label="章节名称" prop='articlename'>
                                <el-input :maxlength="60" v-model="ElectronicJournalInfo.articlename"  placeholder="请输入文章名称"></el-input>
                              </el-form-item>
                              <el-form-item label="弹幕显示" style="width:20%;float:left;">
                                <el-switch
                                  v-model="ElectronicJournalInfo.showbarrage"
                                  active-color="#13ce66"
                                  :active-value='1'
                                  :inactive-value='0'
                                  inactive-color="#cccccc">
                                </el-switch>
                              </el-form-item>
                            </el-form>
                      </div>
                        <div class ="fromLine" >
                          <el-form ref="form" :rules="rules" :model="ElectronicJournalInfo" label-width=".4rem" class='topFrom'>
                            <el-form-item label="封面" style="width:20%;float:left;" prop='imgurl'>
                                <el-upload
                                    style="text-align:left;"
                                    class="avatar-uploader"
                                    :action="UPLOAD_IMAGE"
                                    :data='{pid:"121"}'
                                    :show-file-list="false"
                                    :on-success="handleAvatarSuccess"
                                >    
                                    <div class='smallImgDiv' v-if="ElectronicJournalInfo.imgurl" style="position:relative;">
                                      <img :src="ElectronicJournalInfo.imgurl" class="avatar">
                                      <div class="enlarge">
                                        <img style="width:100%;" :src="ElectronicJournalInfo.imgurl" alt="">
                                      </div>
                                    </div>
                                    <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                                </el-upload>
                            </el-form-item>
                            <el-form-item style="width:10%;float:left;">
                               <el-checkbox v-model="checkBox" label="封面是否添加到头部"></el-checkbox>
                            </el-form-item>
                            <el-form-item prop="topic" style="width:30%;float:left;margin-left:20%"  label="栏目" >
                                <el-input :maxlength="20" v-model="ElectronicJournalInfo.topic" placeholder="请输入栏目"></el-input>
                            </el-form-item>
                          </el-form>
                        </div>
                        <div class ="fromLine" >
                          <el-form ref="form" :rules="rules" :model="ElectronicJournalInfo" label-width=".4rem" class='topFrom'>
                            <el-form-item  label="类型" style="width:30%;float:left;" prop="articletype">
                                <el-select style="width:100%;" placeholder="请选择" v-model="ElectronicJournalInfo.articletype" @change="changEntryType1">
                                    <el-option-group
                                        v-for="group in entryOptions"
                                        :key="group.label"
                                        :label="group.label">
                                        <el-option
                                            v-for="item in group.options"
                                            :key="item.value"
                                            :label="item.label"
                                            :value="item.value">
                                        </el-option>
                                    </el-option-group>
                                </el-select>
                            </el-form-item>
                            <el-form-item prop="content"  v-show="ElectronicJournalInfo.articletype == '外部链接' || ElectronicJournalInfo.articletype == 1" style="width:30%;float:left;margin-left:.2rem;" label="链接地址">
                                <el-input v-model="outerLineUrl" placeholder="请输入完整链接地址"></el-input>
                            </el-form-item>
                            <el-form-item style="width:34%;float:right;margin-right:.05rem;">
                                <el-button type="success" style="text-align:center;margin-bottom:.01rem;float:left;" @click="previewDialogVisible = true">预览</el-button>
                                <el-button type="primary" style="text-align:center;margin-bottom:.01rem;float:left;" @click="saveClick">保存</el-button>
                                <el-button type="primary" style="text-align:center;margin-bottom:.01rem;float:left;" @click="beforeClick">返回</el-button>
                            </el-form-item>
                          </el-form>
                        </div>
                    </div>
                </div>
                <div class="textDiv"> 
                   <froala  class="editor-box" v-if="config" :tag="'textarea'" :config="config" v-model="textModel"></froala>
                </div>
               
            </div>
        </div>
        <footer class="footer">
            
        </footer>
        <manageprive :previewDialogVisible="previewDialogVisible" :url="previewUrl" @close="previewDialogVisible = false" />
        
        <!-- 声音录入的提示框 -->
        <el-dialog
            title="选取声音文件"
            :visible.sync="dialogVisible"
            width="30%"
            >
            <el-upload
            class="upload-demo"
            style="text-align:center;"
            :data="{pid:122}"
            :action="UPLOAD_IMAGE"
            :limit="limit"
            :on-success="singleUpLoadSuccess"
            :file-list="fileList"
            >
            <!--  -->
            <el-button class="btn-add" type="primary" icon="el-icon-plus" circle></el-button>
            <div slot="tip" class="el-upload__tip" style="text-align:center;">只能上传mp3文件</div>
          </el-upload>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false,fileList=[]">取 消</el-button>
              <el-button type="primary" @click="saveMp3Click">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import manageprive from "../../components/managePrive";
export default {
  components: {
    manageprive
  },
  data() {
    const UPLOAD_EDITOR_IMAGE = this.UPLOAD_EDITOR_IMAGE;
    return {
      previewDialogVisible: false,
      dialogVisible: false,
      previewUrl: "",
      limit: 1,
      checkBox:false,
      fileList: [],
      musicUrl: "",
      breadArr: [
        { path: "/posterList", name: "电子期刊" },
        { path: "/articleList", name: this.$route.query.name }
      ],
      journaName: this.$route.query.name,
      journaOrder: this.$route.query.order,
      journaId: this.$route.query.id,
      // 文档类型为外链的时候的url变量
      outerLineUrl: "",
      textModel: "",
      //   文本编译器
      config: {
        placeholder: "",
        language: "zh_cn",
        heightMin: 200,
        widthMin: 800,
        height: 320,
        width: "100%",
        direction: "ltr",
        toolbarButtons: [
          "html",
          "fullscreen",
          "|",
          "insertLink",
          "insertImage",
          "insertVideo",
          "alert",
          "insertTable",
          "|",
          "quote",
          "insertHR",
          "subscript",
          "superscript",
          "undo",
          "redo",
          "|",
          "bold",
          "italic",
          "underline",
          "strikeThrough",
          "|",
          "fontFamily",
          "|",
          "fontSize",
          "|",
          "color",
          "emoticons",
          "inlineStyle",
          "|",
          "paragraphFormat",
          "|",
          "paragraphStyle",
          "align",
          "formatOL",
          "formatUL",
          "outdent",
          "indent",
          "clearFormatting",
          "insertimg",
          "lineHeight",
          "|",
          "insertHTML",
          "html"
        ],
        allowedImageTypes: ["jpeg", "jpg", "png", "gif"],
        imageAllowedTypes: ["jpeg", "jpg", "png", "gif"],
        imageUploadURL: UPLOAD_EDITOR_IMAGE,
        videoUploadURL: UPLOAD_EDITOR_IMAGE,
        events: {
          "froalaEditor.initialized": (e, editor) => {
            this.$editor = editor;
          }
          // "froalaEditor.DefineIcon": ("alert", { NAME: "info" }),
          // "froalaEditor.RegisterCommand": ("alert",
          // {
          //   title: "sfs",
          //   focus: false,
          //   undo: false,
          //   refreshAfterCallback: false,
          //   callback: function() {
          //     alert("Hello!");
          //   }
          // })
        }
      },
      model: null,
      ElectronicJournalInfo: {
        articlename: "",
        articletype: null,
        content: "",
        createtime: "",
        deleted: null,
        id: null,
        imgurl: "",
        journalid: null,
        likes: null,
        updateuser: null,
        ordernum: null,
        updatetime: null,
        topic: null,
        showheadimg:null,
        showbarrage:null,
      },
      rules: {
        articlename: [
          { required: true, message: "请输入文章名称", trigger: "blur" }
        ],
        topic: [{ required: true, message: "请输入栏目名称", trigger: "blur" }],
        articletype: [
          { required: true, message: "请选择活动区域", trigger: "change" }
        ],
        content: [
          { required: true, message: "请输入外链地址", trigger: "blur" }
        ],
        imgurl: [{ required: true, message: "请选择封面", trigger: "change" }]
      },
      articleListData: [],
      entryOptions: [
        {
          label: "",
          options: [
            {
              value: "0",
              label: "文章"
            },
            {
              value: "1",
              label: "外部链接"
            }
          ]
        }
      ],
      post_data:[],
    };
  },
  methods: {
    // 富文本编译器插入图标方法
    addButtonIcon() {
      $.FroalaEditor.DefineIcon("alert", { NAME: "info" });
      $.FroalaEditor.RegisterCommand("alert", {
        title: "插入声音",
        focus: false,
        undo: false,
        refreshAfterCallback: false,
        callback: () => {
          this.dialogVisible = true;
        }
      });

      $("div#froala-editor").froalaEditor({
        toolbarButtons: ["alert", "html"]
      });
    },
    // 语音上传成功
    singleUpLoadSuccess(res, file) {
      if (res.Code == 200) {
        this.fileList = [];
        let obj = {};
        obj.name = file.name;
        obj.url = res.Data.link;
        this.musicUrl = res.Data.link;
        this.fileList.push(obj);
      }
    },
    // 保存音频文件的点击事件
    saveMp3Click() {
      // console.log(this.$editor);
      // console.log(this.$editor.html);
      if (!this.musicUrl) {
        this.$showErrorTip("请先上传文件");
        return false;
      }
      let newHtml = `<p><div class='audio_box' style='margin-top: 10px;height: 60px;width: 100%;display:block;' ref='audioBox'><div class='audio_Dom' style='height: 100%;background-color: #eee;border-radius:5px;position: relative;'><div class='left' style='width: 60px; float: left;background: url(https://img.guoanfamily.com/Zqs6cGiwVN0MqSP5rq8LmcGcKRW9M_LqF072IVcIgE_hqJvABYyrYogfTzt1ZUV9.jpg) center no-repeat;background-size: 60%;height: 100%;'></div><div class='right' style='padding-left: 10px;'><div class='titles' style='height:100%;line-height:60px;font-size:18px;'></div><div class='timers' style='position: absolute;bottom: 6px;right: 6px;'></div></div><audio class='audio' style='display:none' src='${
        this.musicUrl
      }'><source src=src='${
        this.musicUrl
      }' type='audio/mpeg'/></audio></div></div></p>`;
      this.$editor.html.insert(newHtml);
      let audioFlag = false;
      // 音乐播放与暂停的点击事件
      $(".left").on("click", function(event) {
        event.stopPropagation();
        event.preventDefault();
        if (!audioFlag) {
          var audio = $(".audio")[0];
          audio.play();
          audioFlag = true;
          $(".left").css({
            background:
              "url(https://img.guoanfamily.com/9kUQhufca4ghQmVqUtt9cXYH6rda7tfJqCuAQFkQ31hZwSMGSwSLxCeiSl1AwhA9.jpg) no-repeat center",
            "background-size": "60%"
          });
        } else {
          var audio = $(".audio")[0];
          audio.pause();
          audioFlag = false;
          $(".left").css({
            background:
              "url(https://img.guoanfamily.com/Zqs6cGiwVN0MqSP5rq8LmcGcKRW9M_LqF072IVcIgE_hqJvABYyrYogfTzt1ZUV9.jpg)no-repeat center",
            "background-size": "60%"
          });
        }
        // alert('1234');
      });
      // 音乐的时长
      var musicTime = null;

      $(".audio")[0].addEventListener(
        "canplaythrough",
        () => {
          musicTime = Math.round($(".audio")[0].duration * 100) / 100 + "s";
          // console.log('1234',musicTime);
          $(".timers").html(musicTime);
        },
        false
      );
      $(".audio")[0].load();
      this.dialogVisible = false;
    },

    // 数据集加载
    articleListLoad() {
      this.$get(`GetEjArticlelistWhere?cond=journalid&arg=${this.journaId}`)
        .then(res => {
          if (res.Code == 200) {
            if (res.Data.length > 0) {
              this.articleListData = res.Data;
              this.ElectronicJournalInfo.articlename = this.articleListData[0].articlename;
              this.ElectronicJournalInfo.articletype = this.articleListData[0].articletype;
              this.ElectronicJournalInfo.content = this.articleListData[0].content;
              this.ElectronicJournalInfo.createtime = this.articleListData[0].createtime;
              this.ElectronicJournalInfo.deleted = this.articleListData[0].deleted;
              this.ElectronicJournalInfo.id = this.articleListData[0].id;
              this.ElectronicJournalInfo.imgurl = this.articleListData[0].imgurl;
              this.ElectronicJournalInfo.journalid = this.articleListData[0].journalid;
              this.ElectronicJournalInfo.likes = this.articleListData[0].likes;
              this.ElectronicJournalInfo.updateuser = this.articleListData[0].updateuser;
              this.ElectronicJournalInfo.ordernum = this.articleListData[0].ordernum;
              this.ElectronicJournalInfo.updatetime = this.articleListData[0].updatetime;
              this.ElectronicJournalInfo.topic = this.articleListData[0].topic;
              this.ElectronicJournalInfo.showbarrage = this.articleListData[0].showbarrage;
              if (this.articleListData[0].articletype == 0) {
                this.ElectronicJournalInfo.articletype = "文章";
              } else {
                this.ElectronicJournalInfo.articletype = "外部链接";
              }
              if (this.articleListData[0].showheadimg ==0){
                this.checkBox = false;
              }else{
                this.checkBox = true;
              }
              this.articleListClick(this.articleListData[0]);
            }
          }
        })
        .then(() => {
          // 拖拽中
          this.$dragging.$on("dragged", ({ value }) => {
            this.post_data = [];
            // console.log('1234',value.list);
            for (var i = 0; i < this.articleListData.length; i++) {
              let obj = {};
              obj.id = this.articleListData[i].id;
              obj.ordernum = i;
              this.post_data.push(obj);
            }
            // console.log('12342343243242',value);
            // console.log('123412',JSON.stringify(this.post_data));
          });
        }).then(()=>{
          // 拖拽结束
          this.$dragging.$on("dragend", ({ }) => {
            // console.log('截取数组',JSON.stringify(this.post_data));
            this.$post(`EjArticleSort?jid=${this.journaId}`, this.post_data.splice(-this.articleListData.length)).then(
              res => {
                if (res.Code == 200) {
                  this.articleListData = res.Data;
                  this.ElectronicJournalInfo.articlename = this.articleListData[0].articlename;
                  this.ElectronicJournalInfo.articletype = this.articleListData[0].articletype;
                  this.ElectronicJournalInfo.content = this.articleListData[0].content;
                  this.ElectronicJournalInfo.createtime = this.articleListData[0].createtime;
                  this.ElectronicJournalInfo.deleted = this.articleListData[0].deleted;
                  this.ElectronicJournalInfo.id = this.articleListData[0].id;
                  this.ElectronicJournalInfo.imgurl = this.articleListData[0].imgurl;
                  this.ElectronicJournalInfo.journalid = this.articleListData[0].journalid;
                  this.ElectronicJournalInfo.likes = this.articleListData[0].likes;
                  this.ElectronicJournalInfo.updateuser = this.articleListData[0].updateuser;
                  this.ElectronicJournalInfo.ordernum = this.articleListData[0].ordernum;
                  this.ElectronicJournalInfo.updatetime = this.articleListData[0].updatetime;
                  this.ElectronicJournalInfo.topic = this.articleListData[0].topic;
                  this.ElectronicJournalInfo.showbarrage = this.articleListData[0].showbarrage;
                  if (this.articleListData[0].articletype == 0) {
                    this.ElectronicJournalInfo.articletype = "文章";
                  } else {
                    this.ElectronicJournalInfo.articletype = "外部链接";
                  }
                  if (this.articleListData[0].showheadimg ==0){
                    this.checkBox = false;
                  }else{
                    this.checkBox = true;
                  }
                  this.articleListClick(this.articleListData[0]);
                }
              }
            );
          });
        });
    },
    // 图片控制
    handleAvatarSuccess(res, file) {
      if (res.Code == 200) {
        this.ElectronicJournalInfo.imgurl = res.Data.link;
      }
    },
    // 文章类型选择
    changEntryType1(val) {
      // console.log(val);
      if (val == 0) {
        this.ElectronicJournalInfo.articletype == "文章";
      } else {
        this.ElectronicJournalInfo.articletype == "外部链接";
      }
    },
    // 文章列表的点击事件
    articleListClick(item) {
      this.$get(`GetEjArticlelistFirst?cond=id&arg=${item.id}`)
        .then(res => {
          if (res.Code == 200) {
            this.previewUrl ="posterWarp/#/journal?journalid=" +this.journaId +"&isPublish=0" + "&listId=" + item.id ;
            this.ElectronicJournalInfo.articlename = res.Data.articlename;
            this.ElectronicJournalInfo.topic = res.Data.topic;
            if (res.Data.articletype == 0) {
              this.ElectronicJournalInfo.articletype = "文章";
              this.textModel = res.Data.content;
              this.outerLineUrl = "";
            } else {
              this.ElectronicJournalInfo.articletype = "外部链接";
              this.outerLineUrl = res.Data.content;
              this.textModel = "";
            }
            if (res.Data.showheadimg == 1){
                this.checkBox = true;
              }else{
                this.checkBox = false;
              }
            this.ElectronicJournalInfo.createtime = res.Data.createtime;
            this.ElectronicJournalInfo.deleted = res.Data.deleted;
            this.ElectronicJournalInfo.id = res.Data.id;
            this.ElectronicJournalInfo.imgurl = res.Data.imgurl;
            this.ElectronicJournalInfo.journalid = res.Data.journalid;
            this.ElectronicJournalInfo.likes = res.Data.likes;
            this.ElectronicJournalInfo.updateuser = res.Data.updateuser;
            this.ElectronicJournalInfo.ordernum = res.Data.ordernum;
            this.ElectronicJournalInfo.updatetime = res.Data.updatetime;
            this.ElectronicJournalInfo.showheadimg = this.checkBox;
            this.ElectronicJournalInfo.showbarrage = res.Data.showbarrage;
          }
        })
        .then(() => {
          let isAudio = $(".audio")[0];
          // console.log("123432", isAudio);
          if (isAudio) {
            $(".left").css({
              background:
                "url(https://img.guoanfamily.com/Zqs6cGiwVN0MqSP5rq8LmcGcKRW9M_LqF072IVcIgE_hqJvABYyrYogfTzt1ZUV9.jpg)no-repeat center",
              "background-size": "60%"
            });
            let audioFlag = false;
            // 音乐播放与暂停的点击事件
            $(".left").on("click", function(event) {
              event.stopPropagation();
              event.preventDefault();
              if (!audioFlag) {
                var audio = $(".audio")[0];
                audio.play();
                audioFlag = true;
                $(".left").css({
                  background:
                    "url(https://img.guoanfamily.com/9kUQhufca4ghQmVqUtt9cXYH6rda7tfJqCuAQFkQ31hZwSMGSwSLxCeiSl1AwhA9.jpg) no-repeat center",
                  "background-size": "60%"
                });
              } else {
                var audio = $(".audio")[0];
                audio.pause();
                audioFlag = false;
                $(".left").css({
                  background:
                    "url(https://img.guoanfamily.com/Zqs6cGiwVN0MqSP5rq8LmcGcKRW9M_LqF072IVcIgE_hqJvABYyrYogfTzt1ZUV9.jpg)no-repeat center",
                  "background-size": "60%"
                });
              }
              // alert('1234');
            });
          }
        });
    },
    // 新增的点击事件
    addArticleClick() {
      this.ElectronicJournalInfo.articlename = "";
      this.ElectronicJournalInfo.articletype = null;
      this.ElectronicJournalInfo.content = "";
      this.ElectronicJournalInfo.createtime = "";
      this.ElectronicJournalInfo.deleted = 0;
      this.ElectronicJournalInfo.id = 0;
      this.ElectronicJournalInfo.imgurl = "";
      this.ElectronicJournalInfo.journalid = 0;
      this.ElectronicJournalInfo.likes = 0;
      this.ElectronicJournalInfo.updateuser = 0;
      this.ElectronicJournalInfo.ordernum = 0;
      this.ElectronicJournalInfo.updatetime = 0;
      this.ElectronicJournalInfo.topic = null;
      this.ElectronicJournalInfo.showheadimg = null;
      this.ElectronicJournalInfo.showbarrage = null;
      this.outerLineUrl = "";
      this.textModel = "";
    },
    // 保存的点击事件
    saveClick() {
      if (this.ElectronicJournalInfo.articletype == "文章") {
        this.ElectronicJournalInfo.articletype = 0;
      } else if (this.ElectronicJournalInfo.articletype == "外部链接") {
        this.ElectronicJournalInfo.articletype = 1;
      }
      if (this.ElectronicJournalInfo.articletype == 1) {
        this.ElectronicJournalInfo.content = this.outerLineUrl;
      } else {
        this.ElectronicJournalInfo.content = this.textModel;
      }
      if(this.checkBox){
        this.ElectronicJournalInfo.showheadimg = 1;
      }else{
        this.ElectronicJournalInfo.showheadimg = 0;
      }
      let post_data = {
        journalid: parseInt(this.journaId),
        articlename: this.ElectronicJournalInfo.articlename,
        imgurl: this.ElectronicJournalInfo.imgurl,
        ordernum: this.ElectronicJournalInfo.ordernum,
        content: this.ElectronicJournalInfo.content,
        articletype: parseInt(this.ElectronicJournalInfo.articletype),
        likes: this.ElectronicJournalInfo.likes,
        createtime: this.ElectronicJournalInfo.createtime,
        updatetime: this.ElectronicJournalInfo.updatetime,
        updateuser: parseInt(sessionStorage.getItem("USER_ID")),
        deleted: this.ElectronicJournalInfo.deleted,
        id: this.ElectronicJournalInfo.id,
        topic: this.ElectronicJournalInfo.topic,
        showheadimg:this.ElectronicJournalInfo.showheadimg,
        showbarrage:this.ElectronicJournalInfo.showbarrage,
      };
      if (!post_data.articlename) {
        this.$showErrorTip("请输入文章名称");
        return false;
      }
      if (
        post_data.articletype == null &&
        post_data.articletype == undefined &&
        post_data.articletype == ""
      ) {
        this.$showErrorTip("请选择文章类型");
        return false;
      }

      if (!post_data.imgurl) {
        this.$showErrorTip("请选择图片");
        return false;
      }

      if (post_data.articletype == 1) {
        if (!post_data.content) {
          this.$showErrorTip("请输入链接地址");
          return false;
        }
      }

      this.$post(`EjArticlelistSave`, post_data).then(res => {
        if (res.Code == 200) {
          // this.ElectronicJournalInfo.articletype = null;
          if (
            this.ElectronicJournalInfo.id == 0 ||
            this.ElectronicJournalInfo.id == null
          ) {
            this.$showMsgTip("保存成功");
            this.textModel = "";
            this.articleListLoad();
          } else {
            this.$showMsgTip("修改成功");
            this.textModel = "";
            this.articleListClick(this.ElectronicJournalInfo);
          }
        }
      });
    },
    // 返回的点击事件
    beforeClick() {
      // history.go(-1);
      this.$router.push({ path: "/posterList" });
    },
    // 删除按钮的点击事件
    articleListDelete(item) {
      this.$showConfirm("是否确定删除此章节", () => {
        this.deleteFn(item);
      });
    },
    deleteFn(item) {
      this.$post(`EjArticlelistDelete`, item).then(res => {
        if (res.Code == 200) {
          this.$showMsgTip("删除成功");
          this.textModel = "";
          this.articleListLoad();
        }
      });
    }
  },
  created() {
    this.addButtonIcon();
    this.previewUrl ="posterWarp/#/journal?journalid=" +this.journaId +"&isPublish=0";
  },
  mounted() {
    this.articleListLoad();
  }
};
</script>
<style>
a[href="https://www.froala.com/wysiwyg-editor?k=u"] {
  display: none !important;
}
</style>

<style lang='less' scoped>
.articlePage {
  width: 100%;
  height: 100%;
  overflow-y: hidden;
  position: relative;
}
.fileInfo {
  width: 100%;
  height: 0.9rem;
  // background: red;
  margin-top: 0.1rem;
  line-height: 0.25rem;
  border-bottom: 1px solid #ccc;
  .fromLine {
    width: 100%;
    height: 0.3rem;
    float: left;
  }
  .avatar {
    display: inline-block;
    height: 0.2rem;
  }
  .smallImgDiv:hover {
    .enlarge {
      display: block;
    }
  }
  .enlarge {
    width: 1rem;
    top: -0.2rem;
    right: -1.1rem;
    position: absolute;
    z-index: 2;
    display: none;
  }
}
.outerLayer {
  width: 100%;
  position: absolute;
  z-index: 1;
  bottom: 0.2rem;
  left: 0;
  top: 0.2rem;
  border: 1px solid #ccc;
  .leftList {
    width: 20%;
    height: 100%;
    float: left;
    border-right: 1px solid #ccc;
    text-align: center;
    overflow-y: scroll;
    .article_List {
      width: 94%;
      margin-left: 3%;
      height: 0.25rem;
      border-bottom: 1px solid #ccc;
      div:nth-child(1) {
        width: 25%;
        height: 100%;
        float: left;
      }
      div:nth-child(2) {
        width: 50%;
        height: 100%;
        float: left;
        line-height: 0.25rem;
        font-size: 0.08rem;
        color: blue;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
      }
      div:nth-child(3) {
        display: none;
        width: 15%;
        height: 100%;
        float: right;
        line-height: 0.25rem;
      }
    }
    .article_List:hover {
      cursor: pointer;
      div:nth-child(3) {
        display: block;
      }
    }
  }
  .rightText {
    width: 75%;
    height: 100%;
    float: right;
    overflow-y: scroll;
    border-left: 1px solid #ccc;
    position: relative;
    .topDiv {
      padding-left: 0.2rem;
      width: 100%;
      height: 1rem;
      position: absolute;
      left: 0;
      top: 0;
      z-index: 2;
    }
    .textDiv {
      width: 100%;
      height: auto;
      position: absolute;
      left: 0;
      top: 1.1rem;
      z-index: 1;
    }
  }
}
.footer {
  /* width: 100%; */
  text-align: center;
  position: fixed;
  bottom: 10px;
  left: 4rem;
}
</style>
<style lang = "less">
.bread .el-breadcrumb {
  font-size: 0.07rem;
  height: 0.1rem;
  line-height: 0.1rem;
}

.rightText {
  .fr-box {
    button:nth-child(7) {
      /* background:red !important; */
      background: url("../../../static/voic.png") no-repeat center !important;
      background-size: 50% !important;
      i {
        display: none;
      }
    }
  }
}
.left {
  cursor: pointer;
}
</style>

