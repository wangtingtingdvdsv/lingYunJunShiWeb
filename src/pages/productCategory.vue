<template>
  <div class="basetable"  element-loading-text="拼命加载中">
    <div class="selectMenu">
    <el-input
    class="search"
      placeholder="请输入姓名进行搜索"
      v-model="input10"
      clearable>
    </el-input>
      <el-button type="primary" @click="search">搜索</el-button>
      <el-button type="primary" @click="add" class="add">新增</el-button>
      <el-dialog title="" :visible.sync="dialogFormVisible">
        <el-form :model="form">
          <el-form-item label="商品名称" :label-width="formLabelWidth">
            <el-input v-model="form.name" autocomplete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="addSure">确 定</el-button>
        </div>
      </el-dialog>
    </div>
    <div class="tableMain">
         <el-table :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)" height="450px" border style="width: 100%">
            <el-table-column prop="category_id" label="类目id">
            </el-table-column>
            <el-table-column prop="category_name" label="商品名称">
            </el-table-column>
            <el-table-column prop="update_time" label="创建时间">
            </el-table-column>
        </el-table>
    </div>
    <div class="page">
      <el-pagination 
          @size-change="handleSizeChange" 
          @current-change="handleCurrentChange" 
          :current-page="page" 
          :page-size='pageSize' 
          layout="prev, pager, next, jumper" 
          :total="tableData.length">
      </el-pagination>
    </div>

  </div>
</template>

<script type="text/ecmascript-6">
import { reformat } from '../common/reformartDate'
import axios from 'axios'
import config from '../../configFile.js'
export default {
  data() {
    return {
      loading: true,
      dialogFormVisible:false,
      form: {
          name: '',
          // category: '',
          // price:'',
          // phone:'',
          // description:'',
          // picUrl:''
        },
        formLabelWidth: '120px',
    categoryInfo:[],
      currentPage: 1,
      currentIndex: '',
      pageSize:5,
      total:1000,
      input10:"",
      tableData: [],
      page:0
    }
  },
 mounted() {
    this.getCategorys()
    this.tableData = this.categoryInfo;
  },
  methods: {
    convertUTCTimeToLocalTime: function (UTCDateString) {
            if(!UTCDateString){
              return '-';
            }
            function formatFunc(str) {    //格式化显示
              return str > 9 ? str : '0' + str
            }
            var date2 = new Date(UTCDateString);     //这步是关键
            var year = date2.getFullYear();
            var mon = formatFunc(date2.getMonth() + 1);
            var day = formatFunc(date2.getDate());
            var hour = date2.getHours();

            hour = formatFunc(hour);
            var min = formatFunc(date2.getMinutes());
            var dateStr = year+'-'+mon+'-'+day+' ' +' '+hour+':'+min;
            return dateStr;
        },
      addSure() {
          this.dialogFormVisible = false
          console.log(this.form.name);
          axios.post(config.url+'/seller/category/save', {
            categoryName:this.form.name
          }).then(function() {
            location.reload(true) 
          })
          
      },
      getCategorys() {
        var that = this;
        axios.get(config.url +'/seller/category')
        .then(res => {
          let data = res.data.data;
          for(let i = 0; i < data.length; i++) {
            data[i].update_time = this.convertUTCTimeToLocalTime(data[i].update_time)
            that.categoryInfo[i]={};
            that.categoryInfo[i] = data[i]
          }
          console.log("categoryInfo", this.categoryInfo);
          that.page++;   
        }).catch(err => {console.log(err)})
      },
    add(){
      this.dialogFormVisible = true
      

    },
    showTime() {
      this.$alert(this.value6, '起止时间', {
        confirmButtonText: '确定',
        callback: action => {
          this.$message({
            type: 'info',
            message: '已显示'
          })
        }
      })
    },
    search() {
      var search = this.categoryInfo.filter(order => {
          if(order.category_name == this.input10.trim()) {
            return order;
          }
      })
        this.tableData = search;
    },
    handleSizeChange(size) {
      this.pagesize = size;
    },
    handleCurrentChange:function(currentPage){
        this.currentPage = currentPage;
    }
  },
}
</script>
<style lang="scss">
.basetable {
  .tableMain {
    margin: {
      top: 10px;
    }
  }
  .page {
    float: right;
    margin: {
      top: 10px;
    }
  }
}
.search{
  display: inline-block;
  width: 40%;
}
.add{
  float: right;
  margin-right: 40px;
}
</style>
