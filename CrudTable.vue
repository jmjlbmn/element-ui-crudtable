<template>
  <div class="container">
    <el-col v-if="showAdd||showSearch||showMultiDel||$slots.toolbar||$slots.toolbar2||$slots.toolbar3||$slots.toolbar4" :span="24" class="toolbar">
      <slot name="toolbar" />
      <el-button v-if="showAdd" type="primary" icon="el-icon-edit" @click="add">{{addBtn}}</el-button>
      <slot name="toolbar2" />
      <el-button v-if="showSearch" type="success" icon="el-icon-search" plain @click="searchVisible=true">{{searchBtn}}</el-button>
      <slot name="toolbar3" />
      <el-button v-if="showMultiDel" type="danger" icon="el-icon-delete" plain @click="delMulti" :disabled="select.length===0">{{multiDelBtn}}</el-button>
      <slot name="toolbar4" />
    </el-col>
    <el-table class="table" height="auto" :data="data" v-loading="loading" stripe highlight-current-row @row-click="rowClick" @selection-change="sel=>{this.select = sel}">
      <el-table-column v-if="showMultiDel||showCheckBox" type="selection" fixed="left" />
      <slot name="cols" />
      <el-table-column v-if="showEdit||showDel||$scopedSlots.opBtn||$scopedSlots.opBtn2||$scopedSlots.opBtn3" :label="opBtn" fixed="right" :min-width="opBtnWidth+'px'">
        <template slot-scope="scope">
          <slot name="opBtn" :row="scope.row" />
          <el-button v-if="showEdit" size="small" type="primary" plain @click="edit(scope.row)">{{editBtn}}</el-button>
          <slot name="opBtn2" :row="scope.row" />
          <el-button v-if="showDel" size="small" type="danger" plain @click="del(scope.row)">{{delBtn}}</el-button>
          <slot name="opBtn3" :row="scope.row" />
        </template>
      </el-table-column>
    </el-table>
    <el-pagination v-if="showPage" class="pagebar" background layout="sizes, prev, pager, next, jumper, ->, total" :current-page="page" :page-size="size" :total="total" @size-change="sizeChange" @current-change="currentChange">
    </el-pagination>
    <el-dialog append-to-body v-if="showAdd||showEdit" :title="editTitle" :width="dlgWidth" :visible="editVisible" @close="close" :close-on-click-modal="false" :close-on-press-escape="false">
      <el-form :label-position="editLabelPosition" :style="{margin:editMargin}" :model="editModel" :label-width="labelWidth+'px'" label-suffix="：" status-icon :rules="editRule" ref="editForm">
        <slot name="editCols" :editModel="editModel"></slot>
      </el-form>
      <template slot="footer">
        <el-button icon="el-icon-circle-close-outline" @click="close">{{cancelBtn}}</el-button>
        <el-button icon="el-icon-check" type="primary" @click="submit" :loading="editLoading">{{submitBtn}}</el-button>
      </template>
    </el-dialog>
    <el-dialog append-to-body v-if="showSearch" :title="searchTitle" :width="searchDlgWidth" :visible="searchVisible" @close="searchVisible= false">
      <el-form :label-position="searchLabelPosition" :style="{margin:searchMargin}" :model="searchModel" :label-width="searchLabelWidth+'px'" label-suffix="：">
        <slot name="searchCols" :searchModel="searchModel" />
      </el-form>
      <template slot="footer">
        <el-button @click="refresh" :loading="loading" icon="el-icon-refresh">{{restoreBtn}}</el-button>
        <el-button type="primary" @click="search" :loading="loading" icon="el-icon-search">{{searchBtn}}</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script>
