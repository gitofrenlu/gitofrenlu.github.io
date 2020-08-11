# 前后端数据交互格式
<!-- author:lu.ren@mljr.com -->

## get
**地址栏传送数据** 
* query string parameters


## post
**报文传送数据**
* text/plain   --->   request payload  ---> 流
* application/X-www-from-urlencode   ---> form data   --->  Getparameter /@RequestParam
* application/json(axios)    --->   request payload   ---> 流/@responsebody