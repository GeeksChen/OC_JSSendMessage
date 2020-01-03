#### 前沿

>oc与js交互有两种：oc调用js方法实现功能和js调用oc实现功能。

#### 1.oc调用js

##### 1.1.使用场景
>截取网页输入值，供oc使用，去页面广告

##### 1.2.实现流程
```
// 1.初始化一个 WKUserContentController 对象用于js交互
WKUserContentController* userContent = [[WKUserContentController alloc] init];
[userContent addScriptMessageHandler:self name:@"messageHandler"];
config.userContentController = userContent;
```
```
// 2.处理js传来的一些事件
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
NSString *mBody = [message.body stringByReplacingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
NSLog(@"%@",mBody);
// 判断是否是调用原生的
if ([@"messageHandler" isEqualToString:message.name]) {
NSDictionary *mDic = [self dictionaryWithJsonString:mBody];
if ([mDic[@"type"] isEqualToString:@"submit"]) {
NSLog(@"submit");
}else {
NSLog(@"invilid request");
}
}
}
```


#### 2.js调用oc

##### 2.1.使用场景
>在app端获取状态值，回传html页面；把app信息传给js。

##### 2.2.实现流程
```
//1.  注册代理
_wkWebView.navigationDelegate = self;
_wkWebView.UIDelegate = self;
```
```
//2.页面加载完成，处理js事件
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation{
NSLog(@"didFinishNavigation");
[webView evaluateJavaScript:@"deviceInfo('8.3')" completionHandler:^(id _Nullable result, NSError * _Nullable error) {
NSLog(@"====ok");
}];
}
```
```
//3.弹框事件
#pragma mark WKUIDelegate
- (void)webView:(WKWebView *)webView runJavaScriptAlertPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(void))completionHandler
{
UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"提示" message:message preferredStyle:UIAlertControllerStyleAlert];
UIAlertAction *action = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleCancel handler:^(UIAlertAction * _Nonnull action) {

completionHandler();//此处的completionHandler()就是调用JS方法时，`evaluateJavaScript`方法中的completionHandler

}];
[alert addAction:action];
[self presentViewController:alert animated:YES completion:nil];
}
```

>不管大佬们关不关注公众号，我都会放上本章的[Demo](https://github.com/GeeksChen/OC_JSSendMessage)

个人作品1：（匿名聊天）
[http://im.meetyy.cn/](http://im.meetyy.cn/)

个人作品2：（单身交友）
![公众号Meetyy](https://upload-images.jianshu.io/upload_images/1745735-9ba29c862a0268be.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


