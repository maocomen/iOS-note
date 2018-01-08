# iOS 谈 js 与原生交互原理

这里推荐两个iOS网桥库 [DSBridge-IOS](https://github.com/wendux/DSBridge-IOS) 还有 [WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)

简书上有篇文章用来描述 DSBridge 的优点，也可以看下[传送门](http://www.jianshu.com/p/3946605109ed)

这篇文章主要用来描述下 DSBridge 的交互原理(因为 WebViewJavascriptBridge 我还没有研究,只是看过一下)

### 一、UIWebView 篇

#### 1、js 调用原生

关于 UIWebView 实现 js 调用原生的方法，大概有这么三种(我所知道的是三种)，==一种==是在 UIWebViewDelegate 的`-(BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType;`

这个代理方法中一顿操作。 WebViewJavascriptBrdge 就是用这种方法实现的，以下是实现代码

```objective-c
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {
    if (webView != _webView) { return YES; }
    NSURL *url = [request URL];
    __strong WVJB_WEBVIEW_DELEGATE_TYPE* strongDelegate = _webViewDelegate;
    if ([_base isCorrectProcotocolScheme:url]) {
        if ([_base isBridgeLoadedURL:url]) {
            [_base injectJavascriptFile];
        } else if ([_base isQueueMessageURL:url]) {
            NSString *messageQueueString = [self _evaluateJavascript:[_base webViewJavascriptFetchQueyCommand]];
            [_base flushMessageQueue:messageQueueString];
        } else {
            [_base logUnkownMessage:url];
        }
        return NO;
    } else if (strongDelegate && [strongDelegate respondsToSelector:@selector(webView:shouldStartLoadWithRequest:navigationType:)]) {
        return [strongDelegate webView:webView shouldStartLoadWithRequest:request navigationType:navigationType];
    } else {
        return YES;
    }
}
```

这种实现依托与 iOS 中的 WebKit 框架

==一种==就是 DSBridge 中的这种方法(感觉这种方法要优雅很多啊)

```objective-c
- (void)webView:(UIWebView *)webView didCreateJavaScriptContext:(JSContext *)ctx
{
    __block  JSContext * ctx_=ctx;
    ctx[@"_dsbridge"]=^(NSString * method,NSString * args){
        return [JSBUtil call:method :args JavascriptInterfaceObject:JavascriptInterfaceObject jscontext:ctx_];
    };
    [ctx evaluateScript:INIT_SCRIPT];
}
```

这种实现的话用到了 iOS7 之后提供的 JavaScriptCore 引擎，有篇文章大家有兴趣的可以看下[iOS引入JavaScriptCore引擎框架](https://segmentfault.com/a/1190000004285316)

==第三种==就是用 NSURLProtocol 实现 js 调用原生的拦截(有兴趣可以自己研究一下)

今天我们主要探究一下 DSBridge 中这种优雅的实现

JavaScriptCore 的交互原理就跟安卓很相似了，==native 将接口注入到js的上下文中，js在合适的时候调用函数即可==,这样子的话，js 的上下文获取就成了重点，只要能获取到上下文，我们做的事情就会很多了，DSBridge 也有很多地方体现到。

用这个方法可以很简单的获取到 webView 的上下文，（ KVC ，键值编码）

```objective-c
JSContext *context=[webView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
```

但是，实际上，apple 并未给开发者提供访问 UIWebView 的方法，虽然通过 KVC 可达到目标，但是当 APP 采用该种 hack 方法时，有很大几率不能通过 APP Store 的审核，所以，这种方法感觉并不是那么完美。

在 OS X 中，WebFrameLoadDelegate 负责 WebKit 与 NSWebView 的通信，由于NSWebView 内部仍然使用 WebKit 渲染引擎，若要侦听渲染过程中的一系列事件，则必须使用 WebFrameLoadDelegate 对象。

可是在 iOS 中，我们找不到 WebFrameLoadDelegate 这个对象，怎么办咧，那就要提到 iOS 界的一个伟人了，==Nick Hodapp== 通过看 WebKit 源码的过程中发现，在 iOS 中，尽管没有暴露 WebFrameLoadDelegate 这个对象，但是在具体实现上仍会判断 WebKit 的 implement 有没有实现这个协议的某些方法，如果实现则仍会执行。这样子的话，我们需要只需要实现==webView:didCreateJavaScriptContext:forFrame:== 就好了。

这个方法会传递三个参数

* webView ，此 WebView 并不是 UIWebVIew ，Nick 曾做过测试，通过获取的WebView 并不能遍历到我们需要的 UIWebVIew ，因此推测，这个 WebView 是一个 UIView 的 proxy 对象，不是 UIView 类
* JSContext ，我们想要获得的东西
* WebFrame ，webkit 框架中的 WebFrame 对象，暂且用不到

在 DSBridge 的实现里，会在函数内调用下面的方法对 js 的上下文进行 OC 方法的注入（为 js 的上下文注入一个block）

```objective-c
- (void)webView:(UIWebView *)webView didCreateJavaScriptContext:(JSContext *)ctx
{
    __block  JSContext * ctx_=ctx;
    ctx[@"_dsbridge"]=^(NSString * method,NSString * args){
        return [JSBUtil call:method :args JavascriptInterfaceObject:JavascriptInterfaceObject jscontext:ctx_];
    };
    [ctx evaluateScript:INIT_SCRIPT];
}
```

这个 block 代码块有两个参数

* method ，js 调用原生方法的方法名

* args ，js 调用原生方法所传的参数

  ​

js 需要调用原生方法的时候，都会走到这里来

```objective-c
ctx[@"_dsbridge"]=^(NSString * method,NSString * args){
        return [JSBUtil call:method :args JavascriptInterfaceObject:JavascriptInterfaceObject jscontext:ctx_];
    };
```

JSBUtil 是 NSObject 的派生类，这个类里面主要做了一些 json 字符串转 json ，json 转 json 字符串和 js 调用原生方法是的调度工作。我们可以看一下 JSBUtil 中这个方法的实现。

```objective-c
+(NSString *)call:(NSString*) method :(NSString*) args  JavascriptInterfaceObject:(id) JavascriptInterfaceObject jscontext:(id) jscontext
{
    SEL sel=NSSelectorFromString([method stringByAppendingString:@":"]);
    SEL selasyn=NSSelectorFromString([method stringByAppendingString:@"::"]);
    NSString *error=[NSString stringWithFormat:@"Error! \n Method %@ is not invoked, since there is not a implementation for it",method];
    NSString *result=@"";
    if(!JavascriptInterfaceObject){
        NSLog(@"Js bridge method called, but there is not a JavascriptInterfaceObject, please set JavascriptInterfaceObject first!");
    }else{
        NSDictionary * json=[JSBUtil jsonStringToObject:args];
        NSString * cb;
        do{
            if(json && (cb= [json valueForKey:@"_dscbstub"])){
                if([JavascriptInterfaceObject respondsToSelector:selasyn]){
                    void (^completionHandler)(NSString *) = ^(NSString * value){
                        value=[value stringByAddingPercentEscapesUsingEncoding: NSUTF8StringEncoding];
                        NSString*js=[NSString stringWithFormat:@"try {%@(decodeURIComponent(\"%@\"));delete window.%@; } catch(e){};",cb,(value == nil) ? @"" : value,cb];
                        if([jscontext isKindOfClass:JSContext.class]){
                            [jscontext evaluateScript:js ];
                        }else if([jscontext isKindOfClass:WKWebView.class]){
                            @synchronized(jscontext)
                            {
                                UInt64  t=[[NSDate date] timeIntervalSince1970]*1000;
                                g_ds_js_cache=[g_ds_js_cache stringByAppendingString:js];
                                if(t-g_ds_last_call_time<50){
                                    if(!g_ds_have_pending){
                                        [self evalJavascript:(WKWebView *)jscontext :50];
                                        g_ds_have_pending=true;
                                    }
                                }else{
                                    [self evalJavascript:(WKWebView *)jscontext  :0];
                                }
                            }
                        }
                    };
                    SuppressPerformSelectorLeakWarning(
                                                       result=[JavascriptInterfaceObject performSelector:selasyn withObject:json withObject:completionHandler];
                                                       
                                                       );
                    //when performSelector is performing a selector that return value type is void,
                    //the return value of performSelector always seem to be the first argument of the selector in real device(simulator is nil).
                    //So,you should declare the return type of all api as NSString explicitly.
                    if(result==(id)json){
                        result=@"";
                    }
                    
                    break;
                }
            }else if([JavascriptInterfaceObject respondsToSelector:sel]){
                SuppressPerformSelectorLeakWarning(
                                                   result=[JavascriptInterfaceObject performSelector:sel withObject:json];
                                                   );
                break;
            }
            NSString*js=[error stringByAddingPercentEscapesUsingEncoding: NSUTF8StringEncoding];
            js=[NSString stringWithFormat:@"window.alert(decodeURIComponent(\"%@\"));",js];
            if([jscontext isKindOfClass:JSContext.class]){
                [jscontext evaluateScript:js ];
            }else if([jscontext isKindOfClass:WKWebView.class]){
                [(WKWebView *)jscontext evaluateJavaScript :js completionHandler:nil];
            }
            NSLog(@"%@",error);
        }while (0);
    }
    if(result == nil||![result isKindOfClass:[NSString class]]){
        result=@"";
    }
    return result;
}

```



这个方法有四个参数

* method ，上面我们也有讲到，js 所需要调用的原生方法的方法名
* args ， js 调用原生方法的入参
* JavascriptInterfaceObject ， 这个东西就比较重要了，js 给我们回调了方法名和入参，但是，让谁去实现这个方法呢，JavascriptInterfaceObject 就起了个代理类的作用，由 JavascriptInterfaceObject 来进行 原生方法的实现或者调度
* jscontext ，js 上下文



#### 2、原生调用 js 

UIWebView 使用下面这个方法即可调用到 js 代码中的方法

```objective-c
[self stringByEvaluatingJavaScriptFromString:javaScriptString]
```

简单的一批。。。。

具体实现的话，大家可以分别下载 DSBridge 和 WebViewJavascriptBridge 。





### 二、WKWebView 篇

#### 1、WKWebView简介

一言不合先甩个链接[WKWebView学习笔记](http://www.jianshu.com/p/7bb5f15f1daa)

webView 是我们 iOS 开发者日常生活中不可或缺的一个组件，通常，我们会用 UIWebView 来实现我们的需求，但是出于 UIWebView 自身的一些缺陷，使用起来总是差强人意。

iOS8 之后苹果推荐使用 WKWebView 替代 UIWebView ，新控件的优点也很强大：

1. 性能、稳定性上，WKWebView更为优秀
2. WKWebView更多的支持HTML5的特性
3. WKWebView更快，占用内存可能只有UIWebView的1/3 ~ 1/4
4. WKWebView高达60fps的滚动刷新率和丰富的内置手势
5. WKWebView具有Safari相同的JavaScript引擎
6. WKWebView增加了加载进度属性
7. 将UIWebViewDelegate和UIWebView重构成了14个类与3个协议

简单了解一下，我们再来讲用WKWebView做容器时候，如何与 js 进行交互。

#### 1、js 调用原生

WKWebView与UIWebView的交互机制可谓千差万别，上手可能会感觉有点儿蛋疼，但是 WKWebView 暴露了更多的方法，可以方便我们定制自己的需求

DSBridge 在 WKWebView 初始化的时候，对 js 的上下文进行了注入

```objective-c
NSString * js=[@"_dswk='_dsbridge=';" stringByAppendingString: INIT_SCRIPT];
    WKUserScript *script = [[WKUserScript alloc] initWithSource:js
                                                  injectionTime:WKUserScriptInjectionTimeAtDocumentStart
                                               forMainFrameOnly:YES];
    [configuration.userContentController addUserScript:script];
```

注入参数时，injuctionTime 有两个选项：

* WKUserScriptInjectionTimeAtDocumentStart ，注入时机为 document 的元素生成之后，其他内容 load 之前。
* WKUserScriptInjectionTimeAtDocumentEnd ，注入时机为 document 全部 load 完成，任意子资源 load 之前。

这块儿代码的意思是，在 document 的元素生成之后，其他内容 load 之前，为 js 的上下文注入一段内容。（ DSBridge 中这个内容的目的是为了让 js 在调用原生方法走到WebUIDelegate时，标识符加上 _dsbridge 前缀）



```objective-c
WKUserScript *scriptDomReady = [[WKUserScript alloc] initWithSource:@";prompt('_dsinited');"
                                                          injectionTime:WKUserScriptInjectionTimeAtDocumentEnd
                                                       forMainFrameOnly:YES];
    [configuration.userContentController addUserScript:scriptDomReady];
```

这段代码的意思是，在 document 全部 load 完成，任意子资源 load 之前，为 js 的上下文注入一段内容。（ DSBridge 中这个内容的目的是为了让 document 的元素生产之后走到WebUIDelegate时，标识符加上 _dsinited 的前缀）

无论是 document load 完成还是 js 触发的点击事件，都会走到WebUIDelegate的方法里去，可以看看[官方文档](https://developer.apple.com/reference/webkit/webuidelegate?language=objc)。

```objective-c
- (void)webView:(WKWebView *)webView runJavaScriptTextInputPanelWithPrompt:(NSString *)prompt
    defaultText:(nullable NSString *)defaultText initiatedByFrame:(WKFrameInfo *)frame
completionHandler:(void (^)(NSString * _Nullable result))completionHandler
{
    NSString * prefix=@"_dsbridge=";
    if ([prompt hasPrefix:prefix])
    {
        NSString *method= [prompt substringFromIndex:[prefix length]];
        NSString *result =[JSBUtil call:method :defaultText JavascriptInterfaceObject:_JavascriptInterfaceObject jscontext:webView];
        completionHandler(result);
    }else if([prompt hasPrefix:@"_dsinited"]){
        completionHandler(@"");
        if(javascriptContextInitedListener) javascriptContextInitedListener();
        
    }else {
        if(self.DSUIDelegate && [self.DSUIDelegate respondsToSelector:
                                 @selector(webView:runJavaScriptTextInputPanelWithPrompt
                                           :defaultText:initiatedByFrame
                                           :completionHandler:)])
        {
            return [self.DSUIDelegate webView:webView runJavaScriptTextInputPanelWithPrompt:prompt
                                  defaultText:defaultText
                             initiatedByFrame:frame
                            completionHandler:completionHandler];
        }else{
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:prompt message:@"" delegate:self cancelButtonTitle:@"取消" otherButtonTitles:@"确定", nil];
            [alert setAlertViewStyle:UIAlertViewStylePlainTextInput];
            UITextField *txtName = [alert textFieldAtIndex:0];
            txtName.text=defaultText;
            [alert show];
            while (!confirmDone){
                [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
            }
            confirmDone=false;
            completionHandler([txtName text]);
        }
    }
}
```

DSBridge 是在上面这个代理方法中处理的，当 prompt 带有 _dsbridge= 前缀时，说明是 js 调用原生，执行相应的方法调度，当 prompt 带有 _dsinited 前缀时，说明 document 全部 load 完成。（ DSBridge 在其他情况下就都是展示 alert 了， 功能感觉还是没那么多，有待作者完善吧）



#### 2、原生调用 js

```objective-c
[self evaluateJavaScript:script completionHandler:^(id value,NSError * error){
        if(completionHandler) completionHandler(value);
    }];
```

用这个方法，就可以调用到 js 中的方法了。completionHandler 是个异步回调。