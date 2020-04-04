<template>
  <div>
    <el-dialog title="添加信息" :visible.sync="dialogTableVisible" :before-close="handleClose" width="50%">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="日期">
          <el-input v-model="form.date"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="form.name" ></el-input>
        </el-form-item>
        <el-form-item label="省份">
          <el-input v-model="form.province"></el-input>
        </el-form-item>
        <el-form-item label="城市">
          <el-input v-model="form.city"></el-input>
        </el-form-item>
        <el-form-item label="地址">
          <el-input v-model="form.address"></el-input>
        </el-form-item>
        <el-form-item label="邮编">
          <el-input v-model="form.zip"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="handleCancel">取消</el-button>
        <el-button @click="handleSubmit">确定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  props: ['dialogTableVisible'],
  data () {
    return {
      message: '来自子组件的消息',
      form: {
        date: '',
        name: '',
        province: '',
        city: '',
        address: '',
        zip: ''
      }
    }
  },
  methods: {
    handleCancel () {
      this.$emit('cActive') // $emit应是用来子组件向父组件传参的,但是,这里我只是想改变父组件中isActive为false,
      // 对应事件cActive
    },
    handleSubmit () {
      this.$emit('cAdd', this.form)
      // 对应事件cAdd
      // &emit向父组件提交form表单
    },
    childaddClick () {
      this.form.date = ''
      this.form.name = ''
      this.form.province = ''
      this.form.city = ''
      this.form.address = ''
      this.form.zip = ''
    },
    handleClose (done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          this.$emit('cActive') // 如果确认,就取消弹窗,
          done()
        })
        .catch(_ => {})
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
