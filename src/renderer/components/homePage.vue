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
              <el-button size="small" type="primary" @click="exportJson">导出文件</el-button>
              <el-button size="small" type="primary" @click="preview">界面预览</el-button>
              <el-button size="small" type="primary" v-if="activeName === 'paramsConfig'" @click="addParamsConfig">新增params配置</el-button>
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
                    <!-- <el-tab-pane label="increase" name="increase"></el-tab-pane> -->
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
                    prop="tailvalue"
                    label="tailvalue"
                    width="100">
                </el-table-column>
                <el-table-column
                    show-overflow-tooltip
                    prop="prevalue"
                    label="prevalue"
                    width="100">
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
                <el-form-item v-for="(item,index) in webConfigData" :key="index" style="margin-left:10px;">
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
                    <el-select v-model="paramsconfigForm.prefix" size="small">
                        <el-option label="true" :value="true"></el-option>
                        <el-option label="false" :value="false"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="tailvalue" label-width="100px">
                    <el-input v-model="paramsconfigForm.tailvalue" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="prevalue" label-width="100px">
                    <el-input v-model="paramsconfigForm.prevalue" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="options" label-width="100px">
                    <el-input v-model="paramsconfigForm.options" size="small"  autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="increase" label-width="100px">
                  <el-select v-model="paramsconfigForm.increase" size="small">
                      <el-option label="0" :value="0"></el-option>
                      <el-option label="1" :value="1"></el-option>
                  </el-select>
                    <!-- <el-input v-model="paramsconfigForm.increase" size="small"  autocomplete="off"></el-input> -->
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
                <el-select 
                   v-model="paramsOption"
                   v-if="jsonData.paramsConfig[value].options.length>0"  
                   :placeholder="jsonData.paramsConfig[value].name" 
                   style="width: 200px;margin-bottom: 10px;">
                  <el-option
                    v-for="(item,key) in jsonData.paramsConfig[value].options"
                    :key="key"
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
      increaseConfigData: [],
      backendConfigscannerConfigData: {},
      backendConfigData: {},
      webConfigData: [],
      fileList: [],
      tableData: [],
      activeName: 'paramsConfig',
      dialogFormVisible: false,
      previewVisible: false,
      paramsconfigForm: {},
      optionsStr: '',
      paramsIndex: 0,
      paramsOption: '',
      isEdit: true,
      path: '',
      valueArr: [],
      scannerQrArr: [],
      backendQrArr: [],
      increaseArr: []
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
      // console.log('value:', value)
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
      this.channelsConfigData = {}
      this.increaseConfigData = []
      this.scannerConfigData = {}
      this.backendConfigData = {}
      this.webConfigData = []
    },
    submitUpload () {
      this.$refs.upload.submit()
    },
    handleRemove (file, fileList) {
      this.initData()
    },
    handlePreview (file) {
      console.log('handlePreview1')
      console.log(file.path)
    },
    handleChange (file, fileList) {
      if (fileList.length > 0) {
        this.fileList = [fileList[fileList.length - 1]]
      }
      try {
        this.path = file.raw.path
        let _z = fs.readFileSync(this.path, 'utf8')
        _z = JSON.parse(_z.replace(/^\uFEFF/, ''))
        // 此处需要用深拷贝
        this.jsonData = _z
        this.paramsconfigData = JSON.parse(JSON.stringify(_z.paramsConfig))
        this.channelsConfigData = JSON.parse(JSON.stringify(_z.channelsConfig))
        this.scannerConfigData = JSON.parse(JSON.stringify(_z.scannerConfig))
        this.backendConfigData = JSON.parse(JSON.stringify(_z.backendConfig))
        this.webConfigData = JSON.parse(JSON.stringify(_z.webConfig))
        this.increaseConfigData = JSON.parse(JSON.stringify(_z.increase))
      } catch (error) {
        this.$message.error('json格式有误，请检查')
        console.log(error)
      }
    },
    handleClick () {
      if (JSON.stringify(this.jsonData) !== '{}') {
        this.paramsconfigData = JSON.parse(JSON.stringify(this.jsonData.paramsConfig))
        this.channelsConfigData = JSON.parse(JSON.stringify(this.jsonData.channelsConfig))
        this.scannerConfigData = JSON.parse(JSON.stringify(this.jsonData.scannerConfig))
        this.backendConfigData = JSON.parse(JSON.stringify(this.jsonData.backendConfig))
        this.webConfigData = JSON.parse(JSON.stringify(this.jsonData.webConfig))
      }
    },
    addParamsConfig () {
      this.isEdit = false
      this.paramsconfigForm = {}
      this.dialogFormVisible = true
    },
    editParams (scope) {
      this.isEdit = true
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
      this.dialogFormVisible = true
    },
    deleteParams (scope) {
      this.valueArr = []
      this.scannerQrArr = []
      this.backendQrArr = []
      this.increaseArr = []
      // console.log(scope.row)
      this.$confirm('确定要删除该条配置信息?', '提示', {
        confirmButtonText: '确定 ',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let arrIndex = this.paramsconfigData.indexOf(scope.row)
        console.log(arrIndex)
        console.log('arrIndex')
        this.paramsconfigData.splice(arrIndex, 1)
        this.jsonData.paramsConfig.splice(arrIndex, 1)
        // 20200810 若做了参数删除 则需要更新所有获取到paramsindex的地方
        this.webConfigData = this.webConfigData.map((i, key) => {
          const status = i.status
          const index = i.index
          // const status = i.status
          // const index = i.index ? i.index : null
          this.valueArr[key] = []
          i.value.map(item => {
            console.log(item)
            console.log('item')
            if (item < arrIndex) {
              this.valueArr[key].push(item)
            }
            if (item > arrIndex) {
              this.valueArr[key].push(item - 1)
            }
            if (item === arrIndex) {
              let _index = i.value.indexOf(item)
              this.valueArr[key].splice(_index, 1)
            }
          })
          if (index) {
            console.log(111)
            return {
              status: status,
              index: index,
              value: this.valueArr[key]
            }
          } else {
            console.log(222)
            return {
              status: status,
              value: this.valueArr[key]
            }
          }
        })
        console.log(this.increaseConfigData)
        this.increaseConfigData.map((i, index) => {
          console.log('increaseConfigData')
          console.log(i)
          console.log('i')
          this.increaseArr[index] = []
          i.map(item => {
            console.log(item)
            console.log('item')
            if (item < arrIndex) {
              console.log(999)
              this.increaseArr[index].push(item)
            }
            if (item > arrIndex) {
              console.log(8888)
              this.increaseArr[index].push(item - 1)
            }
            if (item === arrIndex) {
              console.log(6666)
              let _index = i.indexOf(item)
              this.increaseArr[index].splice(_index, 1)
            }
          })
        })
        this.scannerConfigData.qrcode.map((i, index) => {
          console.log('scannerConfigData')
          console.log(this.scannerConfigData)
          if (i < arrIndex) {
            this.scannerQrArr.push(i)
          }
          if (i > arrIndex) {
            this.scannerQrArr.push(i - 1)
          }
          if (i === arrIndex) {
            let _index = this.scannerConfigData.qrcode.indexOf(i)
            this.scannerQrArr.splice(_index, 1)
          }
        })
        this.backendConfigData.qrcode.map((i, index) => {
          if (i < arrIndex) {
            this.backendQrArr.push(i)
          }
          if (i > arrIndex) {
            this.backendQrArr.push(i - 1)
          }
          if (i === arrIndex) {
            let _index = this.backendConfigData.qrcode.indexOf(i)
            this.backendQrArr.splice(_index, 1)
          }
        })
        // this.$set(this.jsonData.increase, this.increaseArr)
        this.$set(this.jsonData.scannerConfig, 'qrcode', this.scannerQrArr)
        this.$set(this.jsonData.backendConfig, 'qrcode', this.backendQrArr)
        this.jsonData.webConfig = JSON.parse(JSON.stringify(this.webConfigData))
        this.jsonData.increase = JSON.parse(JSON.stringify(this.increaseArr))
        this.$set(this.scannerConfigData, 'qrcode', this.scannerQrArr)
        this.$set(this.backendConfigData, 'qrcode', this.backendQrArr)
        this.webConfigData = JSON.parse(JSON.stringify(this.webConfigData))
        this.increaseConfigData = JSON.parse(JSON.stringify(this.increaseArr))
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch((err) => {
        console.log(err)
        console.log('error')
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    deleteWebconfig (row) {
      let index = this.webConfigData.indexOf(row)
      this.webConfigData.splice(index, 1)
    },
    addWebconfig () {
      this.webConfigData.push({
        value: [],
        status: false
      })
    },
    submitParamsConfig () {
      if (this.isEdit) {
        try {
          this.paramsconfigData[this.paramsIndex] = Object.assign({}, this.paramsconfigForm)
          let optionsArr = this.paramsconfigForm.options ? this.paramsconfigForm.options.split(',') : []
          this.$set(this.paramsconfigData[this.paramsIndex], 'options', optionsArr)
          for (let key in this.paramsconfigForm) {
            if (key === 'options') {
              this.$set(this.jsonData.paramsConfig[this.paramsIndex], key, optionsArr)
            } else if (key === 'flex' || key === 'increase') {
              this.$set(this.jsonData.paramsConfig[this.paramsIndex], key, parseInt(this.paramsconfigForm[key]))
            } else if (key === 'prefix') {
              this.$set(this.jsonData.paramsConfig[this.paramsIndex], key, !!this.paramsconfigForm[key])
            } else {
              this.$set(this.jsonData.paramsConfig[this.paramsIndex], key, this.paramsconfigForm[key])
            }
          }
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
      }
      if (!this.isEdit) {
        try {
          let paramsconfigAddData = JSON.parse(JSON.stringify(this.paramsconfigForm))
          let optionsArr = paramsconfigAddData.options ? paramsconfigAddData.options.split(',') : []
          this.$set(paramsconfigAddData, 'options', optionsArr)
          this.jsonData.paramsConfig.push(paramsconfigAddData)
          this.paramsconfigData.push(paramsconfigAddData)
          this.dialogFormVisible = false
          this.$nextTick(() => {
            this.$refs.tableDataRef.doLayout()
          })
          setTimeout(() => {
            this.$message({
              message: '新增params参数成功',
              type: 'success'
            })
          }, 500)
        } catch (err) {
          this.$message({
            message: '新增params参数失败',
            type: 'error'
          })
          console.log(err)
          console.log('err')
        }
      }
      this.webConfigData.forEach((element, index) => {
        this.increaseConfigData[index] = []
        let _this = this
        if (element.status) {
          element.value.forEach(i => {
            console.log(_this.paramsconfigData[i].increase)
            console.log(i)
            if (_this.paramsconfigData[i].increase === 1 || _this.paramsconfigData[i].increase === '1') {
              console.log(i)
              _this.increaseConfigData[index].push(i)
            }
          })
        }
      })
      for (let key in this.increaseConfigData) {
        this.$set(this.jsonData.increase, key, this.increaseConfigData[key])
      }
      console.log(this.increaseConfigData)
      console.log('this.webConfigData')
    },
    submitChannelConfig () {
      try {
        for (let key in this.channelsConfigData) {
          this.$set(this.jsonData.channelsConfig, key, this.channelsConfigData[key])
        }
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
      try {
        for (let key in this.scannerConfigData) {
          this.$set(this.jsonData.scannerConfig, key, this.scannerConfigData[key])
        }
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
        for (let key in this.backendConfigData) {
          this.$set(this.jsonData.backendConfig, key, this.backendConfigData[key])
        }
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
      try {
        this.jsonData.webConfig = JSON.parse(JSON.stringify(this.webConfigData))
        setTimeout(() => {
          this.$message({
            message: '修改webconfig参数成功',
            type: 'success'
          })
        }, 500)
        this.webConfigData.forEach((element, index) => {
        // console.log(element.value)
        // console.log('element')
        // if (element.status) {
        // // var increaseArr = []
          this.increaseConfigData[index] = []
          let _this = this
          if (element.status) {
            element.value.forEach(i => {
              console.log(_this.paramsconfigData[i].increase)
              console.log(i)
              if (_this.paramsconfigData[i].increase === 1 || _this.paramsconfigData[i].increase === '1') {
                console.log(i)
                _this.increaseConfigData[index].push(i)
              }
            // console.log(this.paramsconfigData[i].increase)
            })
          }
        })
        for (let key in this.increaseConfigData) {
          this.$set(this.jsonData.increase, key, this.increaseConfigData[key])
        }
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
    },
    exportJson () {
      console.log('exportJson')
      let data = JSON.stringify(this.jsonData, null, 4)
      fs.writeFileSync(this.path, data, (err) => {
        if (err) {
          console.log('err')
          console.log(err)
        } else {
          console.log('导出成功')
        }
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