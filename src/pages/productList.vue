<template>
  <div class="basetable" element-loading-text="拼命加载中">
    <div class="selectMenu">
    <el-input
    class="search"
      placeholder="请输入商品名称进行搜索"
      v-model="input10"
      clearable>
    </el-input>
      <el-button type="primary" @click="search">搜索</el-button>
      <el-button type="primary" @click="add" class="add">新增</el-button>

      <el-dialog title="" :visible.sync="dialogFormVisible">
        <el-form :model="form">
          <el-form-item label="名称" :label-width="formLabelWidth">
            <el-input v-model="form.name" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="类目" :label-width="formLabelWidth">
            <el-select v-model="form.category" placeholder="请选择所属类目">
                <el-option
                  v-for="item in categorys"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="价格" :label-width="formLabelWidth">
            <el-input v-model="form.price" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="卖家电话"  :label-width="formLabelWidth">
            <el-input 
            v-model="form.phone" :minlength=11  autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="描述" :label-width="formLabelWidth">
            <el-input v-model="form.description" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="图片地址" :label-width="formLabelWidth">
             <el-upload
              ref="upload"
              :file-list="imgList"
              :action="actionPath"
              list-type="picture-card"
              :limit=3
              :data="postData"
              accept="image/jpeg,image/gif,image/png"
              :on-success="handleAvatarSuccess"
              :before-upload="beforeAvatarUpload"
              :on-remove="handleRemove"
             
              >
              <i class="el-icon-plus"></i>
            </el-upload>
            <el-dialog :visible.sync="dialogVisible">
              <img v-if="imageUrl" width="100%" :src="imageUrl" alt="">
            </el-dialog>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="cancel">取 消</el-button>
          <el-button type="primary" @click="addSure">确 定</el-button>
        </div>
      </el-dialog>

    </div>
    <div class="tableMain">
         <el-table :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)" height="500px" border style="width: 100%">
            <el-table-column prop="product_id" label="商品id">
            </el-table-column>
            <el-table-column prop="product_name" label="商品名称">
            </el-table-column>
            <el-table-column prop="picUrl" label="商品图片">
                <template scope="scope">
                     <img :src="scope.row.picUrl" width="80" height="80" />
                </template>
            </el-table-column>
            <el-table-column prop="product_price" label="商品单价">
            </el-table-column>
            <el-table-column prop="product_description" label="商品描述">
            </el-table-column>
            <el-table-column prop="seller_phone" label="卖家电话">
            </el-table-column>
            <el-table-column prop="category_type" label="商品类目">
            </el-table-column>
            <el-table-column prop="product_sales" label="总销量">
            </el-table-column>
            <el-table-column prop="update_time" label="创建时间">
            </el-table-column>
            <el-table-column label="操作" width="170">
                <template scope="scope">
                    <el-button size="small" @click="handleEdit(scope.$index, scope.row)">修改
                    </el-button>
                    <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除
                    </el-button>
                </template>
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
 import {genUpToken} from "../qiniuToken";
import axios from 'axios'
import { reformat } from '../common/reformartDate'
import config from '../../configFile.js'
import { setInterval, setTimeout } from 'timers';

