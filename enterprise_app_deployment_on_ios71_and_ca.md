[iOS 7.1在线安装IPA失败以及数字证书](http://zengrong.net/post/2108.htm)

Enterprise app deployment doesn't work on iOS 7.1 and CA

## 错误现象

在 iOS 升级到 7.1 之后，使用 Safari 在线安装企业版或者 AD-HOC 版本的IPA时，iOS会提示：

>无法安装应用程序，因为“xx.xx.xx” 的证书无效。

这是一个蛋疼的提示，它的真正人类能懂的含义是：

在 iOS 7.1 版本以后，请使用 [HTTPS][1] 部署此类安装。

## 解决方案

### 1. 部署自己的HTTPS服务器

这种方案需要使用一个可信的证书颁发机构办法的证书，将其部署在自己的服务器上。配置自己的服务器软件（例如 Apache、Nginx等） 使其支持它。

这是一劳永逸的解决方案，如果需要大量部署，那么此种方案是首选。

注意，自己给自己颁发的证书是不行的。必须使用可信的证书颁发机构颁发的证书。这就意味着，你很可能需要付费。

后面会讲到数字证书的部分，会稍稍展开一点。<!--more-->

### 2. 使用已有的HTTPS服务器

只需要找一个已经部署的HTTPS服务器，将我们的部署文件（plist）传到该服务器上即可。

注意，为了保证下载速度，也为了保证域名的统一性，我们可以只把 plist 文件上传到HTTPS服务器上。

例如，在我们的网站（http://my.http.server）上有一个在线安装IPA页面是这样的：

<pre lang="html">
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<title>Setup My App</title>
</head>
<body>
<ul>
<li><a href="itms-services://?action=download-manifest&url=http://my.http.server/myapp.plist"> Tap Here to Install My App </a>
</li>
</ul>
</body>
</html>
</pre>

由于 iOS 7.1 不支持通过 HTTP 服务器在线安装IPA，我们需要修改其中的链接，将其改到 HTTPS 服务器上。请注意修改过的前缀和域名。

<pre lang="html">
<a href="itms-services://?action=download-manifest&url=https://my.HTTPS.server/myapp.plist"> Tap Here to Install My App</a>
</pre>

我们只需要把这个plist文件上传到HTTPS服务器即可，用户仍然可以访问原来的HTTP来进行下载。IPA文件也可以放在HTTP服务器上。

<pre lang="xml">
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>items</key>
	<array>
		<dict>
			<key>assets</key>
			<array>
				<dict>
					<key>kind</key>
					<string>software-package</string>
					<key>url</key>
					<string>http://my.http.server/myapp.ipa</string>
				</dict>
			</array>
			<key>metadata</key>
			<dict>
				<key>bundle-identifier</key>
				<string>us.1201.lulala</string>
				<key>bundle-version</key>
				<string>1.0</string>
				<key>kind</key>
				<string>software</string>
				<key>title</key>
				<string>My App</string>
			</dict>
		</dict>
	</array>
</dict>
</plist>
</pre>

而且，在 iOS 7.1 以下的系统中，也可以支持 HTTPS 服务器。所以这个修改对所有iOS系统都生效。

### 3. 使用免费的 HTTPS 服务器

如果你找不到拥有 HTTPS 服务器的土豪朋友，那么可以使用下面两种免费的方案：

* [DropBox][2] 这位美国土豪提供免费服务。只需要把你的plist文件传到 dropbox 的 public 共享区并 Copy public link 得到公共下载地址就行了。
* [七牛云存储][8] 这位中国土豪也支持 HTTPS ，服务同样免费，速度在国内访问更快，方法类似。

## 数字证书

由于考虑自己架设 HTTPS 服务器，于是找了一些数字证书的资料放在这里备查。

* 如果一定要使用自签名的证书或者来发布自己的App，那么可以要求iOS系统使用者在TA的设备上把你的证书加入信任。具体参考：[Adding CA Cert root and personal certificates to your iPhone, iPod or iPad][5] 。（对于苹果小白来说，这个很难...）
* 一些证书颁发机构可以免费申请30天、60天的试用证书，例如：[试用型 SSL 证书（Free Trial SSL）][6] 和 [StartSSL][7]。
* [Godaddy][9] 有一个面向开源项目1年免费的计划。
* 还有一些证书每年大约只要6刀，Google可以得到信息。
* 不要在国内的证书颁发机构购买证书。
* 不要信任CNNIC的根证书。

还有一些链接：

* [HTTPS][1]
* [Certificate authority][4]

[1]: http://zh.wikipedia.org/wiki/Https
[2]: https://db.tt/dPFwsNEj
[3]: https://portal.qiniu.com/
[4]: http://en.wikipedia.org/wiki/Certificate_authority
[5]: http://wiki.cacert.org/Technology/TechnicalSupport/EndUserSupport/OperatingSystems/iOS
[6]: http://cn.globalsign.com/ssl/ssl_freetrial_ssl.html
[7]: http://www.startssl.com/
[8]: https://portal.qiniu.com/signup?code=3lqod6o5frnf6
[9]: http://www.godaddy.com/ssl/ssl-open-source.aspx
