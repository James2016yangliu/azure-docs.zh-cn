---
title: "Azure AD Connect：将本地标识与 Azure Active Directory 集成 | Microsoft Docs"
description: "Azure AD Connect 会将你的本地目录与 Azure Active Directory 集成。 这样，你便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序提供一个通用标识。"
keywords: "Azure AD Connect 介绍, Azure AD Connect 概述, 什么是 Azure AD Connect, 安装 active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/04/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eedb788b2a174d01a2ef661cf4093ff938649bce


---
# <a name="integrating-your-onpremises-identities-with-azure-active-directory"></a>将本地标识与 Azure Active Directory 集成
Azure AD Connect 会将你的本地目录与 Azure Active Directory 集成。 这样，你便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序的用户提供一个通用标识。 本主题介绍计划、部署和操作步骤。 其中统合了与这些操作相关的主题的链接。

> [!IMPORTANT]
> [Azure AD Connect 是连接本地目录与 Azure AD 和 Office 365 的最佳方式。这是将 Azure AD Connect 从 Microsoft Azure Active Directory Sync (DirSync) 或 Azure AD Sync 升级的最佳时机，因为这些工具现已弃用，在 2017 年 4 月 13 日将结束支持。](active-directory-aadconnect-dirsync-deprecated.md)
> 
> 

