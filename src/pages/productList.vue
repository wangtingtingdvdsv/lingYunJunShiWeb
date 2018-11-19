<template>
  <div class="basetable" v-loading="loading" element-loading-text="拼命加载中">
    <div class="selectMenu">
    <el-input
    class="search"
      placeholder="请输入商品名称进行搜索"
      v-model="input10"
      clearable>
    </el-input>
      <el-button type="primary" @click="search">搜索</el-button>
      <el-button type="primary" @click="add" class="add">新增</el-button>
      <el-button type="primary" @click="modify" class="modify">修改</el-button>
       <el-dialog title="" :visible.sync="dialogFormVisible">
        <el-form :model="form">
          <el-form-item label="商品名称" :label-width="formLabelWidth">
            <el-input v-model="form.name" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="类目" :label-width="formLabelWidth">
            <el-select v-model="form.category" placeholder="请选择所属类目">
              <el-option label="化妆品 " value=" 化妆品"></el-option>
              <el-option label="创意优品" value="最热"></el-option>
              <el-option label="女生最爱" value="女生最爱"></el-option>
              <el-option label="难吃的菜" value="难吃的菜"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="价格" :label-width="formLabelWidth">
            <el-input v-model="form.price" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="卖家电话" :label-width="formLabelWidth">
            <el-input v-model="form.phone" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="描述" :label-width="formLabelWidth">
            <el-input v-model="form.description" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="图片url地址" :label-width="formLabelWidth">
            <el-input v-model="form.picUrl" autocomplete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
        </div>
      </el-dialog>
    </div>
    <div class="tableMain">
         <el-table :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)" height="100%" border style="width: 100%">
            <el-table-column prop="productId" label="商品id">
            </el-table-column>
            <el-table-column prop="productName" label="商品名称">
            </el-table-column>
            <el-table-column prop="productPic" label="商品图片">
                <template scope="scope">
                     <img :src="scope.row.productPic" width="80" height="80" />
                </template>
            </el-table-column>
            <el-table-column prop="productPrice" label="商品单价">
            </el-table-column>
            <el-table-column prop="productDescription" label="商品描述">
            </el-table-column>
            <el-table-column prop="sellerPhone" label="卖家电话">
            </el-table-column>
            <el-table-column prop="productCategory" label="商品类目">
            </el-table-column>
            <el-table-column prop="totalSales" label="总销量">
            </el-table-column>
            <el-table-column prop="creationTime" label="创建时间">
            </el-table-column>
            <el-table-column prop="creationTime" label="修改时间">
            </el-table-column>
            <el-table-column label="操作" width="150">
                <template scope="scope">
                    <el-button size="small" @click="handleEdit(scope.$index, scope.row)">修改
                    </el-button>
                    <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">下架
                    </el-button>
                </template>
            </el-table-column>
        </el-table>
    </div>
    <div class="page">
      <el-pagination 
          @size-change="handleSizeChange" 
          @current-change="handleCurrentChange" 
          :current-page="currentPage" 
          :page-size='pageSize' 
          layout="prev, pager, next, jumper" 
          :total="tableData.length">
      </el-pagination>
    </div>

  </div>
</template>

