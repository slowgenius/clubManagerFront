<template>
  <div>
    <div class="top">
    </div>
    <el-button @click="add">新增</el-button>
    <el-table :data="clubList">
      <el-table-column label="编号" width="120" align="center">
        <template slot-scope="scope">{{scope.row.id}}</template>
      </el-table-column>
      <el-table-column label="俱乐部名称" width="120" align="center">
        <template slot-scope="scope">{{scope.row.name}}</template>
      </el-table-column>
      <el-table-column label="俱乐部名称" width="120" align="center">
        <template slot-scope="scope">{{scope.row.principal}}</template>
      </el-table-column>
      <el-table-column label="创建时间" width="120" align="center">
        <template slot-scope="scope">{{scope.row.createTime|dateFormat}}</template>
      </el-table-column>
      <el-table-column label="类型" width="120" align="center">
        <template slot-scope="scope">{{scope.row.type|typeFormat}}</template>
      </el-table-column>
      <el-table-column label="活动次数" width="120" align="center">
        <template slot-scope="scope">{{scope.row.activityTimes}}</template>
      </el-table-column>
      <el-table-column label="会员数量" width="120" align="center">
        <template slot-scope="scope">{{scope.row.vipCount}}</template>
      </el-table-column>
      <el-table-column label="备注" width="120" align="center">
        <template slot-scope="scope">{{scope.row.remark}}</template>
      </el-table-column>

      <el-table-column label="操作" width="240" align="center">
        <template slot-scope="scope">
          <el-button size="mini"
                     type="text"
                     @click="handleUpdate(scope.row)">
            编辑
          </el-button>
          <el-button size="mini"
                     type="text"
                     @click="handleDelete(scope.row.id)">删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog
      title="添加"
      :visible.sync="dialogVisible"
      width="30%">
      <el-form :model="club"  label-width="100px">
        <el-form-item label="俱乐部名称"  :rules="[
      { required: true, message: '请输入俱乐部信息', trigger: 'blur' }
    ]">
          <el-input v-model="club.name" placeholder="请输入俱乐部信息"></el-input>
        </el-form-item>
        <el-form-item label="负责人"  :rules="[
      { required: true, message: '请输入俱乐部信息', trigger: 'blur' }
    ]">
          <el-input v-model="club.principal" placeholder="请输入负责人姓名"></el-input>
        </el-form-item>
        <el-form-item label="创建时间" :rules="[
      { type: 'date' ,required: true, message: '请输入俱乐部信息', trigger: 'change' }
    ]">
          <el-date-picker
            v-model="club.createTime"
            type="date"
            placeholder="选择日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="类型" :rules="[
      { required: true, message: '请输入俱乐部信息', trigger: 'change' }
    ]">
          <el-select v-model="club.type" placeholder="请选择类型" >
            <el-option
              v-for="item in types"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="活动次数"  :rules="[
      { type : 'number',required: true, message: '请输入正确活动次数', trigger: 'blur' }
    ]">
          <el-input v-model.number="club.activityTimes" placeholder="请输入活动次数"></el-input>
        </el-form-item>
        <el-form-item label="会员数量"  :rules="[
      {  type : 'number', required: true, message: '请输入正确会员数量', trigger: 'blur' }
    ]">
          <el-input v-model.number="club.vipCount" placeholder="请输入会员数量"></el-input>
        </el-form-item>
        <el-form-item label="备注" >
          <el-input v-model="club.remark" placeholder="请输入备注"></el-input>
        </el-form-item>
      </el-form>
      <el-button @click="cancle">取 消</el-button>
      <el-button type="primary" @click="saveOrUpdate">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        status: 0, //1:新增 2:更新
        dialogVisible: false,
        clubList: [],
        club: {
          id: '',
          name: '',
          principal: '',
          createTime: null,
          type: "",
          activityTimes: "",
          vipCount: "",
          remark: ''
        },
        oldClub: {},
        types: [{
          value: '1',
          label: '足球'
        }, {
          value: '2',
          label: '羽毛球'
        }, {
          value: '3',
          label: '篮球'
        }, {
          value: '4',
          label: '乒乓球'
        }]
      }

    },
    created() {
      this.getList();
    },
    // filters: {
    //   formatTime(time) {
    //     let date = new Date(time);
    //     console.log(new Date(time));
    //     if (time) {
    //       return this.formatDate(date, 'yyyy-MM-dd hh:mm:ss');
    //     }
    //   }
    // },
    methods: {
      cancle() {
        this.dialogVisible = false;
        this.club = {};
        this.getList();
      },
      add() {
        this.club = {}
        this.dialogVisible = true;
        this.status = 1
      },
      save() {
        let that = this;
        let time = new Date(that.club.createTime);
        console.log(that.club)
        this.$axios.post('http://localhost:8081/club/save?id=' + that.club.id + '&name=' + that.club.name + '&principal=' + that.club.principal + '&createTime=' + time.getTime() + '&type=' + that.club.type + '&activityTimes=' + that.club.activityTimes + '&vipCount=' + that.club.vipCount + '&remark=' + that.club.remark).then(function (response) {
          if (response.data === -1) {
            that.$message('俱乐部名称已经被使用');
          } else {
            that.getList();
            that.club = {}
            that.dialogVisible = false;
            that.status = 0
          }
        }).catch(function (error) {
          console.log(error);
        })

      },
      update() {
        let that = this;
        let time = new Date(that.club.createTime);
        let type = that.reConvertType(that.club.type);
        this.$axios.post('http://localhost:8081/club/update?id=' + that.club.id + '&name=' + that.club.name + '&principal=' + that.club.principal + '&createTime=' + time.getTime() + '&type=' + type + '&activityTimes=' + that.club.activityTimes + '&vipCount=' + that.club.vipCount + '&remark=' + that.club.remark).then(function (response) {
          if (response.data === -1) {
            that.$message('俱乐部名称已经被使用');
          } else {
            that.getList();
            that.club = {}
            that.dialogVisible = false;
            that.status = 0
          }
        }).catch(function (error) {
          console.log(error);
        })

      },
      getList() {
        let that = this;
        this.$axios.post('http://localhost:8081/club/queryList', {}).then(function (response) {
          that.clubList = response.data
        }).catch(function (error) {
          console.log(error);
        })

      },
      handleUpdate(row) {
        this.dialogVisible = true;
        this.status = 2;
        this.club = row.valueOf();
        this.club.type = this.convertType(row.type)
        console.log(this.club)
      },
      handleDelete(id) {
        let that = this;
        this.$axios.post('http://localhost:8081/club/delete?id=' + id).then(function (response) {
          that.getList();
        }).catch(function (error) {
          console.log(response);
        })
      },
      saveOrUpdate() {
        let that = this;
        if (that.verrify(that.club)) {
          if (that.status === 1) {
            that.save();
          } else {
            that.update();
          }
        }
      },
      convertType(type) {
        if (type === 1) {
          return '足球'
        } else if (type === 2) {
          return '羽毛球'
        } else if (type === 3) {
          return '篮球'
        } else if (type === 4) {
          return '乒乓球'
        }
      },
      reConvertType(type) {
        if (type === '足球') {
          return 1
        } else if (type === '羽毛球') {
          return 2
        } else if (type === '篮球') {
          return 3
        } else if (type === '乒乓球') {
          return 4
        } else {
          return type
        }
      },
      verrify(club) {
        let that = this;
        if (club.name && club.principal && club.createTime && club.type && club.activityTimes && club.vipCount) {
          if (club.activityTimes < 0) {
            that.$message('活动次数需要大于零');
            return false;
          } else if (club.vipCount < 0) {
            that.$message('vip数量需要大于零');
            return false;
          } else {
            return true;
          }
        }else {
          that.$message('请确认填入信息');
          return false;
        }


      }

    },
    filters: {
      dateFormat: function (dateStr, pattern = 'yyyy-MM-dd') {
        // 根据给定的时间字符串，得到特定的时间
        var dt = new Date(dateStr)

        //   yyyy-mm-dd
        var y = dt.getFullYear()
        var m = (dt.getMonth() + 1).toString().padStart(2, '0')
        var d = dt.getDate().toString().padStart(2, '0')

        if (pattern.toLowerCase() === 'yyyy-mm-dd') {
          return `${y}-${m}-${d}`
        } else {
          var hh = dt.getHours().toString().padStart(2, '0')
          var mm = dt.getMinutes().toString().padStart(2, '0')
          var ss = dt.getSeconds().toString().padStart(2, '0')

          return `${y}-${m}-${d} ${hh}:${mm}:${ss}`
        }
      },
      typeFormat: function (type) {
        if (type === 1) {
          return '足球'
        } else if (type === 2) {
          return '羽毛球'
        } else if (type === 3) {
          return '篮球'
        } else if (type === 4) {
          return '乒乓球'
        }
      }
    }
  }

</script>
<style>

</style>
