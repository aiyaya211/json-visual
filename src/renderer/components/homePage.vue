<template>
    <div class="main-page">
         <!-- <vue-json-editor 
         lang="zh"   
         v-model="json" 
         :show-btns="true" 
         :expandedOnStart="true" 
         @json-change="onJsonChange">
         </vue-json-editor> -->
        <div style="margin-bottom: 10px;display:flex;justify-content:space-between;">
            <el-upload
                :multiple="false"
                class="upload-demo"
                ref="upload"
                action=""
                :on-change="handleChange"
                :on-preview="handlePreview"
                :on-remove="handleRemove"
                :file-list="fileList"
                :auto-upload="false">
                <el-button slot="trigger" size="small" type="primary">导入文件</el-button>
            </el-upload>
            <div>
            <el-button size="small" type="primary" @click="importJson">生成json</el-button>
            </div>
        </div>
        <div style="display: flex;">
            <div class="left-page">
                <json-viewer
                    :value="jsonData"
                    :expand-depth=10
                    copyable
                    boxed>
                </json-viewer>
            </div>
            <div class="right-page">
                <el-tabs v-model="activeName" @tab-click="handleClick">
                    <el-tab-pane label="paramsConfig" name="paramsConfig"></el-tab-pane>
                    <el-tab-pane label="channelsConfig" name="channelsConfig"></el-tab-pane>
                    <el-tab-pane label="webConfig" name="webConfig"></el-tab-pane>
                    <el-tab-pane label="increase" name="increase"></el-tab-pane>
                    <el-tab-pane label="scannerConfig" name="scannerConfig"></el-tab-pane>
                    <el-tab-pane label="backendConfig" name="backendConfig"></el-tab-pane>
                </el-tabs>
                <!-- paramsconfig -->
               <el-table
                v-if="activeName == 'paramsConfig'"
                :data="paramsconfigData"
                style="width: 100%">
                <el-table-column
                    prop="key"
                    label="key"
                    width="100">
                </el-table-column>
                <el-table-column
                    prop="name"
                    label="name"
                    width="100">
                </el-table-column>
                <el-table-column
                    prop="flex"
                    label="flex"
                    width="100">
                </el-table-column>
                <el-table-column
                    prop="prefix"
                    label="prefix"
                    width="100">
                </el-table-column>
                <el-table-column
                    prop="options"
                    label="options"
                    width="100">
                </el-table-column>
                <el-table-column
                    prop="increase"
                    label="increase"
                    width="100">
                </el-table-column>
                <el-table-column
                    prop="validate"
                    label="validate"
                    width="100">
                </el-table-column>
                 <el-table-column
                fixed="right"
                label="操作"
                width="100">
                <template slot-scope="scope">
                    <el-button type="text" size="small" @click="editParams(scope)">编辑</el-button>
                    <el-button type="text" size="small" @click="deleteParams(scope)">删除</el-button>
                </template>
                </el-table-column>
              </el-table>
              <el-form ref="form" :model="channelsConfigData" label-width="100px" v-if="activeName == 'channelsConfig'">
                <el-form-item label="status">
                    <el-select v-model="channelsConfigData.status">
                        <el-option label="true" :value="true"></el-option>
                        <el-option label="false" :value="false"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="channelNumber">
                    <el-input-number v-model="channelsConfigData.channelNumber"></el-input-number>
                </el-form-item>
              </el-form>
            </div>
        </div>
        <!-- 修改paramsConfig -->
        <el-dialog title="paramsConfig配置" :visible.sync="dialogFormVisible" class="dialog-form">
            <el-form :model="paramsconfigForm">
                <el-form-item label="key" label-width="100px">
                    <el-input v-model="paramsconfigForm.key" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="name" label-width="100px">
                    <el-input v-model="paramsconfigForm.name" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="flex" label-width="100px">
                    <el-input v-model="paramsconfigForm.flex" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="prefix" label-width="100px">
                    <el-input v-model="paramsconfigForm.prefix" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="options" label-width="100px">
                    <el-input v-model="paramsconfigForm.options" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="increase" label-width="100px">
                    <el-input v-model="paramsconfigForm.increase" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="validate" label-width="100px">
                    <el-input v-model="paramsconfigForm.validate" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <!-- <el-form-item label="活动区域" :label-width="formLabelWidth">
                <el-select v-model="form.region" placeholder="请选择活动区域">
                    <el-option label="区域一" value="shanghai"></el-option>
                    <el-option label="区域二" value="beijing"></el-option>
                </el-select>
                </el-form-item> -->
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false" size="small">取 消</el-button>
                <el-button type="primary" @click="dialogFormVisible = false" size="small">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>
<script>
import JsonViewer from 'vue-json-viewer'
const fs = require('fs')

export default {
  data () {
    const data = [{
      id: 1,
      children: []
    }, {
      id: 2,
      children: []
    }, {
      id: 3,
      children: []
    }]
    return {
      jsonData: {},
      paramsconfigData: [],
      channelsConfigData: {},
      fileList: [],
      tableData: [],
      data: JSON.parse(JSON.stringify(data)),
      activeName: 'paramsConfig',
      dialogFormVisible: false,
      paramsconfigForm: {}
    }
  },
  components: {
    JsonViewer
  },
  methods: {
    onJsonChange (value) {
      console.log('value:', value)
    },

    importJson () {
      fs.readFile('config1.json', function (err, data) {
        if (err) {
          return console.error(err)
        }

        let _z = JSON.parse(data.replace(/^\uFEFF/, ''))
        console.log('异步读取: ' + _z)
      })
    },
    submitUpload () {
      this.$refs.upload.submit()
    },
    handleRemove (file, fileList) {
      console.log('handleRemove')
      console.log(file, fileList)
    },
    handlePreview (file) {
      console.log('handlePreview1')
      console.log(file.path)
    },
    handleChange (file) {
      console.log('handleChange')
      console.log(file.raw.path)
      let path = file.raw.path
      let _z = fs.readFileSync(path, 'utf8')
      _z = JSON.parse(_z.replace(/^\uFEFF/, ''))
      this.jsonData = _z
      this.paramsconfigData = _z.paramsConfig
      this.channelsConfigData = _z.channelsConfig
      console.log(this.jsonData)
    //   this.paramsconfigData = _z.
    //   fs.readFile(path, function (err, data) {
    //     if (err) {
    //       return console.error(err)
    //     }
    //     _this.jsonData = data.toString()
    //     console.log('异步读取: ' + data.toString())
    //   })
    // }
    },
    handleClick () {},
    editParams (scope) {
      console.log(scope.row)
      this.paramsconfigForm = scope.row
      this.dialogFormVisible = true
    },
    deleteParams (scope) {
      console.log(scope.row)
      this.$confirm('确定要删除该条配置信息?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let index = this.paramsconfigData.indexOf(scope.row)
        this.paramsconfigData.splice(index, 1)
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    }
  }
}
</script>
<style lang="scss">
.main-page {
    // display: flex;
    height: 500px;
}
.left-page {
    flex: 2;
}
.right-page {
    margin-left: 10px;
    flex: 3;
    overflow: scroll;
    height: 440px;
    border: 1px solid #eee;
    border-radius: 6px;
}
.el-tree-node__content {
    margin-top: 10px;
    margin-bottom:  10px;
}
.custom-tree-node  {
    .el-input--small {
       width: 120px; 
    }
    .el-input__inner {
        width: 120px;
    }
}
.jv-container .jv-code {
    overflow-y: scroll;
}
.jv-container .jv-code.boxed {
    max-height: 400px;
}
.dialog-form {
   .el-form-item {
       margin-bottom: 0;
   } 
}
</style>