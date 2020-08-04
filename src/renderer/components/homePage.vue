<template>
    <div class="main-page">
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
                :auto-upload="false"
                :limit="1">
                <el-button slot="trigger" size="small" type="primary">导入文件</el-button>
            </el-upload>
            <div>
              <el-button size="small" type="primary">导出文件</el-button>
              <el-button size="small" type="primary" @click="preview">界面预览</el-button>
            </div>
        </div>
        <div style="display: flex;">
          <!-- json预览 -->
            <div class="left-page">
                <json-viewer
                    :value="jsonData"
                    :expand-depth=5
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
                ref="tableDataRef"
                v-if="activeName == 'paramsConfig'"
                :data="paramsconfigData"
                style="width: 100%">
                <el-table-column
                    show-overflow-tooltip
                    prop="key"
                    label="key"
                    width="100">
                </el-table-column>
                <el-table-column
                    show-overflow-tooltip
                    prop="name"
                    label="name"
                    width="100">
                </el-table-column>
                <el-table-column
                    show-overflow-tooltip
                    prop="flex"
                    label="flex"
                    width="100">
                </el-table-column>
                <el-table-column
                    show-overflow-tooltip
                    prop="prefix"
                    label="prefix"
                    width="100"
                    :formatter="formatterPrefix">
                </el-table-column>
                <el-table-column
                    show-overflow-tooltip
                    prop="options"
                    label="options"
                    width="100">
                </el-table-column>
                <el-table-column
                    show-overflow-tooltip
                    prop="increase"
                    label="increase"
                    width="100">
                </el-table-column>
                <el-table-column
                    show-overflow-tooltip
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
              <!-- channelsConfig -->
              <el-form ref="form" :model="channelsConfigData" label-width="100px" v-if="activeName == 'channelsConfig'">
                <el-form-item label="status">
                    <el-select v-model="channelsConfigData.status">
                        <el-option label="true" :value="true"></el-option>
                        <el-option label="false" :value="false"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="channels">
                    <el-input-number v-model="channelsConfigData.channelNumber"></el-input-number>
                </el-form-item>
                 <el-form-item>
                    <el-button type="primary" @click="submitChannelConfig">保存</el-button>
                </el-form-item>
              </el-form>
              <!-- scannerConfig -->
              <el-form ref="form" :model="scannerConfigData" label-width="100px" v-if="activeName == 'scannerConfig'">
                <el-form-item label="connector">
                    <el-input v-model="scannerConfigData.connector" style="width: 100px"></el-input>
                </el-form-item>
                <el-form-item label="qrcode">
                    <el-select v-model="scannerConfigData.qrcode" multiple placeholder="请选择">
                      <el-option
                         v-for="(item,index) in paramsconfigData"
                        :key="index"
                        :label="item.name"
                        :value="index">
                      </el-option>
                    </el-select>
                    <!-- <el-input v-model="scannerConfigData.connector" style="width: 100px"></el-input> -->
                </el-form-item>
                 <el-form-item>
                    <el-button type="primary" @click="submitScannerConfig">保存</el-button>
                </el-form-item>
              </el-form>
              <!-- backendConfig -->
               <el-form ref="form" :model="backendConfigData" label-width="100px" v-if="activeName == 'backendConfig'">
                <el-form-item label="qrpadding">
                    <el-input v-model="backendConfigData.qrpadding" style="width: 150px"></el-input>
                </el-form-item>
                <el-form-item label="connector">
                    <el-input v-model="backendConfigData.connector" style="width: 150px"></el-input>
                </el-form-item>
                <el-form-item label="qrcode">
                    <el-select v-model="backendConfigData.qrcode" multiple placeholder="请选择">
                      <el-option
                         v-for="(item,index) in paramsconfigData"
                        :key="index"
                        :label="item.name"
                        :value="index">
                      </el-option>
                    </el-select>
                    <!-- <el-input v-model="scannerConfigData.connector" style="width: 100px"></el-input> -->
                </el-form-item>
                <el-form-item label="note">
                    <el-input v-model="backendConfigData.note" style="width: 150px"></el-input>
                </el-form-item>
                 <el-form-item>
                    <el-button type="primary" @click="submitBackendConfig">保存</el-button>
                </el-form-item>
              </el-form>
              <!-- webConfig -->
              <el-form  v-if="activeName == 'webConfig'">
                <el-form-item v-for="item in webConfigData" style="margin-left:10px;">
                    <el-select multiple placeholder="请选择" v-model="item.value">
                      <el-option
                          v-for="(item,index) in paramsconfigData"
                          :key="index"
                          :label="item.name"
                          :value="index">
                        </el-option>
                    </el-select>
                    <el-checkbox v-model="item.status">自增</el-checkbox>
                    <el-button type="danger" size="small" style="margin-left: 10px;" @click="deleteWebconfig(item)">删除</el-button>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" size="small" style="margin-left: 10px" @click="addWebconfig">新增</el-button>
                    <el-button type="primary" size="small" style="margin-left: 10px" @click="submitWebConfig">保存</el-button>
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
                    <el-select v-model="paramsconfigForm.validate" size="small">
                        <el-option label="Any-Number" value="Any-Number"></el-option>
                        <el-option label="Number" value="Number"></el-option>
                        <el-option label="CN" value="CN"></el-option>
                        <el-option label="Number|EN" value="Number|EN"></el-option>
                        <el-option label="EN" value="EN"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false" size="small">取 消</el-button>
                <el-button type="primary" @click="submitParamsConfig" size="small">确 定</el-button>
            </div>
        </el-dialog>
        <!-- 界面预览 -->
        <el-dialog title="界面预览" :visible.sync="previewVisible" class="dialog-form">
          <div  v-if="jsonData">
            <div v-for="(item, index) in jsonData.webConfig" :key="index">
              <div v-for="(value,index) in item.value" :key="index" :style="{flex:jsonData.paramsConfig[value].flex}" style="display: flex;align-items:center;">
                 <el-select v-if="jsonData.paramsConfig[value].options.length>0"  :placeholder="jsonData.paramsConfig[value].name" style="width: 200px;margin-bottom: 10px;">
                  <el-option
                    v-for="item in jsonData.paramsConfig[value].options"
                    :key="item"
                    :label="item"
                    :value="item">
                  </el-option>
                </el-select>
                <el-input v-else class="params-input" :placeholder="jsonData.paramsConfig[value].name" :disabled="jsonData.paramsConfig[value].prefix" style="width: 200px;margin-bottom: 10px;"></el-input>
                <div v-if="jsonData.increase" style="display: flex;align-items:center;">
                  <el-input v-show="item.status"  maxlength="3" size="mini" style="width: 100px;margin-left: 10px;"/>  
                  <el-checkbox v-show="item.status" style="margin-left: 10px;">自增</el-checkbox>
                </div>
              </div>
            </div>
          </div>  
        </el-dialog>
    </div>
