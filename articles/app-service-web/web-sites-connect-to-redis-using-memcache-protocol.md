---
title: "通过 Memcache 协议将 Azure 应用服务中的 Web 应用连接到 Redis 缓存 | Microsoft Docs"
description: "使用 Memcache 协议将 Azure App Service 中的 Web 应用连接到 Redis 缓存"
services: app-service\web
documentationcenter: php
author: SyntaxC4
manager: wpickett
editor: riande
ms.assetid: 0fcdf9fa-2995-4839-ba4d-cfa389c4ba06
ms.service: app-service-web
ms.devlang: php
ms.topic: get-started-article
ms.tgt_pltfrm: windows
ms.workload: na
ms.date: 02/29/2016
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f0321c71655f1b023862aeeef4615544135adb5a


---
# <a name="connect-a-web-app-in-azure-app-service-to-redis-cache-via-the-memcache-protocol"></a>通过 Memcache 协议将 Azure App Service 中的 Web 应用连接到 Redis 缓存
本文将介绍如何使用 [Memcach][13] 协议将 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714) 中的 WordPress Web 应用连接到 [Azure Redis 缓存][12]。 如果你有将 Memcached 服务器用于内存缓存的现有 Web 应用，则可以将其迁移到 Azure App Service，并使用 Microsoft Azure 中的第一方缓存解决方案，其中对你的应用程序代码进行少量更改或无更改。 还可利用现有的 Memcache 专业知识在 Azure App Service（带用于内部缓存的 Azure Redis 缓存）中创建高度可缩放的分布式应用，同时使用常用的应用程序框架，例如 .NET、PHP、Node.js、Java 和 Python。  

App Service Web Apps 使用 Web Apps Memcache 填充码启用该应用程序方案，这是充当 Memcache 代理服务器的本地 Memcached 服务器，用于缓存对 Azure Redis 缓存的调用。 这使得使用 Memcache 协议通信的任何应用可以使用 Redis 缓存缓存数据。 此 Memcache 填充码在协议级别运行，因此只要它使用 Memcached 协议通信，就可以供任何应用程序或应用程序框架使用。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="prerequisites"></a>先决条件
Web Apps Memcache 填充码可以与任何应用程序一起使用，前提是使用 Memcached 协议通信。 就此特定示例来说，引用应用程序是一个可缩放的 WordPress 站点，它可以从 Azure 应用商店设置。

请按照以下文章中所述的步骤操作：

* [预配 Azure Redis 缓存服务的实例][0]
* [在 Azure 中部署可缩放的 WordPress 站点][1]

部署可缩放的 WordPress 站点并设置 Redis 缓存实例后，你随时可以启用 Azure App Service Web Apps 中的 Memcache 填充码。

