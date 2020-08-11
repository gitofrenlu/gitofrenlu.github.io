# element-ui中遇到的问题
<!-- author:jingxian.liang@mljr.com -->
## el-select
### Q：回显的字段是汉字，根据对应的汉字获取ID并作为下一个联动查询的参数
```
# 回显数据
{
	 "carSeries":"一汽奥迪A4",
	 "carYear":"(2003年至2005年)A4 B6",
}
```
```
# api:carSeries
[
	{"familyId":"402880ef0ca9c2b6010cc86045c300f1","familyName":"一汽奥迪A4"},
    {"familyId":"4028b2883dc34916013dcf1597b81a09","familyName":"一汽奥迪Q3"}
]
```

- 解决办法
```
template:
    el-select(placeholder="请选择",v-model="carInfo.carSeries", ref="carSeriesSel",@change="getCstyle")
        el-option(v-for="(option,index) in carSeries", :label="option.familyName", :value="option.familyName", :familyId="option.familyId", :key="option.index")


script:
    methods:{
        getCstyle() {
            this.$nextTick(function() {
                let familyId = this.$refs.carSeriesSel.selected.$attrs.familyId;
                let params = {
                    familyId
                };
                thirdAPI.getCstyle(params).then(_ => {
                    this.carStyle = _.data;
                })
            })
        },  
    }
```
## el-table 
### Q：如何自定义带单选按钮的表格
![avatar](https://femans.com/images/table_radio.png)
**[查看](https://femans.com/images/table_radio.png)**
```
template:
    el-table.el-table-single(:data="che300CarIdentify",ref="che300CarIdentify",border,tooltip-effect="dark",  max-height="350",highlight-current-row,@current-change="handleCurrentChange",@row-click ="showRow") 
                el-table-column(label="选择",width="70", center)
                    template(slot-scope="scope")
                        el-radio(v-model="radio",  :label="scope.$index")
                el-table-column(prop="model_name", label="车型")
                el-table-column(prop="model_price", label="新车指导价(万元)" )

                
script:
    data:{
        return {
            che300CarIdentify:[------],
            choseCarIdentify:null,
            radio:0 //默认选中第一项的radio
        }
    },
    methods:{
        showRow(row) {
            //赋值给radio
            this.radio = this.che300CarIdentify.indexOf(row);
        },
        handleCurrentChange(val) {
            this.choseCarIdentify = val;
        },
        getDetail(){
            //默认选中第一行数据
            this.$refs.che300CarIdentify.setCurrentRow(this.che300CarIdentify[0]);
        }
    }
```