中国电信物联网开放平台API（北向）对SDK
=======================

<p align="center">
<a href="https://packagist.org/packages/zeevin/libiocm"><img src="https://poser.pugx.org/zeevin/libiocm/d/total.svg" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/zeevin/libiocm"><img src="https://poser.pugx.org/zeevin/libiocm/v/stable.svg" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/zeevin/libiocm"><img src="https://poser.pugx.org/zeevin/libiocm/license.svg" alt="License"></a>
</p>

Libiocm 实现了对中国电信物联网开发平台（北向）API的对接。

### 关于NB-IoT的开发，有兴趣的朋友可以加QQ群交流：群号:577640752

#### 版本说明

```text
master 分支正在迭代开发，对应电信API的版本是1.5.1
对接进度：
2.1 应用安全接入
 2.1.1 鉴权 Zeevin\Libiocm\Sec\LoginClient
 2.1.2 刷新token Zeevin\Libiocm\Sec\RefreshTokenClient

2.2 设备管理
 2.2.1 注册直连设备 Zeevin\Libiocm\Reg\RegClient
 2.2.2 刷新设备密钥 Zeevin\Libiocm\Reg\RefreshKeyClient
 2.2.3 修改设备信息 Zeevin\Libiocm\Dm\UpdateDeviceClient

```

```text
SDK 0.5.0 版本对接的电信API版本是1.1.3，并且暂未实现如下接口：

1.2.1.3 注销
1.2.2.2 发现非直连设备
1.2.2.8 设置加密
1.2.6 批量处理
1.2.7 规则 

因此版本电信已经不再维护，不推进再使用
```


安装：
```bash
composer require zeevin/libiocm
```

必须操作：

添加一个自定义的autoload.php文件，自己的程序调动的时候引用此autoload.php,不要直接引用vendor/autoload.php内容如下：
```php
use Doctrine\Common\Annotations\AnnotationRegistry;

$loader = require_once '../vendor/autoload.php';
AnnotationRegistry::registerLoader([$loader, 'loadClass']);
return $loader;
```

或者修改vendor/autoload.php 为如下样式(每次执行composer命令后都需要重新编辑)：
```php
// autoload.php @generated by Composer
use Doctrine\Common\Annotations\AnnotationRegistry;

require_once __DIR__ . '/composer/autoload_real.php';

$loader = ComposerAutoloaderInitb6ddad78dfb081b4ad47d02feb034c25::getLoader();
AnnotationRegistry::registerLoader([$loader, 'loadClass']);

return $loader;
```

主要完成如下内容：

- 1、使用JMS包装请求参数。以定义类变量的方式设置请求参数，便于参数的设置和参数提示。
- 2、使用JMS包装返回的json数据，默认会把请求数据映射为对应的RequestAttribute类，便于进一步使用。
- 3、使用doctrine/cache 缓存ouath token结果。目前适配了memcached、Redis、file三类。

注意事项：
- 1、Guzzle 库只支持pem格式的证书，因此需要将默认的p12格式证书转换成pem格式，比如：
```bash
openssl pkcs12 -in outgoing.CertwithKey.pkcs12 -out key.pem -nodes -clcerts
```
目前测试平台的证书密码是：IoM@1234，如果后期电信有更新需要同步更新。

- 2、电信编写的api文档看起来并不十分完善，有些返回信息是从头信息中查看，比如1.2.2.4 删除直连设备接口，
此接口在删除成功时返回结果需要查看头信息的statusCode和reasonPhrase内容，body中并没有json信息，为解决这种问题，
我在所有的请求结果中都加入了"statusCode"和"reasonPhrase"属性。

- 3、接口调用方式请查看tests文件夹下的示例。
