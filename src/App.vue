<template>
  <div>
    <div>
      <el-row :gutter="20">
        <el-col :span="16">
          <el-input placeholder="请输入内容" v-model="searchdata" @clear="handleClear" clearable></el-input>
        </el-col>
        <el-col :span="8">
          <el-button @click="handleSearch">搜索</el-button>
          <el-button @click="addClick">添加</el-button>
        </el-col>
      </el-row>
    </div>
    <el-table
      :data="tableData"
      style="width: 100%"
      max-height="550"
      v-if = isShow>
      <el-table-column
        fixed
        prop="date"
        label="日期"
        width="150">
      </el-table-column>
      <el-table-column
        prop="name"
        label="姓名"
        width="120">
      </el-table-column>
      <el-table-column
        prop="province"
        label="省份"
        width="120">
      </el-table-column>
      <el-table-column
        prop="city"
        label="市区"
        width="120">
      </el-table-column>
      <el-table-column
        prop="address"
        label="地址"
        width="300">
      </el-table-column>
      <el-table-column
        prop="zip"
        label="邮编"
        width="120">
      </el-table-column>
      <el-table-column
        fixed="right"
        label="操作"
        width="220">
        <template slot-scope="scope">
          <el-button
            @click.native.prevent="handleDelete(scope.$index, tableData)"
            type="button"
            size="small">
            移除
          </el-button>
          <el-button
            @click.native.prevent="modifyClick(scope.$index, tableData)"
            type="button"
            size="small"
          >
            修改
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <add-item :dialog-table-visible="isActive" @cActive="changeActive" @cAdd="handleAdd" ref="myaddchild"></add-item>
    <!--    @cActive控制添加表单的显示,每当点击取消时,不再显示弹窗-->
    <!--    @cAdd控制提交form表单的信息,点击确定时,子组件向父组件传递表单数据,同时isActive=false,不再显示弹窗-->
    <modify-item :dialog-table-visiblemodify="isActive_modify" :index_from_parent="index_modify"
                 @cActive_modify="changeActive_modify" @cmodify="handleRewrite"></modify-item>
  </div>
</template>

<script>
import additem from './components/additem'
import modifyitem from './components/modifyitem'

export default {
  components: {
    'add-item': additem,
    'modify-item': modifyitem
  },
  data () {
    return {
      message: '',
      isActive: false,
      isActive_modify: false,
      index_modify: 0,
      isShow: true,
      searchdata: '',
      tableData: [{
        date: '2016-05-01',
        name: '刘小鹿',
        province: '上海',
        city: '普陀区',
        address: '上海市普陀区金沙江路 1551 弄',
        zip: '23333'
      }, {
        date: '2016-05-02',
        name: '王小虎',
        province: '上海',
        city: '普陀区',
        address: '上海市普陀区金沙江路 1518 弄',
        zip: '200333'
      }
      ],
      searchList: [],
      tempList: []
    }
  },
  methods: {
    addClick () { // 每次点击“添加”按钮时,首先将isActive激活,显示表单;再调用子组件的childaddClicj方法,清空之前子组件中的form表单
      this.isActive = true // 显示弹窗
      this.$refs.myaddchild.childaddClick() // 调用子组件中的childaddClick方法,清空表单
    },
    modifyClick (index, rows) {
      this.isActive_modify = true // 显示修改弹窗
      this.index_modify = index
    },
    handleDelete (index, rows) {
      for (var i = 0; i < this.tempList.length; i++) { // 因为后来要实现一个搜索功能,但搜索出来的结果也要实现删除功能,所以tempList和tableData要实现同步删除
        if (this.tempList[i].name === rows[index].name) {
          this.tempList.splice(i, 1)
        }
      }
      rows.splice(index, 1)
    },
    handleRewrite (form) {
      this.isActive_modify = false // 显示修改弹窗
      // eslint-disable-next-line no-unused-vars
      this.tableData[this.index_modify].date = form.date
      this.tableData[this.index_modify].name = form.name
      this.tableData[this.index_modify].province = form.province
      this.tableData[this.index_modify].city = form.city
      this.tableData[this.index_modify].address = form.address
      this.tableData[this.index_modify].zip = form.zip
      this.isActive_modify = false
      this.tempList = this.tableData
    },
    handleAdd (form) {
      const obj = {
        date: form.date,
        name: form.name,
        province: form.province,
        city: form.city,
        address: form.address,
        zip: form.zip
      } // 这里用临时变量存储子组件提交来的form表单的数据,而不能直接push子组件的form,因为那样做会导致是将form添加到了tableDate中,每次push都只是
      // 增加了同一个form(个数有多个),修改一次form,其他的数据也会改变
      this.tableData.push(obj)
      this.tempList = this.tableData
      this.isActive = false // 关闭显示弹窗
    },
    handleSearch () {
      this.searchList = [] // 每次搜索,要将上次的搜索结果searchList清空
      for (var i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].date.includes(this.searchdata) ||
          this.tableData[i].name.includes(this.searchdata) ||
          this.tableData[i].province.includes(this.searchdata) ||
          this.tableData[i].city.includes(this.searchdata) ||
          this.tableData[i].address.includes(this.searchdata) ||
          this.tableData[i].zip.includes(this.searchdata)) {
          this.searchList.push(this.tableData[i])
        }
      }
      this.tempList = this.tableData // 用tempList暂存tableDate,即搜索前的数据
      this.tableData = this.searchList
    },
    handleClear () {
      this.tableData = this.tempList
    },
    changeActive () { // 用于只改变isActive的值来取消显示弹窗
      this.isActive = false
    },
    changeActive_modify () {
      this.isActive_modify = false
    }
  }
}
</script>

<style>

</style>
