---
title: Apple Watch远程通知的实现以及真机实测
date: 2015-10-02 15:13:35
categories: iDev
tags: [Apple Watch]
---

**题记：**任何一款优秀产品的背后都是一群为此呕心沥血的程序猿，前方多坑，注意避让！

**注意事项**
1、真机测试的时候最方便的应该是使用adhoc证书，这个是苹果推送的要求。
2、**手表一定要戴在手腕上！手表一定要戴在手腕上！手表一定要戴在手腕上！**重要的事情要说3遍。我曾傻傻地把手表放在桌上等通知，结果半天都没收到通知，通知全在手机上显示了。
3、手机需要处于“黑屏状态”，只要屏幕亮着，消息都会出现在手机上。
4、推送服务器的`JSON Payloads`格式一般需要重新调整。原先只有手机的时候alert只要有内容即可，现在需要分为title和body两部分了。如果要在手表中显示自定义通知样式，增加交互按钮，还需要定义category，value要和Watch中的Storyboard通知界面中的identifier一致，否则将显示苹果默认的通知样式，只有一个关闭按钮。
新结构如下：
{
“aps” : {
“alert” : {
“title” : “新通知”,
“body” : “OS X El Captain正式版今天推送……”,
},
“category”:”respond”,
“badge” : 5,
}
}
<!--more-->
**自定义推送通知样式的代码示例：**
1、在手机端app的AppDelegate中注册推送通知的影响按钮
```
- (void)registerSettingsAndCategories {
// Create a mutable set to store the category definitions.
NSMutableSet* categories = [NSMutableSet set];

// Define the actions for a meeting invite notification.
UIMutableUserNotificationAction* acceptAction = [[UIMutableUserNotificationAction alloc] init];
acceptAction.title = NSLocalizedString(@"Repondre", @"Repondre commentaire");
acceptAction.identifier = @"respond";
acceptAction.activationMode = UIUserNotificationActivationModeForeground; //**前台处理**.
acceptAction.authenticationRequired = NO;

UIMutableUserNotificationAction* declineAction = [[UIMutableUserNotificationAction alloc] init];
declineAction.title = NSLocalizedString(@"Decline", @"Decline invitation");
declineAction.identifier = @"decline";
declineAction.activationMode = UIUserNotificationActivationModeBackground;//**后台处理**
declineAction.authenticationRequired = NO;

// Create the category object and add it to the set.
UIMutableUserNotificationCategory* inviteCategory = [[UIMutableUserNotificationCategory alloc] init];
[inviteCategory setActions:@[acceptAction,declineAction]
                forContext:UIUserNotificationActionContextDefault];
inviteCategory.identifier = @"respond"; //**这一行很重要**

[categories addObject:inviteCategory];

//Configure other actions and categories and add them to the set...
UIUserNotificationSettings* settings = [UIUserNotificationSettings settingsForTypes:
                                        (UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound)
                                                                         categories:categories];

[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
}
```
上述代码是在 appdelegate 中的
-(BOOL)application:(UIApplication _)application didFinishLaunchingWithOptions:(NSDictionary_ )launchOptions调用的。
2、设置推送服务器`Apns server`的消息如下
```
{
  "aps" : {
      "alert" : {
          "title" : "新通知",
          "body" : "OS X El Captain正式版今天推送......",
      },
      "category":"respond",
      "badge" : 5,
  }
}
```
3、在WatchKitExtention中处理前台的按钮事件
```
- (void)handleActionWithIdentifier:(NSString *)identifier forRemoteNotification:(NSDictionary *)remoteNotification{
     if ([identifier isEqualToString:@"Repondre"]) {
        //Do stuff Here to handle action... 
     }
 }
```
4、 在手机端app的appDelegate中处理后台的按钮事件[因推送服务器调整对线上版本有影响，暂未验证]:
```
- (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forRemoteNotification:(NSDictionary *)userInfo completionHandler:(void (^)())completionHandler {
     if ([identifier isEqualToString:@"Decline"]) {
        //Do stuff Here to handle action... 
     }
    completionHandler();
}
```
注册为后台类型的按钮在手机的通知框中也会显示，在手机端的响应方式如下:
```
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
}
```