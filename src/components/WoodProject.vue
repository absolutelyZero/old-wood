<template>
  <div class="project">
    <div class="child_head">
      <div class="block"></div>
      <div class="child_title">{{title}}</div>
    </div>
    <div class="woodproject">
      <div class="top">
        <div class="suscribetitle">
          <img src="../assets/dy1.png" alt>
          <div class="suspro">已上架项目</div>
        </div>
        <el-card
          :body-style="{ padding: '0px' }"
          class="card"
          v-for="(item) of data"
          :key="item.id"
        >
          <img src="../assets/example.png" class="image">
          <div class="detail">
            <span style="font-size:18px;weight:bold;color:#2B4A7E">{{item.title}}</span>
            <div class="time">
              <img src="../assets/shijian1@2x.png" alt>
              <span style="font-size:12px;font-weight:bold;color:#666666">{{item.timeLength}}</span>
              <div class="love">
                <div :class="item" v-for="item in item.score" :key="item.id"></div>
              </div>
            </div>
            <div class="button">
              <el-button type="text" @click="manage(item.title,item.courseId)">管理</el-button>
              <el-button type="text" @click="view(item.courseId)">预览</el-button>
              <el-button type="text" @click="material(item.courseId)">材料</el-button>
              <el-button type="text" @click="xiajia(item.courseId)">下架</el-button>
            </div>
          </div>
        </el-card>
        <!-- //播放器弹框 -->
        <div class="vediodialog">
          <el-dialog
            :visible.sync="dialogVisible"
            width="60%"
            custom-class="dialog"
            :close-on-click-modal="false"
            top="7vh"
            :beforeClose="playerclose"
          >
            <Video ref="player" :url="vedioUrl" v-if="palyerReset"></Video>
          </el-dialog>
        </div>
        <!-- 视频列表弹框 -->
        <div class="moviedialog">
          <el-dialog
            :visible.sync="moviedialogVisible"
            width="80%"
            custom-class="dialog"
            :close-on-click-modal="false"
            top="7vh"
            title="视频列表"
            :modal-append-to-body="false"
            :append-to-body="false"
            :modal="true"
          >
            <el-table
              :data="moviedata"
              border
              style="width:100%"
              :row-style="{'color':'#333333','font-size':'16px'}"
              :header-cell-style="{'color':'#666666','font-size':'16px'}"
            >
              <el-table-column type="index" label="序号" width="150"></el-table-column>
              <el-table-column prop="vedioUrl" label="视频地址"></el-table-column>
              <el-table-column label="预览" width="250">
                <template slot-scope="scope">
                  <el-button size="mini" icon="el-icon-view" @click="watch(scope.row.vedioUrl)">预览</el-button>
                </template>
              </el-table-column>
            </el-table>
          </el-dialog>
        </div>
      </div>

      <div class="bottom">
        <div class="suscribetitle">
          <img src="../assets/xiajia@2x.png" alt>
          <div class="suspro">已下架项目</div>
        </div>
        <el-card
          :body-style="{ padding: '0px' }"
          class="card"
          v-for="(item) of data1"
          :key="item.id"
        >
          <img src="../assets/example.png" class="image">
          <div class="detail">
            <span style="font-size:18px;weight:bold;color:#2B4A7E">{{item.title}}</span>
            <div class="time">
              <img src="../assets/shijian1@2x.png" alt>
              <span style="font-size:12px;font-weight:bold;color:#666666">{{item.timeLength}}</span>
              <div class="love">
                <div :class="item" v-for="item in item.score" :key="item.id"></div>
              </div>
            </div>
            <div class="button">
              <el-button type="text" @click="manage(item.title,item.courseId)">管理</el-button>
              <el-button type="text" @click="view(item.courseId)">预览</el-button>
              <el-button type="text" @click="material(item.courseId)">材料</el-button>
              <el-button type="text" @click="shangjia(item.courseId)">上架</el-button>
            </div>
          </div>
        </el-card>
      </div>
      <div class="materialdialog">
        <el-dialog
          :visible.sync="materialdialogVisible"
          width="80%"
          custom-class="dialog"
          :close-on-click-modal="false"
          top="7vh"
          title="材料列表"
          :modal-append-to-body="false"
          :append-to-body="false"
          :modal="true"
          :beforeClose="close"
        >
          <el-table
            v-if="hackReset"
            :data="materialData"
            border
            style="width:100%"
            :row-style="{'color':'#333333','font-size':'16px'}"
            :header-cell-style="{'color':'#666666','font-size':'16px'}"
          >
            <el-table-column type="index" label="序号" width="150"></el-table-column>
            <el-table-column prop="title" label="材料"></el-table-column>
            <el-table-column prop="picUrl" label="图片地址"></el-table-column>
            <el-table-column label="预览" width="250">
              <template slot-scope="scope">
                <!-- <el-button size="mini" @click="handlePrew(scope.$index, scope.row)">预览</el-button> -->
                <div>
                  <img
                    :preview="scope.row.courseId"
                    :preview-text="scope.row.title"
                    :src="(dapi + (scope.row.picUrl).substring(0, (scope.row.picUrl).length - 1))"
                    style="width:50px;height:50px"
                  >
                </div>
              </template>
            </el-table-column>
          </el-table>
        </el-dialog>
      </div>
    </div>
  </div>