<script type="text/ecmascript-6">
import { reformat } from '../common/reformartDate'
import $ from 'jquery'
export default {
  data() {
    return {
      loading: true,
      input10:"",
      tableData:'',
      listInfo: [{
          productId:'180989312',
          productName:'鸡捞',
          productPic:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533447087301&di=cdf84d3a62b05919acec50a16f194813&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F17%2F35%2F63%2F14f58PICigZ_1024.jpg ',
          productPrice:'56.8',
          productDescription:'好吃，好吃，真好吃',
          sellerPhone:'18710959261',
          productCategory:'4',
          totalSales:'233',
          deliveryTime:'2018-9-19 22:00:12',
          creationTime:'2018-9-19 21:25:45'
      },{
          productId:'180989312',
          productName:'鸡捞面',
          productPic:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533447087301&di=cdf84d3a62b05919acec50a16f194813&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F17%2F35%2F63%2F14f58PICigZ_1024.jpg ',
          productPrice:'56.8',
          productDescription:'好吃，好吃，真好吃',
          sellerPhone:'18710959261',
          productCategory:'4',
          totalSales:'233',
          deliveryTime:'2018-9-19 22:00:12',
          creationTime:'2018-9-19 21:25:45'
      },{
          productId:'180989312',
          productName:'鸡捞面',
          productPic:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533447087301&di=cdf84d3a62b05919acec50a16f194813&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F17%2F35%2F63%2F14f58PICigZ_1024.jpg ',
          productPrice:'56.8',
          productDescription:'好吃，好吃，真好吃',
          sellerPhone:'18710959261',
          productCategory:'4',
          totalSales:'233',
          deliveryTime:'2018-9-19 22:00:12',
          creationTime:'2018-9-19 21:25:45'
      },{
          productId:'180989312',
          productName:'鸡捞面',
          productPic:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533447087301&di=cdf84d3a62b05919acec50a16f194813&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F17%2F35%2F63%2F14f58PICigZ_1024.jpg ',
          productPrice:'56.8',
          productDescription:'好吃，好吃，真好吃',
          sellerPhone:'18710959261',
          productCategory:'4',
          totalSales:'233',
          deliveryTime:'2018-9-19 22:00:12',
          creationTime:'2018-9-19 21:25:45'
      },
      {
          productId:'180989312',
          productName:'鸡捞面',
          productPic:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533447087301&di=cdf84d3a62b05919acec50a16f194813&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F17%2F35%2F63%2F14f58PICigZ_1024.jpg ',
          productPrice:'56.8',
          productDescription:'好吃，好吃，真好吃',
          sellerPhone:'18710959261',
          productCategory:'4',
          totalSales:'233',
          deliveryTime:'2018-9-19 22:00:12',
          creationTime:'2018-9-19 21:25:45'
      },{
          productId:'180989312',
          productName:'鸡捞面',
          productPic:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533447087301&di=cdf84d3a62b05919acec50a16f194813&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F17%2F35%2F63%2F14f58PICigZ_1024.jpg ',
          productPrice:'56.8',
          productDescription:'好吃，好吃，真好吃',
          sellerPhone:'18710959261',
          productCategory:'4',
          totalSales:'233',
          deliveryTime:'2018-9-19 22:00:12',
          creationTime:'2018-9-19 21:25:45'
      },{
          productId:'180989312',
          productName:'鸡捞面',
          productPic:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533447087301&di=cdf84d3a62b05919acec50a16f194813&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F17%2F35%2F63%2F14f58PICigZ_1024.jpg ',
          productPrice:'56.8',
          productDescription:'好吃，好吃，真好吃',
          sellerPhone:'18710959261',
          productCategory:'4',
          totalSales:'233',
          deliveryTime:'2018-9-19 22:00:12',
          creationTime:'2018-9-19 21:25:45'
      },{
          productId:'180989312',
          productName:'王婷',
          productPic:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533447087301&di=cdf84d3a62b05919acec50a16f194813&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F17%2F35%2F63%2F14f58PICigZ_1024.jpg ',
          productPrice:'56.8',
          productDescription:'好吃，好吃，真好吃',
          sellerPhone:'18710959261',
          productCategory:'4',
          totalSales:'233',
          deliveryTime:'2018-9-19 22:00:12',
          creationTime:'2018-9-19 21:25:45'
      }
      ],


      formLabelWidth: '80px',
      form: {},
      value6: '',
      currentPage: 1,
      currentIndex: '',
      pageSize:4,
      total:1000,
      dialogFormVisible:false
    }
  },
  mounted() {
      $.ajax({
        url: 'http://localhost:9008/seller/product/list',
        type: 'get',
        data:{
          page:2,
          size:3
        },
        //               xhrFields: {
        //     withCredentials: true
        // },
        success: function (data) {
            console.log("-----------------data", data);
        },
        error: function (xhr, status, error) {
            console.log('Error: ' + error.message);
        },
      });
  },
  created() {
    setTimeout(() => {
      this.loading = false
    }, 1500)
    this.tableData = this.listInfo;
  },
  methods: {
    add() {
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
      var search = this.listInfo.filter(order => {
          if(order.productName == this.input10.trim()) {
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
      this.form = this.tableData[index]
      this.currentIndex = index
      this.dialogFormVisible = true
    },
    handleDelete(index, row) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.tableData.splice(index, 1)
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
      this.dialogFormVisible = false
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
.modify{
  float: right;
  margin-right: 40px;
}
</style>
