<style scoped>
.page {
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  padding-left: 20px;
  max-width: 0.3rem;
  width: 100%;
  min-width: 30%;
  text-align: center;
}

.content {
  flex-grow: 1;
  overflow: auto;
}

.box-card {
  margin: 10px;
}

.carousel-item {
  position: relative;
  /* padding-right: 100px; */
}

.carousel-bottom {
  margin: 40px 0;
}

.avatar {
  display: inline-block;
  height: 0.3rem;
}

.btn-remove {
  position: absolute;
  top: 30%;
  right: 0;
  color: #fff;
  transform: translate(0%, -50%);
}

.el-select,
.el-date-editor {
  width: 100%;
}
.el-tag + .el-tag {
  margin-left: 10px;
}
.button-new-tag {
  margin-left: 10px;
  height:0.16rem;
  line-height: 30px;
  padding-top: 0;
  padding-bottom: 0;
}
.bg-purple {
  background: #ffffff;
}
.mapDiv {
  margin-top: 20px;
  width: 95%;
  margin: 0 auto;
  height: 0.17rem;
  background: red;
}

</style>
<style>
  .Attribute .el-alert__icon{
    width: 0.08rem;
    font-size:0.08rem;
  }
  .Attribute .el-alert__title {
    line-height: 0.09rem;
    font-size:0.07rem;
  }
  .Attribute .el-form-item__label{
     font-size:0.07rem;
     line-height: 0.2rem;
     width:0.4rem !important;
  }
  .Attribute .el-form-item__content{

     margin-left:0.4rem !important;
  }
  .Attribute .avatar-uploader{
    min-height: 0.2rem;
    width: 70%;
    text-align:center;
    line-height: 0.2rem;
  }
</style>

