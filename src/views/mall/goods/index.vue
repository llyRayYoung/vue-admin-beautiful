<template>
  <div class="goods-container">
    <byui-query-form>
      <byui-query-form-right-panel :span="24">
        <el-form
          ref="form"
          :model="queryForm"
          :inline="true"
          @submit.native.prevent
        >
          <el-form-item>
            <el-input v-model="queryForm.title" placeholder="商品名称" />
          </el-form-item>
          <el-form-item>
            <el-button
              icon="el-icon-search"
              type="primary"
              native-type="submit"
              @click="handleQuery"
              >查询
            </el-button>
          </el-form-item>
        </el-form>
      </byui-query-form-right-panel>
    </byui-query-form>
    <el-row :gutter="15">
      <el-col
        v-for="(item, index) in list"
        :key="index"
        :xs="24"
        :sm="8"
        :md="8"
        :lg="8"
        :xl="6"
      >
        <el-card :body-style="{ padding: '0px' }" shadow="hover">
          <div class="goods-card-body">
            <div class="goods-tag-group">
              <el-tag v-if="item.isRecommend" hit type="success">推荐</el-tag>
              <el-tag v-if="item.status === 0" hit type="danger">缺货</el-tag>
            </div>
            <div class="goods-image-group">
              <img :src="item.image" class="goods-image" />
            </div>
            <div class="goods-title">{{ item.title }}</div>
            <div class="goods-description">{{ item.description }}</div>
            <div class="goods-price">
              <span>¥ {{ item.price }} 元</span>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>
    <el-pagination
      :background="background"
      :current-page="queryForm.pageNo"
      :layout="layout"
      :page-size="queryForm.pageSize"
      :total="total"
      @current-change="handleCurrentChange"
      @size-change="handleSizeChange"
    ></el-pagination>
  </div>
</template>

<script>
import { getList } from "@/api/goods";

export default {
  name: "Goods",
  components: {},
  data() {
    return {
      queryForm: {
        pageNo: 1,
        pageSize: 20,
        title: "",
      },
      list: null,
      listLoading: true,
      pageNo: 1,
      pageSize: 10,
      layout: "total, sizes, prev, pager, next, jumper",
      total: 0,
      background: true,
      height: 0,
      elementLoadingText: "正在加载...",
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    handleSizeChange(val) {
      this.pageSize = val;
      this.fetchData();
    },
    handleCurrentChange(val) {
      this.pageNo = val;
      this.fetchData();
    },
    handleQuery() {
      this.queryForm.pageNo = 1;
      this.fetchData();
    },
    fetchData() {
      this.listLoading = true;
      getList(this.queryForm).then((res) => {
        this.list = res.data;
        this.total = res.totalCount;
        setTimeout(() => {
          this.listLoading = false;
        }, 300);
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.goods-container {
  .goods-card-body {
    position: relative;
    text-align: center;
    cursor: pointer;

    .goods-tag-group {
      position: absolute;
      top: 10px;
      right: 5px;
      z-index: 9;
    }

    .goods-image-group {
      height: 400px;
      overflow: hidden;

      .goods-image {
        width: 100%;
        height: 400px;
        transition: all ease-in-out 0.3s;

        &:hover {
          transform: scale(1.1);
        }
      }
    }

    .goods-title {
      margin: 8px 0;
      font-size: 16px;
      font-weight: bold;
    }

    .goods-description {
      font-size: 14px;
      color: #808695;
    }

    .goods-price {
      margin: 8px 0;
      font-size: 14px;
      color: $base-color-orange;

      s {
        color: #c5c8ce;
      }
    }
  }
}
</style>