</template>
<script>
import Vue from "vue";
import Video from "./Video.vue";
export default {
  components: {
    Video
  },
  data() {
    return {
      title: "",
      data: [],
      data1: [],
      dialogVisible: false,
      vedioUrl: "",
      palyerReset: false,
      hackReset: false,
      moviedata: [],
      moviedialogVisible: false,
      materialdialogVisible: false,
      dapi: "http://ldzwh.oss-cn-hangzhou.aliyuncs.com/"
    };
  },
  mounted() {
    this.getCourseList();
    this.getCourseList1();
  },
  created() {
    let body = {
      functionId: "0102"
    };
    this.http(this.api.getFunctionDesc, body).then(res => {
      this.title = res.data.data;
    });
  },
  methods: {
    playerclose() {
      this.palyerReset = false;
      this.dialogVisible = false;
    },
    view(courseId) {
      let body = {
        courseId: courseId
      };
      this.http(this.api.getCourseVedioList, body).then(res => {
        if (res.data.code == "0000") {
          this.moviedialogVisible = true;
          this.moviedata = res.data.data;
        }
      });
    },
    watch(url) {
      this.vedioUrl = url;
      //this.$router.push("/video");
      // console.log(this.$refs.player);
      this.dialogVisible = true;
      this.$nextTick(() => {
        this.palyerReset = true; //重建组件
      });
    },
    getCourseList() {
      let body = {
        status: "0"
      };
      this.http(this.api.getCourseList, body).then(res => {
        console.log(res);
        if (res.data.code == "0000") {
          let data = res.data.data;
          console.log(data);
          for (let i = 0; i < data.length; i++) {
            console.log(data[i].score);
            if (data[i].score == "0") {
              data[i].score = ["empty", "empty", "empty", "empty", "empty"];
            } else if (data[i].score == "1") {
              data[i].score = ["full", "empty", "empty", "empty", "empty"];
            } else if (data[i].score == "2") {
              data[i].score = ["full", "full", "empty", "empty", "empty"];
            } else if (data[i].score == "3") {
              data[i].score = ["full", "full", "full", "empty", "empty"];
            } else if (data[i].score == "4") {
              data[i].score = ["full", "full", "full", "full", "empty"];
            } else if (data[i].score == "5") {
              data[i].score = ["full", "full", "full", "full", "full"];
            }
          }
          this.data = data;
        }
      });
    },
    getCourseList1() {
      let body = {
        status: "1"
      };
      this.http(this.api.getCourseList, body).then(res => {
        console.log(res);
        if (res.data.code == "0000") {
          let data = res.data.data;
          console.log(data);
          for (let i = 0; i < data.length; i++) {
            console.log(data[i].score);
            if (data[i].score == "0") {
              data[i].score = ["empty", "empty", "empty", "empty", "empty"];
            } else if (data[i].score == "1") {
              data[i].score = ["full", "empty", "empty", "empty", "empty"];
            } else if (data[i].score == "2") {
              data[i].score = ["full", "full", "empty", "empty", "empty"];
            } else if (data[i].score == "3") {
              data[i].score = ["full", "full", "full", "empty", "empty"];
            } else if (data[i].score == "4") {
              data[i].score = ["full", "full", "full", "full", "empty"];
            } else if (data[i].score == "5") {
              data[i].score = ["full", "full", "full", "full", "full"];
            }
          }
          this.data1 = data;
        }
      });
    },
    xiajia(courseId) {
      let body = {
        courseId: courseId,
        data: "1",
        fieldName: "status",
        tableName: "tbl_school_course"
      };
      this.http(this.api.setField, body).then(res => {
        if (res.data.code == "0000") {
          this.$message({
            type: "success",
            message: "下架成功"
          });
          this.getCourseList1();
          this.getCourseList();
        }
      });
    },
    shangjia(courseId) {
      let body = {
        courseId: courseId,
        data: "0",
        fieldName: "status",
        tableName: "tbl_school_course"
      };
      this.http(this.api.setField, body).then(res => {
        if (res.data.code == "0000") {
          this.$message({
            type: "success",
            message: "上架成功"
          });
          this.getCourseList();
          this.getCourseList1();
        }
      });
    },
    manage(title, courseId) {
      window.localStorage.setItem("courseId", courseId);
      window.localStorage.setItem("title", title);
      this.$router.push("/ProjectIntroduction");
    },
    material(courseId) {
      let body = {
        courseId: courseId
      };
      this.http(this.api.getCourseMeterialList, body).then(res => {
        if (res.data.code == "0000") {
          this.materialdialogVisible = true;
          this.$nextTick(() => {
            this.hackReset = true; //重建组件
            this.materialData = res.data.data;
          });
        }
      });
    },
    close() {
      this.hackReset = false;
      this.materialdialogVisible = false;
    }
  }
};
</script>
<style lang="less">
.project {
  width: 100%;
  .woodproject {
    height: calc(100% - 51px);
    overflow: auto;
    width: 100%;
    display: flex;
    flex-direction: column;

    .top {
      flex: 1;
      box-sizing: border-box;
      padding: 0 20px 30px 20px;
      background-color: #ffffff;
      width: 100%;

      .suscribetitle {
        display: flex;
        height: 30px;
        margin-top: 5px;
        img {
          height: 30px;
          margin-right: 10px;
        }
        .suspro {
          width: 88px;
          height: 16px;
          font-size: 16px;
          font-family: PingFang-SC-Medium;
          font-weight: bold;
          color: rgba(51, 51, 51, 1);
          line-height: 30px;
        }
      }
      .card {
        width: 238px;
        height: 234px;
        background: rgba(255, 255, 255, 1);
        // border-radius: 5px;
        float: left;
        margin: 15px;
        .image {
          width: 100%;
          height: 136px;
          display: block;
        }
        .detail {
          padding: 8px;
          .time {
            clear: both;
            vertical-align: middle;
            border-bottom: 1px solid #e5e5e5;
            padding-bottom: 5px;
            img {
              vertical-align: middle;
            }
            span {
              height: 100%;
              vertical-align: middle;
              display: inline-block;
            }
            .love {
              display: inline-block;
              padding-left: 30px;
              // display: flex;
            }
          }
          .el-button {
            font-size: 14px;
            color: #40a9b0;
            font-weight: bold;
            float: right;
            margin-left: 8px;
          }
        }
      }
    }
    .bottom {
      flex: 1;
      margin-top: 10px;
      padding: 0 20px 30px 20px;
      box-sizing: border-box;
      background-color: #ffffff;
      width: 100%;

      .suscribetitle {
        display: flex;
        height: 30px;
        margin-top: 5px;
        img {
          height: 30px;
          margin-right: 10px;
        }
        .suspro {
          width: 88px;
          height: 16px;
          font-size: 16px;
          font-family: PingFang-SC-Medium;
          font-weight: bold;
          color: rgba(51, 51, 51, 1);
          line-height: 30px;
        }
      }
      .card {
        width: 238px;
        height: 234px;
        background: rgba(255, 255, 255, 1);
        // border-radius: 5px;
        float: left;
        margin: 15px;
        .image {
          width: 100%;
          height: 136px;
          display: block;
        }
        .detail {
          padding: 8px;
          .time {
            clear: both;
            vertical-align: middle;
            border-bottom: 1px solid #e5e5e5;
            padding-bottom: 5px;
            img {
              vertical-align: middle;
            }
            span {
              height: 100%;
              vertical-align: middle;
              display: inline-block;
            }
            .love {
              display: inline-block;
              vertical-align: middle;
              padding-left: 30px;
            }
          }
          .el-button {
            font-size: 14px;
            color: #40a9b0;
            font-weight: bold;
            float: right;
            margin-left: 8px;
          }
        }
      }
    }
  }
}
</style>