<template>
    <div class="page Attribute">
        <h4>属性区</h4>

        <div class="content">
            <div v-if="!formType">
                <el-card class="box-card">
                    <el-alert
                        title="请选择编辑区域..."
                        type="info"
                        show-icon
                        :closable="false"
                    >
                    </el-alert>
                </el-card>
            </div>

            <!--轮播图属性-->
            <div v-if="formType === 'carousel'">
                <el-card class="box-card" v-for="(item, index) in carouselArray" :key="index">
                    <el-form label-width="80px" class="carousel-item">
                        <el-form-item label="图片">
                            <el-upload
                                class="avatar-uploader"
                                :action="UPLOAD_IMAGE"
                                :data="{pid: mid}"
                                :show-file-list="false"
                                :on-success="(...arr)=>{
                                    handleAvatarSuccess(index, ...arr);
                                }"
                            >
                                <img v-if="item.imageUrl" :src="item.imageUrl" class="avatar">
                                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                            </el-upload>
                        </el-form-item>

                        <el-form-item label="跳转地址">
                            <el-input v-model="carouselArray[index].linkUrl" placeholder="请输入跳转链接"></el-input>
                        </el-form-item>

                        <el-button class="btn-remove" type="danger" icon="el-icon-delete" circle
                                   @click="removeCarouselItem(index)"></el-button>
                    </el-form>
                </el-card>

                <div class="carousel-bottom">
                    <el-button class="btn-add" type="primary" icon="el-icon-plus" circle
                               @click="addCarouselItem"></el-button>
                </div>
            </div>

            <!--入口属性-->
            <div v-if="formType === 'entry'">
                <el-card class="box-card">
                    <el-form label-width="80px">
                        <el-form-item label="图片">
                            <el-upload
                                class="avatar-uploader"
                                :action="UPLOAD_IMAGE"
                                :data="{pid: mid}"
                                :show-file-list="false"
                                :on-success="handleEntrySuccess"
                            >
                                <img v-if="entryItem.icon" :src="entryItem.icon" class="avatar">
                                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                            </el-upload>
                            <el-button class="btn-remove" type="danger" icon="el-icon-delete" circle
                                   @click="removeLatticeImg()"></el-button>
                        </el-form-item>

                        <el-form-item label="标签名称">
                            <el-input v-model="entryItem.label" placeholder="请输入名称"></el-input>
                        </el-form-item>

                        <el-form-item label="类型">
                            <el-select v-model="entryItem.type" placeholder="请选择" @change="changEntryType">
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

                        <!--根据不同链接类型展示不同表单数据-->
                        <!--内部文章-->
                        <template v-if="entryItem.type === 'article'">
                            <el-form-item label="内容">
                                <el-select v-model="entryItem.articleId" placeholder="请选择">
                                    <el-option
                                        v-for="item in articleList"
                                        :key="item.id"
                                        :label="item.title"
                                        :value="item.id">
                                    </el-option>
                                </el-select>
                            </el-form-item>
                        </template>

                        <!--外部链接-->
                        <template v-if="entryItem.type === 'link'">
                            <el-form-item label="链接地址">
                                <el-input v-model="entryItem.href" placeholder="请输入链接"></el-input>
                            </el-form-item>
                        </template>

                        <!--特殊类型-->
                        <!--地图-->
                        <template v-if="entryItem.type === 'map'">
                            <el-form-item label="目标地点">
                                <el-input v-model="entryItem.location" placeholder="请输入地址">
                                    <!-- <el-button type="button" slot="append" @click="showDialog">编辑</el-button> -->
                                </el-input>
                            </el-form-item>
                        </template>

                        <!--天气预报-->
                        <template v-if="entryItem.type === 'weather'">
                            <el-form-item label="城市">
                                <el-input v-model="entryItem.city" placeholder="请输入地点"></el-input>
                            </el-form-item>

                            <el-form-item label="日期">
                                <el-date-picker
                                    v-model="entryItem.date"
                                    type="date"
                                    placeholder="选择日期"
                                    format="yyyy 年 MM 月 dd 日"
                                >
                                </el-date-picker>
                            </el-form-item>
                        </template>

                        <!--文件列表-->
                        <template v-if="entryItem.type === 'file'">
                            <el-form-item label="文件列表" key="file">
                                <el-upload
                                    class="upload-demo"
                                    :action="UPLOAD_FILE"
                                    :data="{pid: mid}"
                                    :on-success="fileUploadSuccess"
                                    :on-remove="removeFile"
                                    multiple
                                    :file-list="entryItem.fileList">
                                    <el-button size="small" type="primary">点击上传</el-button>
                                </el-upload>
                            </el-form-item>
                        </template>

                        <!--人员行程-->
                        <template v-if="entryItem.type === 'trip'">
                            <el-form-item label="文件列表" key="trip">
                                <el-upload
                                    class="upload-demo"
                                    :action="UPLOAD_TRIP"
                                    :data="{pid: mid}"
                                    :on-success="scheduleUploadSuccess"
                                    :on-remove="removeSchedule"
                                    :limit="1"
                                    :file-list="entryItem.scheduleFile">
                                    <el-button size="small" type="primary" v-if="entryItem.scheduleFile.length === 0">点击上传</el-button>
                                </el-upload>
                            </el-form-item>

                            <el-form-item label="">
                                <el-button type="primary" size="small" round :disabled="entryItem.scheduleFile.length === 0" @click="checkDetail">查看列表</el-button>
                            </el-form-item>
                        </template>

                        <!--会议签到-->
                        <template v-if="entryItem.type === 'sign'">
                            <el-button size="small" type="primary" @click="checkSign">签到统计</el-button>
                        </template>

                        <!--座位表-->
                        <template v-if="entryItem.type === 'seat'">
                            <el-form-item label="文件列表" key="file">
                                <el-upload
                                    class="upload-demo"
                                    :action="UPLOAD_FILE"
                                    :data="{pid: mid}"
                                    :on-success="seatUploadSuccess"
                                    :on-remove="removeSeat"
                                    :file-list="entryItem.fileList">
                                    <el-button size="small" type="primary" v-if="entryItem.fileList.length === 0">点击上传</el-button>
                                </el-upload>
                            </el-form-item>
                        </template>

                    </el-form>
                </el-card>
            </div>
        </div>
        <!-- 弹窗 -->
        <el-dialog
            title="会议地点编辑"
            :visible.sync="dialogVisible"
            :before-close="hideDialog"
            :close-on-click-modal= "false"
            width="60%"
           >
           <!--地点和搜索 -->
            <el-row>
                <el-col :span="12">
                    <div class="grid-content bg-purple">
                       <el-form ref="form" :model="address" label-width="80px">
                            <el-form-item label="城市">
                                <el-input v-model="address.city" placeholder="请输入地点"></el-input>

                            </el-form-item>
                        </el-form>
                    </div>
                </el-col>
                <el-col :span="12">
                    <div class="grid-content bg-purple-light">
                        <el-form :model="address" ref="form" label-width="80px">
                            <el-form-item label="搜索地点">
                                <el-input v-model="address.detialAddress" placeholder="请输入搜索地点">
                                     <el-button @click="elButtonClick()" slot="append" icon="el-icon-search"></el-button>
                                </el-input>
                            </el-form-item>
                        </el-form>
                    </div>
                </el-col>
            </el-row>
            <!-- 标签tab地址 -->
            <el-tag
                :key="tag"
                v-for="tag in dynamicTags"
                closable
                :disable-transitions="false"
                @close="handleClose(tag)">
                {{tag}}
            </el-tag>
            <baidu-map :zoom="15" :scroll-wheel-zoom="true" >
                <bm-view class="mapDiv"></bm-view>
                <bm-local-search :keyword="keyword"
                :auto-viewport="true"
                :location="location"
                :panel="false"
                :pageCapacity=1
                ></bm-local-search>
            </baidu-map>
            <span slot="footer" class="dialog-footer">
                <el-button @click="hideDialog">取 消</el-button>
                <el-button type="primary" @click="dialogDeterClick">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