export default {
  data() {
    return {
      imgList:[],
      dialogVisible: false,
      postData:{},
      actionPath:'http://upload.qiniup.com/',
      dialogFormVisible:false,
      imageUrl: '',
      input10:"",
      tableData:[],
      listInfo: [],
      formLabelWidth: '120px',
      categorys:[],
      form: {
             name: '',
             category: '',
             price:'',
             phone:'',
             description:'',
             picUrl:[]
      },
      currentPage: 1,
      currentIndex: '',
      pageSize:4,
      page:0,
      total:1000,
      changeProductId:''//要修改的商品的Id
    }
  },
  mounted() {
    this.getProductList()
    this.tableData = this.listInfo;
    this.getCategorys();
  },
  created() {
    var token;
      var policy = {};
      var bucketName = 'lingyunjunshi';
      var AK ='2XzB02eDUbBxbayGPpkGuHbXETUZBPoyDHkHsWQs';
      var SK = 'EcvNdT0sTPTohnIVsQ_wy-pjDHZ-9MXmqn42Vlsp';
      var deadline = Math.round(new Date().getTime() / 1000) + 3600;
      policy.scope = bucketName;
      policy.deadline = deadline;
      token=genUpToken(AK, SK, policy);
      this.postData.token=token;
     
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
    handleRemove(file, fileList) {
     
    },
    addSure(){
       let reg = /((\d{11})|^((\d{7,8})|(\d{4}|\d{3})-(\d{7,8})|(\d{4}|\d{3})-(\d{7,8})-(\d{4}|\d{3}|\d{2}|\d{1})|(\d{7,8})-(\d{4}|\d{3}|\d{2}|\d{1}))$)/;
          if(!reg.test(this.form.phone)) {
            alert('请填写正确的电话号格式');
            return
          }
          if(this.form.picUrl.length != 3) {
            alert('必须上传三张图片');
            return;
          }
          this.$refs['upload'].clearFiles();
          this.dialogFormVisible = false;
          
          let productInfo = this.form;

          if(this.changeProductId) {
            productInfo.productId = this.changeProductId;
          }
          console.log('productInfo', productInfo);
          axios.post(config.url + '/seller/product/save', productInfo)
          var that = this;
          setTimeout(function() {
              that.form.name = ''
              that.form.category=''
              that.form.price=''
              that.form.phone=''
              that.form.description=''
          }, 0)
          location.reload(true) 
    },
    getCategorys() {
       axios.get(config.url + '/seller/category')
        .then(res => {
          let data = res.data.data;
          for(let i = 0; i < data.length; i++) {
            this.categorys[i] = {};
            this.categorys[i].value = data[i].category_id;
            this.categorys[i].label = data[i].category_name;
          }
        
          console.log("%", JSON.stringify(this.categorys));
        }).catch(err => {console.log(err)})
      
    },
      getProductList() {
        var that = this;
        axios.get(config.url + '/seller/product/list')
        .then(res => {
          let data = res.data.data;
         
          for(let i = 0; i < data.length; i++) {
            data[i].update_time = this.convertUTCTimeToLocalTime(data[i].update_time)
            that.listInfo[i]={};
            that.listInfo[i] = data[i]
          }
          console.log('data', this.listInfo);
          that.page++;   
        }).catch(err => {console.log(err)})
      },
      handleAvatarSuccess(res, file) {
    
        this.imageUrl='http://lyjs.wangtingting.top/'+res.key
        this.form.picUrl.push(this.imageUrl);
      },
      beforeAvatarUpload(file) {
        const isLt2M = file.size / 1024 / 1024 < 2;

        if (!isLt2M) {
          this.$message.error('上传头像图片大小不能超过 2MB!');
        }
        return  isLt2M;
      }, 
    add() {
        this.form.name = ''
        this.form.category=''
        this.form.price=''
        this.form.phone=''
        this.form.description=''
        this.changeProductId = '';
        this.form.picUrl=[];
        this.dialogFormVisible = true

    },

    search() {
      var search = this.listInfo.filter(order => {
          if(order.product_name == this.input10.trim()) {
            return order;
          }
      })
        this.tableData = search;
    },
    update() {
      this.form.date = reformat(this.form.date)
      this.tableData.push(this.form)
      this.dialogFormVisible = false
    },
    handleEdit(index, row) {
      
      this.changeProductId = row.product_id;
      this.form.name = row.product_name;
      this.form.description = row.product_description;
      this.form.price = row.product_price;
     // this.form.picUrl = row.product_icon;
      this.form.category = row.category_type;
      console.log("%%%%%%%", this.form.category);
      this.form.phone = row.seller_phone;
      
      
      //console.log(product_id);
        //alert(this.form);
      // this.currentIndex = index
       this.dialogFormVisible = true
      //alert(index);
      //
    },
    handleDelete(index, row) {
      
      this.changeProductId = row.product_id;
      console.log("index", this.changeProductId);
     // console.log('row', JSON.stringify(row));
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {

        this.tableData.splice(index, row)
        axios.post(config.url +  '/seller/product/delete', {
          productId:this.changeProductId
        })
        location.reload(true) 
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
    cancel() {
      this.form.name = ''
      this.form.category=''
      this.form.price=''
      this.form.phone=''
      this.form.description=''
      this.$refs['upload'].clearFiles();
      this.dialogFormVisible = false
    },
    handleSizeChange() {
      this.pagesize = this.pageSize;
    },
    handleCurrentChange:function(currentPage){
        this.currentPage = currentPage;
    }
  },
}
</script>
<style lang="scss">
.avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
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
