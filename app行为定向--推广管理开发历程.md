## app行为定向--推广管理开发
#### 1. 推广管理相关参数
###### 推广标的
+ **store路径：**
adManagement->entity->adCampaign->basicSetting->object   

+ **object类型：**
字符串              
         
+ **可能的值：**
	+ landingPage
	+ androidDownload
	+ appStore      
	
###### 操作系统
+ **store路径：**
adManagement->entity->adGroup->adSlotSetting->osType   

+ **object类型：**
字符串              
         
+ **可能的值：**
	+ all
	+ ios
	+ android     
	
###### 联网方式
+ **store路径：**
adManagement->entity->adGroup->adSlotSetting->netword   

+ **object类型：**
对象              
         
+ **可能的值：**
	+ { type：'all' }
	+ { type：'custom', value: ['wifi', '4g', '3g', 'other'] }    

<br />
#### 2. 确定store存值
```
store路径：adSlotSetting->appBehaveSlot

appBehaveSlot取值：
{ type: 'all' }或者
{ 
  type: 'custom',
  value: ['slotPut', 'ridPut'],
  originAppSlot: { 
					   status: 'loading',
  					   queryCondition: {},
  					   total: 180,
  					   list: [],
                 },
  selectedAppSlot: [],
  originAppRid: {},
  selectedAppRid: [],
}
```
<br />
#### 3. 请求接口
+ 输入关键词，获取列表请求；
+ 保存时的请求；
+ 跳转到创意页的请求；
   

