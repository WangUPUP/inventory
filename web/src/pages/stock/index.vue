<template>
  <section>
    <el-card style="margin-bottom: 20px;">
      <!-- --------------------------------------------------------------- 查找区 ------------------------------------------------ -->
      <el-row :gutter="20" style="margin-bottom: 20px;">
        <el-form
          :model="searchForm"
          :inline="true"
          class="demo-form-inline"
          label-width="110px"
          ref="searchRef"
        >
          <el-form-item prop="time">
            <el-date-picker
              v-model="searchForm.time"
              type="daterange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
              value-format="timestamp"
              unlink-panels
            ></el-date-picker>
          </el-form-item>
          <el-form-item label="供货商" prop="keyword">
            <el-input placeholder="请输入供货商" v-model="searchForm.keyword" clearable></el-input>
          </el-form-item>
          <el-button type="primary" @click="search">查询</el-button>
          <el-button @click="resetForm('searchRef')">重置</el-button>
        </el-form>
      </el-row>
    </el-card>
    <el-card>
      <!-- --------------------------------------------------------------- 数据展示区 ------------------------------------------------ -->
      <el-row>
        <el-row type="flex" justify="space-around">
          <el-col :span="20">
            <div>数据列表</div>
          </el-col>
          <el-col :span="2">
            <el-button type="success" @click="showAddFormDialogVisible = true">新增库存</el-button>
          </el-col>
        </el-row>
        <el-table :data="stockList" border style="width: 100% " stripe class="tab">
          <el-table-column prop="Provider" label="供货商" align="center"></el-table-column>
          <el-table-column prop="Date" label="进货时间" align="center">
            <template slot-scope="scope">{{ scope.row.Date | formatDate }}</template>
          </el-table-column>
          <el-table-column prop="Category" label="品类" align="center"></el-table-column>
          <el-table-column prop="Brand" label="品牌" align="center"></el-table-column>
          <el-table-column prop="Model" label="型号" align="center"></el-table-column>
          <el-table-column prop="Price" label="进货价格(元)" align="center"></el-table-column>
          <el-table-column prop="Inventory" label="已出库(件)" align="center"></el-table-column>
          <el-table-column prop="Quantity" label="库存(件)" align="center"></el-table-column>
          <el-table-column prop="Remarks" label="备注" width="180px" align="center"></el-table-column>

          <!-- 功能按钮区域 -->
          <el-table-column width="200px" scope label="操作" align="center">
            <template slot-scope="scope">
              <!-- 修改按钮 -->
              <el-tooltip
                class="item"
                effect="dark"
                content="修改"
                placement="top"
                :enterable="false"
              >
                <el-button
                  icon="el-icon-setting"
                  size="small"
                  type="primary"
                  @click="showDditForm(scope.row)"
                ></el-button>
              </el-tooltip>
              <!-- 备注按钮 -->
              <el-tooltip
                class="item"
                effect="dark"
                content="添加备注"
                placement="top"
                :enterable="false"
              >
                <el-button
                  icon="el-icon-edit"
                  size="small"
                  type="success"
                  @click="showRemark(scope.row)"
                ></el-button>
              </el-tooltip>
              <!-- 出库按钮 -->
              <el-tooltip
                class="item"
                effect="dark"
                content="出库"
                placement="top"
                :enterable="false"
              >
                <el-button
                  icon="el-icon-s-promotion"
                  size="small"
                  type="warning"
                  @click="outStockFormDialogVisible = true"
                ></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>
      </el-row>
    </el-card>
    <!--------------------------------------------------------新增库存对话框-------------------------------------------------------->
    <el-dialog
      title="新增库存"
      :visible.sync="showAddFormDialogVisible"
      width="30%"
      :rules="addFormRules"
      @close="resetForm('addForm')"
    >
      <el-form ref="addForm" :model="addForm" label-width="120px" :rules="addFormRules">
        <el-form-item label="供应商" prop="Provider">
          <el-input v-model="addForm.Provider"></el-input>
        </el-form-item>
        <el-form-item label="时间" prop="Date">
          <el-date-picker
            v-model="addForm.Date"
            type="date"
            placeholder="选择日期"
            format="yyyy 年 MM 月 dd 日"
            value-format="timestamp"
          ></el-date-picker>
        </el-form-item>
        <el-form-item label="品类">
          <el-cascader
            v-model="addValue"
            :options="options"
            :props="{ value: 'Name', label: 'Name', children: 'Category', expandTrigger: 'hover' }"
            @change="addChangeRef"
            ref="cascader"
          ></el-cascader>
        </el-form-item>
        <el-form-item label="型号" prop="Model">
          <el-input v-model="addForm.Model"></el-input>
        </el-form-item>
        <el-form-item label="进货价格(元)" prop="Price">
          <el-input v-model="addForm.Price"></el-input>
        </el-form-item>
        <el-form-item label="出库数量(件)" prop="Inventory">
          <el-input-number v-model="addForm.Inventory" label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="库存数量(件)" prop="Quantity">
          <el-input-number v-model="addForm.Quantity" label="描述文字"></el-input-number>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="showAddFormDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="addStock">确定</el-button>
      </span>
    </el-dialog>

    <!--------------------------------------------------------修改库存对话框-------------------------------------------------------->
    <el-dialog
      title="修改库存"
      :visible.sync="showEditFormDialogVisible"
      width="30%"
      :rules="addFormRules"
      @close="resetForm('editForm')"
    >
      <el-form ref="editForm" :model="editForm" label-width="120px" :rules="addFormRules">
        <el-form-item label="供应商" prop="Provider">
          <el-input v-model="editForm.Provider"></el-input>
        </el-form-item>
        <el-form-item label="时间" prop="Date">
          <el-date-picker
            v-model="editForm.Date"
            type="date"
            placeholder="选择日期"
            format="yyyy 年 MM 月 dd 日"
            value-format="timestamp"
          ></el-date-picker>
        </el-form-item>
        <el-form-item label="品类">
          <el-cascader
            v-model="editValue"
            :options="options"
            :props="{ value: 'Name', label: 'Name', children: 'Category', expandTrigger: 'hover' }"
            @change="editChangeRef"
            ref="editCascader"
          ></el-cascader>
        </el-form-item>
        <el-form-item label="型号" prop="Model">
          <el-input v-model="editForm.Model"></el-input>
        </el-form-item>
        <el-form-item label="进货价格(元)" prop="Price">
          <el-input v-model="editForm.Price"></el-input>
        </el-form-item>
        <el-form-item label="出库数量(件)" prop="Quantity">
          <el-input-number v-model="editForm.Quantity" label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="库存数量(件)" prop="Inventory">
          <el-input-number v-model="editForm.Inventory" label="描述文字"></el-input-number>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="showEditFormDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="showEditFormDialogVisible = false">确认</el-button>
      </span>
    </el-dialog>

    <!--------------------------------------------------------添加备注对话框-------------------------------------------------------->
    <el-dialog
      title="修改库存"
      :visible.sync="showRemarkDialogVisible"
      width="30%"
      :rules="addFormRules"
      @close="resetForm('remarkForm')"
    >
      <el-form ref="remarkForm" :model="remarkForm" label-width="120px" :rules="addFormRules">
        <el-form-item label="备注 : " prop="Remarks">
          <el-input type="textarea" autosize v-model="remarkForm.Remarks" label="描述文字"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="showRemarkDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="addRemark">确认</el-button>
      </span>
    </el-dialog>
  </section>
</template>
<script>
import formatDate from '../../api/formatDate.js'
import datas from './datas.js'
import methods from './methdos.js'

export default {
  data() {
    return datas.init()
  },
  created() {
    this.getList(), this.getCateList()
  },
  methods: methods,
  filters: {
    formatDate: function(value) {
      // 时间戳转换日期格式方法
      if (value == null) {
        return ''
      } else {
        let date = new Date(value)
        let y = date.getFullYear() // 年
        let MM = date.getMonth() + 1 // 月
        MM = MM < 10 ? '0' + MM : MM
        let d = date.getDate() // 日
        d = d < 10 ? '0' + d : d
        return y + '-' + MM + '-' + d + ' '
      }
    }
  }
}
</script>

<style scoped>
.el-table {
  margin-top: 15px;
}
.el-col {
  margin: 0 20px;
}
.el-button {
  margin: 0 6px;
}

.coll {
  position: relative;
}
.btns {
  position: absolute;
  right: 520px;
  top: 29px;
  width: 300px;
}
</style>
