<template>
  <div class="hello">
   <button @click="exportWord">导出</button>
  </div>
</template>

<script>
export default {
  name: "name",
  data() {
    return {
      // 表单对象
      form: {
        custName: "杰斯", // 客户姓名
        phoneNumber: "138xxxxxxxx", // 联系方式
        projectRequirement: "为了更美好的明天而战", // 项目要求
        totalPrice: 140, // 合计报价
        remark: "QEWARAEQAAAAAAAAA", // 备注
        checkReason: '同意' // 审核备注
      },
      // 表格信息
      tableData: []
    };
  },
  created() {
    this.tableData = [
      {
        number: 1, // 序号
        name: "设备1", // 设备名称
        num: 1, // 数量
        salePrice: 10, // 销售单价
        saleTotal: 10, // 销售合计
        remark: "啦啦啦" // 备注
      },
      {
        number: 2, // 序号
        name: "设备2", // 设备名称
        num: 2, // 数量
        salePrice: 20, // 销售单价
        saleTotal: 40, // 销售合计
        remark: "啦啦啦" // 备注
      },
      {
        number: 3, // 序号
        name: "设备3", // 设备名称
        num: 3, // 数量
        salePrice: 30, // 销售单价
        saleTotal: 90, // 销售合计
        remark: "啦啦啦" // 备注
      }
    ];
  },
  methods: {
    // 点击导出word
    exportWord: function() {
      let _this = this;
      // 读取并获得模板文件的二进制内容
      JSZipUtils.getBinaryContent("../../static/input.docx", function(error, content) {
        // input.docx是模板。我们在导出的时候，会根据此模板来导出对应的数据
        // 抛出异常
        if (error) {
          throw error;
        }
        
        // 创建一个JSZip实例，内容为模板的内容
        let zip = new JSZip(content);
        // 创建并加载docxtemplater实例对象
        let doc = new window.docxtemplater().loadZip(zip);
        // 设置模板变量的值
        doc.setData({
          ..._this.form,
          table: _this.tableData
        });
        
        try {
          // 用模板变量的值替换所有模板变量
          doc.render();
        } catch (error) {
          // 抛出异常
          let e = {
            message: error.message,
            name: error.name,
            stack: error.stack,
            properties: error.properties
          };
          console.log(JSON.stringify({ error: e }));
          throw error;
        }
        
        // 生成一个代表docxtemplater对象的zip文件（不是一个真实的文件，而是在内存中的表示）
        let out = doc.getZip().generate({
          type: "blob",
          mimeType:
            "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
        });
        // 将目标文件对象保存为目标类型的文件，并命名
        saveAs(out, "报价单.docx");
      });
    }
  }
  };
</script>


<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