</template>
<script>
import JsonViewer from 'vue-json-viewer'
const fs = require('fs')

export default {
  data () {
    return {
      jsonData: {},
      paramsconfigData: [],
      channelsConfigData: {},
      increaseConfigData: {},
      scannerConfigData: {},
      backendConfigData: {},
      webConfigData: [],
      fileList: [],
      tableData: [],
      activeName: 'paramsConfig',
      dialogFormVisible: false,
      previewVisible: false,
      paramsconfigForm: {},
      optionsStr: '',
      paramsIndex: 0
    }
  },
  components: {
    JsonViewer
  },
  watch: {
    jsonData: {
      handler (val) {
        console.log(val)
        console.log('77777')
      },
      deep: true
    }
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
    preview () {
      this.previewVisible = true
    },
    initData () {
      this.jsonData = {}
      this.paramsconfigData = []
    },
    submitUpload () {
      this.$refs.upload.submit()
    },
    handleRemove (file, fileList) {
      console.log('handleRemove')
      console.log(file, fileList)
      this.initData()
    },
    handlePreview (file) {
      console.log('handlePreview1')
      console.log(file.path)
    },
    handleChange (file, fileList) {
      console.log('handleChange')
      console.log(file.raw.path)
      if (fileList.length > 0) {
        this.fileList = [fileList[fileList.length - 1]]
      }
      try {
        let path = file.raw.path
        let _z = fs.readFileSync(path, 'utf8')
        _z = JSON.parse(_z.replace(/^\uFEFF/, ''))
        this.jsonData = _z
        this.paramsconfigData = _z.paramsConfig
        this.channelsConfigData = Object.assign({}, _z.channelsConfig)
        this.scannerConfigData = Object.assign({}, _z.scannerConfig)
        this.backendConfigData = Object.assign({}, _z.backendConfig)
        this.webConfigData = _z.webConfig
        console.log('webConfigData')
        console.log(this.webConfigData)
        console.log('jsonData')
        console.log(this.jsonData.increase)
      } catch (error) {
        this.$message.error('json格式有误，请检查')
        console.log(error)
      }
    },
    handleClick () {
      this.paramsconfigData = this.jsonData.paramsConfig
      this.channelsConfigData = Object.assign({}, this.jsonData.channelsConfig)
      this.scannerConfigData = Object.assign({}, this.jsonData.scannerConfig)
      this.backendConfigData = Object.assign({}, this.jsonData.backendConfig)
    },
    editParams (scope) {
      console.log(scope)
      this.paramsIndex = scope.$index
      this.paramsconfigForm = Object.assign({}, scope.row)
      let _this = this
      _this.optionsStr = ''
      scope.row.options.forEach(item => {
        _this.optionsStr += item + ','
      })
      this.$set(this.paramsconfigForm, 'options', this.optionsStr)
      if (scope.row.prefix === true) {
        this.$set(this.paramsconfigForm, 'prefix', 'true')
      }
      if (scope.row.prefix === false) {
        this.$set(this.paramsconfigForm, 'prefix', 'false')
      }
      console.log(this.optionsStr)
      console.log(this.paramsIndex)
      this.dialogFormVisible = true
    },
    deleteParams (scope) {
      console.log(scope.row)
      this.$confirm('确定要删除该条配置信息?', '提示', {
        confirmButtonText: '确定 ',
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
    },
    deleteWebconfig (row) {
      let index = this.webConfigData.indexOf(row)
      this.webConfigData.splice(index, 1)
      console.log('row')
      console.log(row)
      console.log(this.webConfigData)
    },
    addWebconfig () {
      this.webConfigData.push({
        value: [],
        status: false
      })
    },
    submitParamsConfig () {
      try {
        this.paramsconfigData[this.paramsIndex] = Object.assign({}, this.paramsconfigForm)
        let optionsArr = this.paramsconfigForm.options ? this.paramsconfigForm.options.split(',') : []
        this.$set(this.paramsconfigData[this.paramsIndex], 'options', optionsArr)
        this.$set(this.jsonData, 'paramsConfig', this.paramsconfigData)
        if (this.paramsconfigData.prefix === 'true') {
          this.$set(this.jsonData, 'prefix', 'true')
        }
        if (this.paramsconfigData.prefix === 'false') {
          this.$set(this.jsonData, 'prefix', 'false')
        }
        console.log(this.jsonData)
        console.log('jsonData')
        this.dialogFormVisible = false
        this.$nextTick(() => {
          this.$refs.tableDataRef.doLayout()
        })
        setTimeout(() => {
          this.$message({
            message: '修改params参数成功',
            type: 'success'
          })
        }, 500)
      } catch (err) {
        console.log(err)
        this.$message({
          message: '修改params参数失败',
          type: 'error'
        })
      }
      console.log(this.jsonData)
      console.log('this.jsonData')
    },
    submitChannelConfig () {
      console.log(this.channelsConfigData)
      console.log('channelsConfigData')
      try {
        this.$set(this.jsonData, 'channelsConfig', this.channelsConfigData)
        setTimeout(() => {
          this.$message({
            message: '修改channles参数成功',
            type: 'success'
          })
        }, 500)
      } catch (err) {
        console.log(err)
        this.$message({
          message: '修改channles参数失败',
          type: 'error'
        })
      }
    },
    submitScannerConfig () {
      console.log(this.scannerConfigData)
      try {
        this.$set(this.jsonData, 'scannerConfig', this.scannerConfigData)
        setTimeout(() => {
          this.$message({
            message: '修改scanner参数成功',
            type: 'success'
          })
        }, 500)
      } catch (err) {
        console.log(err)
        this.$message({
          message: '修改scanner参数失败',
          type: 'error'
        })
      }
    },
    submitBackendConfig () {
      try {
        this.$set(this.jsonData, 'backendConfig', this.backendConfigData)
        setTimeout(() => {
          this.$message({
            message: '修改backend参数成功',
            type: 'success'
          })
        }, 500)
      } catch (err) {
        console.log(err)
        this.$message({
          message: '修改backend参数失败',
          type: 'error'
        })
      }
    },
    submitWebConfig () {
      console.log(this.webConfigData)
      console.log('webConfigData')
      try {
        this.$set(this.jsonData, 'webConfig', this.webConfigData)
        setTimeout(() => {
          this.$message({
            message: '修改webconfig参数成功',
            type: 'success'
          })
        }, 500)
      } catch (err) {
        console.log(err)
        this.$message({
          message: '修改webconfig参数失败',
          type: 'error'
        })
      }
    },
    formatterPrefix (row, column) {
      if (row.prefix === true || row.prefix === 'true') {
        return row.prefix + ''
      }
      if (row.prefix === false || row.prefix === 'false') {
        return row.prefix + ''
      }
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