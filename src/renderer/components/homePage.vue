<template>
    <div class="main-page">
         <!-- <vue-json-editor 
         lang="zh"   
         v-model="json" 
         :show-btns="true" 
         :expandedOnStart="true" 
         @json-change="onJsonChange">
         </vue-json-editor> -->
        <div style="margin-bottom: 10px;">
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
            <!-- <el-button size="small" type="primary" @click="importJson">导入</el-button> -->
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
                <!-- paramsconfig -->
               <el-table
                :data="tableData"
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
              </el-table>
                    <!-- <el-tree
                    :data="data"
                    node-key="id"
                    default-expand-all
                    :expand-on-click-node="false">
                    <span class="custom-tree-node" slot-scope="{ node, data }">
                        <span>
                            <el-input size="small" placeholder="key"></el-input>
                        </span>
                        <span>
                            <el-input size="small" placeholder="value"></el-input>
                        </span>
                        <span>
                            <el-button
                                type="text"
                                size="mini"
                                @click="() => append(data)">
                                新增
                            </el-button>
                            <el-button
                                type="text"
                                size="mini"
                                @click="() => remove(node, data)">
                                删除
                            </el-button>
                        </span>
                    </span>
                    </el-tree> -->
            </div>
        </div>
    </div>
</template>
<script>
import JsonViewer from 'vue-json-viewer'
const fs = require('fs')

let id = 1000
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
      paramsconfigData: [{
        key: 1,
        name: 'aiyaya'
      }],
      fileList: [],
      tableData: [],
      data: JSON.parse(JSON.stringify(data))
    }
  },
  components: {
    JsonViewer
  },
  methods: {
    onJsonChange (value) {
      console.log('value:', value)
    },
    append (data) {
      console.log(data.$treeNodeId)
      const newChild = { id: id++, label: 'testtest', children: [] }
      if (!data.children) {
        this.$set(data, 'children', [])
      }
      data.children.push(newChild)
    },

    remove (node, data) {
      const parent = node.parent
      const children = parent.data.children || parent.data
      const index = children.findIndex(d => d.id === data.id)
      children.splice(index, 1)
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
    //   fs.readFile(path, function (err, data) {
    //     if (err) {
    //       return console.error(err)
    //     }
    //     _this.jsonData = data.toString()
    //     console.log('异步读取: ' + data.toString())
    //   })
    // }
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
</style>