export default {
  data() {
    return {
      address: {
        city: "",
        detialAddress: ""
      },
      dynamicTags: [],
      //   地图的关键字
      keyword: [],
      location: "北京",
      dialogVisible: false,
      mid: this.$route.query.mid,

      entryOptions: [
        {
          label: "基础类型",
          options: [
            {
              value: "article",
              label: "内部文章"
            },
            {
              value: "link",
              label: "外部链接"
            }
          ]
        },
        {
          label: "特殊类型",
          options: [
            {
              value: "map",
              label: "地图"
            },
            {
              value: "weather",
              label: "天气预报"
            },
            {
              value: "file",
              label: "文件"
            },
            {
              value: "trip",
              label: "行程"
            },
            {
              value: "sign",
              label: "签到"
            },
            {
              value: "seat",
              label: "座位表"
            }
          ]
        }
      ]
    };
  },

  created() {},

  mounted() {},

  methods: {
    //   搜索文本框按钮的点击事件
    elButtonClick() {
      this.dynamicTags.push(this.address.city + this.address.detialAddress);
      this.keyword.push(this.address.city + this.address.detialAddress);
    },
    //  弹窗头部tag方法
    handleClose(tag) {
      this.dynamicTags.splice(this.dynamicTags.indexOf(tag), 1);
      this.keyword.splice(this.dynamicTags.indexOf(tag), 1);
    },

    // 显示弹窗的方法
    showDialog() {
      this.dialogVisible = true;
      var addressArr = this.entryItem.location.split(",");
      this.dynamicTags = this.dynamicTags.concat(addressArr);
      this.keyword = this.keyword.concat(this.dynamicTags);
    },
    // 弹窗确定的点击事件
    dialogDeterClick(){
      this.dialogVisible = false;
      this.entryItem.location =  this.dynamicTags.join(',');
      this.keyword = [];
      this.dynamicTags = [];
      this.address.city = '';
      this.address.detialAddress = '';
    },
    // 隐藏弹窗(确定的点击事件)
    hideDetermineDialog() {
      this.dialogVisible = false;
      this.entryItem.location = this.dynamicTags.toString();
      this.keyword = [];
      this.dynamicTags = [];
      this.address.city = '';
      this.address.detialAddress = '';
    },
    // 隐藏弹窗(取消的点击事件)
    hideDialog() {
      this.dialogVisible = false;
      this.dynamicTags = [];
      this.keyword = [];
      this.address.city = '';
      this.address.detialAddress = '';
    },
    /*轮播图控制*/
    addCarouselItem() {
      this.carouselArray.push({
        imageUrl: "",
        linkUrl: ""
      });
    },
    // 移除轮播图的图片
    removeCarouselItem(index) {
      this.carouselArray.splice(index, 1);
      this.$emit("refreshLis");
    },

    handleAvatarSuccess(index, res, file) {
      this.carouselArray[index].imageUrl = res.Data.link;
      this.$emit("refreshLis");
    },
    /*轮播图控制结束*/

    /*选择入口链接类型*/
    changEntryType(value) {},

    /*九宫格控制*/
    handleEntrySuccess(res, file) {
      this.entryItem.icon = res.Data.link;
    },
    // 移除九宫格的图片
    removeLatticeImg() {
      // this.$Bus.$emit('removeImg','删除图片')
      // console.log(this.entryItem.icon)
      this.entryItem.icon = "";
      // 删除成功，自动调用父组件保存方法
      this.$emit("refreshLis");
    },
    /*九宫格控制结束*/

    /*文件列表控制*/
    fileUploadSuccess(res, file, fileList) {
      const { Data: { Filename, Filepath, Id } } = res;
      const fileName = Filename;
      this.entryItem.fileList = fileList.map(item => {
        if (fileName.includes(item.name)) {
          item.name = fileName;
          item.url = Filepath;
          item.id = Id;
        }
        return item;
      });
      this.$emit("refreshLis");
    },

    removeFile(file, fileList) {
      this.entryItem.fileList = this.entryItem.fileList.filter(item => {
        return !item.name.includes(file.name);
      });
      this.$emit("refreshLis");
    },
    /*文件列表控制结束*/

    /*日程文件控制*/
    scheduleUploadSuccess(res, file, fileList) {
      // const {Data: {Filename, Filepath, Id}} = res;

      if (!this.entryItem.scheduleFile) {
        this.entryItem.scheduleFile = [];
      }

      this.entryItem.scheduleFile = [
        {
          name: file.name
          /*  url: Filepath,
                    id: Id,*/
        }
      ];
    },

    removeSchedule(file, fileList) {
      this.$get(`HbfileDel?id=${this.entryItem.scheduleFile[0].id}`).then(
        res => {
          if (res.Code === 200) {
            this.$showMsgTip(`删除成功`);
            this.entryItem.scheduleFile = [];
          } else {
            this.$showErrorTip(`删除失败`);
          }
        }
      );
    },
    /*日程文件控制结束*/

    seatUploadSuccess(res, file, fileList) {
      const { Data: { Filename, Filepath, Id } } = res;

      if (!this.entryItem.fileList) {
        this.entryItem.fileList = [];
      }

      this.entryItem.fileList = [
        {
          name: file.name,
          url: Filepath,
          id: Id
        }
      ];
    },

    removeSeat(file, fileList) {
      this.$get(`HbfileDel?id=${this.entryItem.fileList[0].id}`).then(res => {
        if (res.Code === 200) {
          this.$showMsgTip(`删除成功`);
          this.entryItem.fileList = [];
        } else {
          this.$showErrorTip(`删除失败`);
        }
      });
    },

    checkDetail() {
      this.$showConfirm("确定已保存数据，以免数据丢失", () => {
        this.$router.push(`/main/trip/edit?mid=${this.mid}`);
      });
    },

    checkSign() {
      this.$showConfirm("确定已保存数据，以免数据丢失", () => {
        this.$router.push(`/main/signDashboard?mid=${this.mid}`);
      });
    }
  },

  components: {},

  computed: {
    formType() {
      return this.$store.state.attribute.objectType;
    },

    carouselArray() {
      return this.$store.state.attribute.carouselArray;
    },

    entryItem() {
      return this.$store.state.attribute.entryArray[
        this.$store.state.attribute.entryIndex
      ];
    },

    articleList() {
      return this.$store.state.attribute.articleList;
    }
  }
};
</script>
