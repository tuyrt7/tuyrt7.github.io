---
title: retrofit传递数组参数
date: 2019-09-24 10:04:56
tags:
	- blog
	- retrofit


---


关键字：retrofit 数组参数
接触Retrofit一段时间后，感觉到Retrofit越来越强大，方便快捷。  

传数组  

```//第1种
@GET("v1/enterprise/find")
Call<ResponseBody> getData(@Query("id") String id, @Query("linked[]") String... linked);

//第2种
@FormUrlEncoded
@POST("matter/app-config")
Flowable<JsonResponse> toolsConfig(@Field("idList[]")  List<String> idList);

//第3种
@GET
Call<JsonElement> checkIds(@Query("product_ids[]") String[] ids); 

```

代码调用： 
 
``` 
String id="Stay"；
String[] s = new String[]{"Stay"};
//调接口
getData(id, s); 
```  
<!--more-->