## <a name="enable-the-web-apps-memcache-shim"></a>启用 Web Apps Memcache 填充码
若要配置 Memcache 填充码，你必须创建三项应用设置。 这可以使用多种方法完成，其中包括 [Azure 门户](http://go.microsoft.com/fwlink/?LinkId=529715)、[经典门户][3]、[Azure PowerShell Cmdlet][5] 或 [Azure 命令行接口][5]。 本文将使用 [Azure 门户][4]来设置应用设置。 从 Redis 缓存实例的  “设置”边栏选项卡中，可以检索以下值。

![Azure Redis 缓存设置边栏选项卡](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### <a name="add-redishost-app-setting"></a>添加 REDIS_HOST 应用设置
需要创建的第一个应用设置是 **REDIS\_HOST** 应用设置。 此设置将对该填充码将缓存信息转发到的目的地进行设置。 REDIS_HOST 应用设置所需的值可以从 Redis 缓存实例的“属性”边栏选项卡中检索。

![Azure Redis 缓存主机名](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

将应用设置的密钥设置为 **REDIS\_HOST**，将应用设置的值设置为 Redis 缓存实例的**主机名**。

![Web 应用 AppSetting REDIS_HOST](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### <a name="add-rediskey-app-setting"></a>添加 REDIS_KEY 应用设置
需要创建的第二个应用设置是 **REDIS\_KEY** 应用设置。 此设置提供了安全访问 Redis 缓存实例所需的身份验证令牌。 可从 Redis 缓存实例的“访问密钥”边栏选项卡中检索 REDIS_KEY 应用设置所需的值。

![Azure Redis 缓存主密钥](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

将应用设置的密钥设置为 **REDIS\_KEY**，将应用设置的值设置为 Redis 缓存实例的**主密钥**。

![Azure 网站 AppSetting REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### <a name="add-memcacheshimredisenable-app-setting"></a>添加 MEMCACHESHIM_REDIS_ENABLE 应用设置
最后一个应用设置用于启用 Web Apps 中的 Memcache 填充码，以便使用 REDIS_HOST 和 REDIS_KEY 来连接到 Azure Redis 缓存并转发缓存调用。 将应用设置的密钥设置为 **MEMCACHESHIM\_REDIS\_ENABLE**，并将其值设置为 **true**。

![Web 应用 AppSetting MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

添加完这三 (3) 个应用设置后，单击“保存”。

## <a name="enable-memcache-extension-for-php"></a>启用针对 PHP 的 Memcache 扩展
若要让应用程序使用 Memcache 协议进行沟通，必须将 Memcache 扩展安装到 PHP（用于 WordPress 站点的语言框架）。

### <a name="download-the-phpmemcache-extension"></a>下载 php_memcache 扩展
浏览到 [PECL][6]。 在缓存类别下单击 [memcache][7]。 在下载列中，单击 DLL 链接。

![PHP PECL 网站](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

下载 Web Apps 中启用的 PHP 版本的非线性安全 (NTS) x86 链接。 （默认为 PHP 5.4）

![PHP PECL 网站 Memcache 包](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### <a name="enable-the-phpmemcache-extension"></a>启用 php_memcache 扩展
下载文件后，将 **php\_memcache.dll** 解压缩并上传到 **d:\\home\\site\\wwwroot\\bin\\ext\\** 目录。 将 php_memcache.dll 上载到 Web 应用后，你需要启用 PHP 运行时的扩展。 若要在 Azure 门户中启用 Memcache 扩展，请打开 Web 应用的“应用程序设置”边栏选项卡，然后添加密钥为 **PHP\_EXTENSIONS**和值为 **bin\\ext\\php_memcache.dll** 的新应用设置。

> [!NOTE]
> 如果 Web 应用需要加载多个 PHP 扩展，则 PHP_EXTENSIONS 的值应为由逗号分隔的 DLL 文件相对路径的列表。
> 
> 

![Web 应用 AppSetting PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

完成后，单击“保存”。

## <a name="install-memcache-wordpress-plugin"></a>安装 Memcache WordPress 插件
> [!NOTE]
> 你也可以从 WordPress.org 下载 [Memcached 对象缓存插件](https://wordpress.org/plugins/memcached/) 。
> 
> 

在 WordPress 插件页上，单击“添加新项” 。

![WordPress 插件页](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

在搜索框中，键入 **memcached**，然后按 **Enter**。

![WordPress 添加新项插件](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

在列表中查找“Memcached 对象缓存”，然后单击“立即安装”。

![WordPress 安装 Memcache 插件](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### <a name="enable-the-memcache-wordpress-plugin"></a>启用 Memcache WordPress 插件
> [!NOTE]
> 按照此博客中关于[如何启用 Web 应用中的站点扩展的说明][8]，安装 Visual Studio Team Services。
> 
> 

在 `wp-config.php` 文件中，将以下代码添加到该文件末尾附近停止编辑备注的上方。

```php
$memcached_servers = array(
    'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

粘贴此代码后，monaco 会自动保存该文档。

下一步是启用对象缓存插件。 操作方式是：将 **object-cache.php** 从 **wp-content/plugins/memcached** 文件夹拖放到 **wp-content** 文件夹，以启用 Memcache 对象缓存功能。

![定位 memcache object-cache.php 插件](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

现在，**object-cache.php** 文件位于 **wp-content** 文件夹中，Memcached 对象缓存已启用。

![启用 memcache object-cache.php 插件](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## <a name="verify-the-memcache-object-cache-plugin-is-functioning"></a>验证 Memcache 对象缓存插件是否正常运行
启用 Web Apps Memcache 填充码的所有步骤现已完成。 剩下的唯一一件事是，验证 Redis 缓存实例是否正在填充数据。

### <a name="enable-the-nonssl-port-support-in-azure-redis-cache"></a>启用 Azure Redis 缓存中的非 SSL 端口支持
> [!NOTE]
> 撰写本文时，Redis CLI 尚不支持 SSL 连接，因此以下步骤是必需的。
> 
> 

在 Azure 门户中，浏览到为此 Web 应用创建的 Redis 缓存实例。 打开缓存边栏选项卡后，单击“设置”  图标。

![Azure Redis 缓存设置按钮](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

从列表中选择“访问端口”。

![Azure Redis 缓存访问端口](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

单击“只允许通过 SSL 访问”旁边的“否”。

![Azure Redis 缓存访问端口仅限 SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

你将看到非 SSL 端口现在已设置。 单击“保存” 。

![Azure Redis 缓存 Redis 访问门户非 SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### <a name="connect-to-azure-redis-cache-from-rediscli"></a>从 redis-cli 连接到 Azure Redis Cache
> [!NOTE]
> 此步骤假定 redis 已通过本地方式安装在你的开发计算机上。 [按以下说明在本地安装 Redis][9]。
> 
> 

打开你选择的命令行控制台并键入以下命令：

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

将 **&lt;hostname-for-redis-cache&gt;** 替换为实际的 xxxxx.redis.cache.windows.net 主机名，将 **&lt;primary-key-for-redis-cache&gt;** 替换为缓存的访问密钥，然后按 **Enter**。 CLI 连接到 Redis 缓存实例之后，发出任何 Redis 命令。 在下面的屏幕截图中，我已选择列出密钥。

![在终端从 Redis CLI 连接到 Azure Redis 缓存](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

列出密钥的调用应返回一个值。 如果没有，请尝试导航到 Web 应用，然后再重试。

## <a name="conclusion"></a>结束语
祝贺你！ WordPress 应用现在有了集中式的内存中缓存，这将有助于提高吞吐量。 请记住，Web Apps Memcache 填充码可以与任何 Memcache 客户端结合使用，无需考虑编程语言或应用程序框架。 若要提供反馈或者提问有关 Web Apps Memcache 填充码的问题，请在 [MSDN 论坛][10]或 [Stackoverflow][11] 上发布相关文章。

> [!NOTE]
> 如果您想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到 [试用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，您可以在 App Service 中立即创建一个生存期较短的入门 Web 应用。 不需要使用信用卡，也不需要做出承诺。
> 
> 

## <a name="whats-changed"></a>发生的更改
* 有关从网站更改为应用服务的指南，请参阅 [Azure App Service 及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)

[0]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: ../powershell-install-configure.md
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org



<!--HONumber=Nov16_HO2-->


