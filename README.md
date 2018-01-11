# 监听微信返回按钮事件

参考来源： [HTML5 - pushState 无刷新更新地址](http://tc.wangchao.net.cn/it/detail_119710.html)  

本人之前在做微信公众号开发过程中，遇到这么一个场景：从微信公众号菜单进入一个顶部 tab 选项卡页面，切换选项卡就改变 location.search 中的值（给后台传值用的）。而每改变 location.search 一次，整个页面就会刷新一次。  

那么来回切换选项卡10次，页面就会刷新10次，浏览器中就保存了10条历史记录。   

此时，我点返回键，或者轻触安卓的 home 键触发返回，页面不会直接关闭回到公众号主菜单，而是不断地返回到上一条历史记录，用户在界面上看到的就是，页面一直关不掉。  

好烦。。。是不是？然后我就百度上各种搜，试了好几种方法，又历经了好几次坑，终于总结出了现在的这份代码。  

**调用方法如下： **  

``` 
	common.listenBackBtn (function () {
		//这里写监听返回的回调事件即可
		//例如，关闭当前页，直接返回到微信公众号
		if (typeof window.WeixinJSBridge == "undefined"){
			$(document).on('WeixinJSBridgeReady',function(){ 
		 		WeixinJSBridge.call('closeWindow');
		  	}); 
		} else {
		  WeixinJSBridge.call('closeWindow');
		}
	});

```


```
var common = {
	listenBackBtn: function (callback) {
		;(function(window,  location)  { 
			setTimeout(function () {
				history.replaceState(null,  document.title,  location.pathname + location.search + "#!/stealingyourhistory");  
				history.pushState(null,  document.title,  location.pathname+ location.search);  
			},300);
			
			window.addEventListener("popstate",  function()  {  
				if(location.hash  ===  "#!/stealingyourhistory")  {  
					history.replaceState(null, document.title,  location.pathname + location.search);  
					if(callback){
						var f = callback();
						if (f === false) {
							history.replaceState(null,  document.title,  location.pathname + location.search + "#!/stealingyourhistory");  
							history.pushState(null,  document.title,  location.pathname+ location.search); 
						}
					}
				}
			},  false);
		}(window,  location));
	}
};

```