import { log } from "@/util";
export default {
  props: {
    api: {
      required: true
    },
    idKey: {
      type: String,
      default: "id"
    },
    showMultiDel: {
      type: Boolean,
      default: false
    },
    showCheckBox: {
      type: Boolean,
      default: false
    },
    showDel: {
      type: Boolean,
      default: true
    },
    showEdit: {
      type: Boolean,
      default: true
    },
    showAdd: {
      type: Boolean,
      default: true
    },
    showSearch: {
      type: Boolean,
      default: true
    },
    showPage: {
      type: Boolean,
      default: true
    },
    editDefault: {
      type: Object
    },
    editRule: {
      type: Object
    },
    editLabelPosition: {
      type: String,
      default: "right"
    },
    searchLabelPosition: {
      type: String,
      default: "right"
    },
    editMargin: {
      type: String,
      default: "0 30px 0 20px"
    },
    searchMargin: {
      type: String,
      default: "0 30px 0 20px"
    },
    searchLabelWidth: {
      type: Number,
      default: 100
    },
    searchDefault: {
      type: Object
    },
    labelWidth: {
      type: Number,
      default: 100
    },
    dlgWidth: {
      type: String,
      default: "600px"
    },
    searchDlgWidth: {
      type: String,
      default: "500px"
    },
    editBtn: {
      type: String,
      default: "编辑"
    },
    delBtn: {
      type: String,
      default: "删除"
    },
    addBtn: {
      type: String,
      default: "新增"
    },
    searchBtn: {
      type: String,
      default: "筛选"
    },
    multiDelBtn: {
      type: String,
      default: "批量删除"
    },
    restoreBtn: {
      type: String,
      default: "恢复默认"
    },
    cancelBtn: {
      type: String,
      default: "取消"
    },
    submitBtn: {
      type: String,
      default: "提交"
    },
    editTitle: {
      type: String,
      default: "编辑信息"
    },
    searchTitle: {
      type: String,
      default: "筛选数据"
    },
    opBtn: {
      type: String,
      default: "操作"
    },
    opBtnWidth: {
      type: Number,
      default: 200
    },
    delDesc: {
      type: String,
      default: "确认要删除该记录吗?"
    },
    multiDelDesc: {
      type: String,
      default: "确认要删除所选记录吗?"
    },
    editExtendRow: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      page: 1,
      size: 10,
      total: 0,
      data: [],
      select: [],
      loading: false,
      editModel: {},
      editVisible: false,
      editLoading: false,
      searchModel: {},
      searchVisible: false
    };
  },
  methods: {
    getData(page) {
      if (page) {
        this.page = page;
      }
      log(
        "加载第" + this.page + "页,每页" + this.size + "条,筛选条件",
        this.searchModel
      );
      this.loading = true;
      this.api
        .page(this.searchModel, this.page, this.size)
        .then(res => {
          log("返回数据", res);
          this.loading = false;
          this.total = res.total;
          this.data = res.rows;
        })
        .catch(() => (this.loading = false));
    },
    add() {
      log("新增");
      this.editModel = Object.assign({}, this.editDefault);
      this.editVisible = true;
      this.$emit("openAdd");
      this.$emit("openAddOrEdit");
    },
    edit(row) {
      log("编辑", row);
      this.editModel = Object.assign({}, this.editDefault);
      this.editVisible = true;
      this.$emit("openEdit", row);
      this.$emit("openAddOrEdit", row);
      if (this.api.get) {
        log("获取记录id=" + row[this.idKey]);
        this.api.get(row[this.idKey]).then(res => {
          log("返回数据", res);
          if (this.editExtendRow) {
            this.editModel = Object.assign({}, this.editModel, row);
          }
          this.editModel = Object.assign({}, this.editModel, res);
        });
      } else {
        this.editModel = Object.assign({}, this.editModel, row);
      }
    },
    del(row) {
      this.$confirm(this.delDesc, "警告", {
        type: "warning"
      }).then(() => {
        log("删除", row);
        this.api.del(row[this.idKey]).then(() => {
          this.$message({
            message: "删除成功",
            type: "success"
          });
          this.getData();
        });
      });
    },
    delMulti() {
      log("批量删除", this.select);
      this.$confirm(this.multiDelDesc, "警告", {
        type: "warning"
      }).then(() => {
        this.api
          .delMulti(this.select.map(x => x[this.idKey]).join(","))
          .then(() => {
            this.$message({
              message: "删除成功",
              type: "success"
            });
            this.getData();
          });
      });
    },
    search() {
      this.page = 1;
      this.searchVisible = false;
      this.getData();
    },
    refresh() {
      this.page = 1;
      this.searchModel = Object.assign({}, this.searchDefault);
      this.searchVisible = false;
      this.getData();
    },
    submit() {
      this.$refs.editForm.validate(valid => {
        if (valid) {
          this.editLoading = true;
          let ajax;
          if (this.editModel[this.idKey] === undefined) {
            log("新增", this.editModel);
            ajax = this.api.add(this.editModel);
          } else {
            log("更新,id=" + this.editModel[this.idKey], this.editModel);
            console.log(this.editModel);
            ajax = this.api.update(this.editModel[this.idKey], this.editModel);
          }
          ajax
            .then(() => {
              this.$message({
                message: "操作成功",
                type: "success"
              });
              this.editLoading = false;
              this.editVisible = false;
              this.$refs.editForm.resetFields();
              this.getData();
            })
            .catch(() => {
              this.editLoading = false;
            });
        }
      });
    },
    close() {
      this.editVisible = false;
      this.$refs.editForm.clearValidate();
    },
    sizeChange(size) {
      this.page = 1;
      this.size = size;
      this.getData();
    },
    currentChange(page) {
      this.page = page;
      this.getData();
    },
    rowClick(row, event, column) {
      this.$emit("row-click", row, event, column);
    }
  },
  watch: {
    editDefault(val) {
      this.editModel = Object.assign(this.editModel, val);
    },
    searchDefault(val) {
      this.searchModel = Object.assign(this.searchModel, val);
      this.$nextTick().then(() => {
        this.getData(1);
      });
    }
  },
  mounted() {
    this.editModel = Object.assign({}, this.editDefault);
    this.searchModel = Object.assign({}, this.searchDefault);
    this.getData();
  }
};
</script>

<style lang="scss" scoped>
@import "../style/mixin";
.container {
  display: flex;
  flex-direction: column;
  .table {
    margin: 15px 0;
  }
}
</style>