![什么是 Azure AD Connect](./media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>为何使用 Azure AD Connect
将本地目录与 Azure AD 集成后，可以让用户使用通用标识访问位于云中和本地的资源，从而提高他们的生产率。 用户和组织可以享受到以下好处：

* 用户可以使用单个标识来访问本地应用程序和云服务，例如 Office 365。
* 单个工具即可提供轻松同步和登录的部署体验。
* 为方案提供最新功能。 Azure AD Connect 取代了 DirSync 和 Azure AD Sync 等早期版本的标识集成工具。 有关详细信息，请参阅 [混合标识目录集成工具比较](active-directory-hybrid-identity-design-considerations-tools-comparison.md)。

### <a name="how-azure-ad-connect-works"></a>Azure AD Connect 工作原理
Azure Active Directory Connect 由三个主要组件构成：同步服务、可选的 Active Directory 联合身份验证服务组件和名为 [Azure AD Connect Health](active-directory-aadconnect-health.md)的监视组件。

<center>![Azure AD Connect 堆栈](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* 同步 - 此组件负责创建用户、组和其他对象。 它还负责确保本地用户和组的标识信息与云匹配。
* AD FS - 联合身份验证是 Azure AD Connect 的可选部件，可用于使用本地 AD FS 基础结构配置混合环境。 组织可以使用此部件来解决复杂的部署，例如域加入 SSO、实施 AD 登录策略和智能卡或第三方 MFA。
* 运行状况监视 - Azure AD Connect Health 提供可靠监视，并在 Azure 门户中提供一个中心位置用于查看此活动。 有关更多信息，请参阅 [Azure Active Directory Connect Health](active-directory-aadconnect-health.md)。

## <a name="install-azure-ad-connect"></a>安装 Azure AD Connect
可以在 [Microsoft 下载中心](http://go.microsoft.com/fwlink/?LinkId=615771)找到 Azure AD Connect 的下载文件。

| 解决方案 | 方案 |
| --- | --- |
| 开始之前 - [硬件和先决条件](active-directory-aadconnect-prerequisites.md) |<li>开始安装 Azure AD Connect 之前所要完成的步骤。</li> |
| [快速设置](connect/active-directory-aadconnect-get-started-express.md) |<li>如果只有一个林 AD，我们建议使用此选项。</li> <li>使用密码同步以同一密码进行用户登录。</li> |
| [自定义设置](connect/active-directory-aadconnect-get-started-custom.md) |<li>有多个林时使用。 支持许多本地[拓扑](active-directory-aadconnect-topologies.md)。</li> <li>自定义登录选项，例如用于联合身份验证的 ADFS，或使用第三方标识提供者。</li> <li>自定义同步功能，例如筛选和写回。</li> |
| [从 DirSync 升级](connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>在已有 DirSync 服务器运行的情况下使用。</li> |
| [从 Azure AD Sync 或 Azure AD Connect 升级](active-directory-aadconnect-upgrade-previous-version.md) |<li>可以根据偏好选择多种不同的方法。</li> |

[安装后](active-directory-aadconnect-whats-next.md) ，你应该验证程序是否按预期工作，并将许可证分配给用户。

### <a name="next-steps-to-install-azure-ad-connect"></a>Azure AD Connect 安装后续步骤
| 主题 |
| --- | --- |
| 下载 Azure AD Connect |
| 使用快速设置安装 |
| 使用自定义设置安装 |
| 从 DirSync 升级 |
| 安装后 |

### <a name="learn-more-about-install-azure-ad-connect"></a>了解有关安装 Azure AD Connect 的详细信息
你还要预先了解 [操作](active-directory-aadconnectsync-operations.md) 注意事项。 可能要部署一台待机服务器，以便在发生 [灾难](active-directory-aadconnectsync-operations.md#disaster-recovery)时轻松故障转移。 如果要频繁进行配置更改，应该计划部署一台 [暂存模式](active-directory-aadconnectsync-operations.md#staging-mode) 服务器。

| 主题 |
| --- | --- |
| 支持的拓扑 |
| 设计概念 |
| 用于安装的帐户 |
| 操作规划 |
| 用户登录选项 |

## <a name="configure-sync-features"></a>配置同步功能
Azure AD Connect 随附了多个可以选择启用或已按默认启用的功能。 在某些方案和拓扑中，有些功能可能需要进行其他配置。

[筛选](active-directory-aadconnectsync-configure-filtering.md) 。 默认情况下，同步所有用户、联系人、组和 Windows 10 计算机。 可以根据域、OU 或属性更改筛选。

[密码同步](active-directory-aadconnectsync-implement-password-synchronization.md) 可将 Active Directory 中的密码哈希同步到 Azure AD。 最终用户可以在本地与云中使用相同的密码，且只需在一个位置管理此密码。 由于它将本地 Active Directory 用作颁发机构，因此你还可以使用自己的密码策略。

[密码写回](active-directory-passwords-getting-started.md) 可让用户在云中更改和重置其密码，及应用本地密码策略。

[设备写回](active-directory-aadconnect-feature-device-writeback.md) 可将 Azure AD 中注册的设备写回到本地 Active Directory，以便可以使用该设备进行条件性访问。

[防止意外删除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) 功能默认处于打开状态，它可以保护云目录，避免同时进行多次删除。 默认情况下，允许每次运行执行 500 次删除。 可以更改此设置，具体取决于组织规模。

[自动升级](active-directory-aadconnect-feature-automatic-upgrade.md) ，这可确保 Azure AD Connect 始终保持最新版本。

### <a name="next-steps-to-configure-sync-features"></a>同步功能配置后续步骤
| 主题 |
| --- | --- |
| 配置筛选 |
| 密码同步 |
| 密码写回 |
| 设备写回 |
| 防止意外删除 |
| 自动升级 |

## <a name="customize-azure-ad-connect-sync"></a>自定义 Azure AD Connect 同步
Azure AD Connect 同步随附一个适用于大部分客户和拓扑的默认配置。 但总会有一些情况使得默认配置不适用，因此必须进行调整。 你可以根据本部分和链接主题中所述进行更改。

如果你以前没有用过同步拓扑，请先了解 [技术概念](active-directory-aadconnectsync-technical-concepts.md)中所述的基本概念和术语。 Azure AD Connect 是在 MIIS2003、ILM2007 和 FIM2010 基础上演进而来的。 即使有些功能相同，但改变的部分也有很多。

[默认配置](active-directory-aadconnectsync-understanding-default-configuration.md) 假设配置中可能存在多个林。 在这些拓扑中，用户对象可能表示为另一个林中的联系人。 用户还可能在另一个资源林中具有链接的邮箱。 [用户和联系人](active-directory-aadconnectsync-understanding-users-and-contacts.md)中介绍了默认配置的行为。

同步的配置模型称为 [声明性预配](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)。 高级属性流程使用 [函数](active-directory-aadconnectsync-functions-reference.md) 来表示属性转换。 你可以使用 Azure AD Connect 随附的工具来检查整个配置。 如果需要进行配置更改，请确保遵循 [最佳做法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) ，以便可以更轻松地采用新版本。

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Azure AD Connect 同步自定义后续步骤
| 主题 |
| --- | --- |
| 所有 Azure AD Connect 同步文章 |
| 技术概念 |
| 了解默认配置 |
| 了解用户和联系人 |
| 声明性预配 |
| 更改默认配置 |

## <a name="configure-federation-features"></a>配置联合身份验证功能
可将 ADFS 配置为支持 [多个域](active-directory-aadconnect-multiple-domains.md)。 例如，你可能在联合身份验证功能中需要使用多个顶级域。

如果你的 ADFS 服务器未配置为自动更新 Azure AD 中的证书，或者如果你使用非 ADFS 解决方案，则在需要 [更新证书](active-directory-aadconnect-o365-certs.md)时会通知你。

### <a name="next-steps-to-configure-federation-features"></a>配置联合身份验证功能的后续步骤
| 主题 |
| --- | --- |
| 所有 AD FS 文章 |
| 配置带有子域的 ADFS |
| 管理 AD FS 场 |
| 手动更新联合身份验证证书 |

## <a name="more-information-and-references"></a>详细信息和参考
| 主题 |
| --- | --- |
| 版本历史记录 |
| 比较 DirSync、Azure ADSync 和 Azure AD Connect |
| Azure AD 的非 ADFS 兼容性列表 |
| 同步的属性 |
| 使用 Azure AD Connect Health 进行监视 |
| 常见问题 |

**其他资源**

有关将本地目录扩展到云的 Ignite 2015 演示文稿。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 




<!--HONumber=Nov16_HO2-->


