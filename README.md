# CrudTable
用于Element-UI的增删改查数据表格

## 属性 prop
### 全局
属性名|类型|默认值|说明
:-|:-|:-|:-
api|Object|无|接口对象
idKey|String|id|get/update/del时的主键名
showCheckBox|Boolean|false|是否显示CheckBox多选
showPage|Boolean|true|是否显示分页栏
delDesc|String|确认要删除该记录吗?|删除数据的提示信息
multiDelDesc|String|确认要删除所选记录吗?|批量删除数据的提示信息

### 工具栏
属性名|类型|默认值|说明
:-|:-|:-|:-
showMultiDel|Boolean|false|是否显示批量删除按钮
showAdd|Boolean|true|是否显示新增按钮
showSearch|Boolean|true|是否显示筛选按钮
addBtn|String|新增|新增按钮的文字
searchBtn|String|筛选|筛选按钮的文字
multiDelBtn|String|批量删除|批量删除按钮的文字

### 操作列按钮
属性名|类型|默认值|说明
:-|:-|:-|:-
showDel|Boolean|true|是否显示删除按钮
showEdit|Boolean|true|是否显示编辑按钮
editBtn|String|编辑|编辑按钮的名字
delBtn|String|删除|删除按钮的名字
opBtn|String|操作|操作列的名字
opBtnWidth|Number|200|操作列的最小宽度

### 编辑(新增/修改)
属性名|类型|默认值|说明
:-|:-|:-|:-
editDefault|Object|无|编辑对象的默认值
editRule|Object|无|表单验证规则
editLabelPosition|String|right|表单条目Label的位置,可选 `right`,`top`,`left`
editMargin|String|`0 30px 0 20px`|表单的整体margin
labelWidth|Number|100|表单条目Label的宽度
dlgWidth|String|600px|对话框宽度
cancelBtn|String|取消|编辑对话框取消按钮的名字
submitBtn|String|提交|编辑对话框提交按钮的名字
editTitle|String|编辑信息|编辑对话框标题
editExtendRow|Boolean|true|编辑数据时,是否继承当前行的数据,如果api未提供get方法,固定为true

### 筛选
属性名|类型|默认值|说明
:-|:-|:-|:-
searchLabelPosition|String|right|表单条目Label的位置,可选 `right`,`top`,`left`
searchMargin|String|`0 30px 0 20px`|表单的整体margin
searchLabelWidth|Number|100|表单条目Label的宽度
searchDefault|Object|无|筛选对象的默认值,该值变化,会自动刷新数据
searchDlgWidth|String|500px|对话框宽度
restoreBtn|String|恢复默认|筛选对话框取消筛选按钮的名字
searchTitle|String|筛选数据|筛选对话框标题

## 插槽 slot
插槽名称|作用域对象|说明
:-|:-|:-
toolbar|无|工具栏,显示在最前面
toolbar2|无|工具栏,显示在新增按钮后面
toolbar3|无|工具栏,显示在筛选按钮后面
toolbar4|无|工具栏,显示在最后面
cols|无|表格显示的列,`el-table-column`
opBtn|`row`行数据|操作列按钮,显示在最前面
opBtn2|`row`行数据|操作列按钮,显示在编辑按钮后面
opBtn3|`row`行数据|操作列按钮,显示在最后面
editCols|`editModel`编辑对象|编辑表单条目
searchCols|`searchModel`筛选对象|筛选表单条目

## 事件 Event
事件名称|说明|参数
:-|:-|:-
open-edit|打开编辑对话框|新增时无,编辑时当前行对象`row`
row-click|点击行数据|`row`, `event`, `column`
## 方法 Method
可能需要搭配$refs使用,未列出不建议调用
名称|参数|说明
:-|:-|:-
getData|page|获取数据
refresh|无|恢复默认筛选
## 数据 Data
可能需要搭配$refs使用,未列出不建议修改
名称|类型|说明
:-|:-|:-
editModel|Object|当前编辑的对象
select|Array|当前选中的行对象的数组
