---
title: MacOS App手动签名打包方法
date: 2015-11-18 14:09:16
categories: iDev
tags:
---
用了跨平台的方案开发App，自然享受不到苹果亲儿子XCode开发带来的便利。
比如打包发布等一系列的操作需要我们手工来操作了。

苹果的安全性与隐私中对于程序分成3类：

1、Mac App Store;

2、Mac App Store和被认可的开发者;

3、任何来源。

一般用户的设置都是1或2,选3的话安全性就很难保证了。

针对第1,2种的App, 苹果的证书对应分为2类，一个类Developer开头的开发者证书，一类是3rd开头的App Store发布证书。

如果不准备上架，或者使用了苹果的私有Api，那么就只能使用开发者证书给App签名了。
<!--more-->
签名方式：
```
#!/bin/sh
#  Script.sh
#  sign

path=$(cd "$(dirname "$0")";pwd)
echo $path
cd $path

codesign -f -s "Developer ID Application: Shanghai XXX Limited (88B8QAXTFE)" -v --deep "XXX.app"

echo -n "检查签名？（y/n）"

read is_sign

if [[ $is_sign  = "y" ]]; then
  spctl --verbose=4 --assess --type execute XXX.app
fi

```

针对上架App Store的方案,需要编写entitlements文件，设置沙盒和访问权限。然后打包成pkg，通过Application Loader上传给Store审核，通过通过后就能对外发布了。

签名方式：
```
#!/bin/sh
#  Script.sh
#  sign

path=$(cd "$(dirname "$0")";pwd)
echo $path
cd $path

codesign --deep --force --verify --verbose --sign "3rd Party Mac Developer Application: Shanghai XXX Limited (88B8QAXTFE)" --entitlements XXX.entitlements XXX.app

echo -n "检查签名？（y/n）"

read is_sign

if [[ $is_sign  = "y" ]]; then
  spctl --verbose=4 --assess --type execute XXX.app
fi


productbuild --component XXX.app /Applications XXX.pkg --sign "3rd Party Mac Developer Installer: Shanghai XXX Limited (88B8QAXTFE)"

```

