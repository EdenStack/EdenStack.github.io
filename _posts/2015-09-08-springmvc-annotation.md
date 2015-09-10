---
layout:     post
title:      springmvc部分注解
date:       2015-09-08 09:25:18
summary:    Spring MVC annotation
author:     Tneciv
categories: spring
thumbnail: code
tags:
 - Spring MVC
 - annotation
---

##Json @Reponsebody
前台使用js ajax传入封装json数据。
后台controller接受数据，经过xxServiceImpl，调用xxMapper.java对应在xxMapper.xml中的SQL语句进行增删改查。
其中，``@Reponsebody``注解于``xxController.java``中，CRUD等需要返回结果的方法上。
返回json结果。
jackson与springmvc配置稍后补充。

````java
@Controller
@RequestMapping("/prod")
public class ProdController {
	Logger logger = getLogger(ProdController.class);

	@Resource
	private ProdService prodService;

	@ResponseBody
	@RequestMapping("/query")
	public ResEnv<List<SsoProd>> query(SsoProd prod) {
		List<SsoProd> list = null;
		try {
			list = this.prodService.query(prod);
		} catch (ApplicationException e) {
			logger.error(e.getMessage(), e);
			return ResEnv.fail(e.getMessage(), e);
		} catch (Exception e) {
			logger.error("系统错误！", e);
			return ResEnv.fail(e);
		}
		return ResEnv.success(list);
	}
}
````


##获取参数 @RequestParam
在SpringMVC后台控制层获取参数的方式主要有两种，一种是request.getParameter("name")，另外一种是用注解@RequestParam直接获取。
从前台获取到指定``@RequestParam(required = false, value = "vStatus") String vStatus``的值。

###1. 基本使用，获取提交的参数 

后端代码：

````java
@RequestMapping("testRequestParam")    
   public String filesUpload(@RequestParam String inputStr, HttpServletRequest request) {    
    System.out.println(inputStr);  
      
    int inputInt = Integer.valueOf(request.getParameter("inputInt"));  
    System.out.println(inputInt);  
      
    // ......省略  
    return "index";  
   } 
````

前端代码：

````html
<form action="/gadget/testRequestParam" method="post">    
     参数inputStr:<input type="text" name="inputStr">    
     参数intputInt:<input type="text" name="inputInt">    
</form>  
````

前端界面： 
![](http://i.imgur.com/6OF19cf.png)

> 执行结果： 
> test1 
> 123 

spring会自动根据参数名字封装进入，我们可以直接拿这个参数名来用.

###2.各种异常情况处理 
* 可以对传入参数指定参数名 

````java
@RequestParam String inputStr  
// 下面的对传入参数指定为aa，如果前端不传aa参数名，会报错  
@RequestParam(value="aa") String inputStr  
````

错误信息： 
HTTP Status 400 - Required String parameter 'aa' is not present 

* 可以通过required=false或者true来要求@RequestParam配置的前端参数是否一定要传 

````java
// required=false表示不传的话，会给参数赋值为null，required=true就是必须要有  
@RequestMapping("testRequestParam")    
    public String filesUpload(@RequestParam(value="aa", required=true) String inputStr, HttpServletRequest request)  
````

* 如果用@RequestMapping注解的参数是int基本类型，但是required=false，这时如果不传参数值会报错，因为不传值，会赋值为null给int，这个不可以 

````java
@RequestMapping("testRequestParam")    
   public String filesUpload(@RequestParam(value="aa", required=true) String inputStr,   
        @RequestParam(value="inputInt", required=false) int inputInt  
        ,HttpServletRequest request) {    
      
    // ......省略  
    return "index";  
   }  
````

解决方法： 
    “Consider declaring it as object wrapper for the corresponding primitive type.”建议使用包装类型代替基本类型，如使用“Integer”代替“int”。
