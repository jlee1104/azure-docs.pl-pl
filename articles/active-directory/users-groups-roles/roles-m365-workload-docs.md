---
title: Dokumentacja roli administratora w usłudze Microsoft 365 Services — Azure AD | Microsoft Docs
description: Znajdź zawartość i informacje dotyczące interfejsów API dla ról administratorów dla usług Microsoft 365 w programie Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024459"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Role administratorów dla usług Microsoft 365 Services

Wszystkie produkty w Microsoft 365 mogą być zarządzane przy użyciu ról administracyjnych w usłudze Azure AD. Niektóre produkty udostępniają także dodatkowe role, które są specyficzne dla danego produktu. Informacje o rolach obsługiwanych przez poszczególne produkty znajdują się w poniższej tabeli. Ogólne dyskusje dotyczące problemów z delegowaniem można znaleźć w temacie [Planowanie delegowania ról w Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Gdzie można znaleźć zawartość

Usługa Microsoft 365 | Zawartość roli | Zawartość interfejsu API
---------------------- | ------------------ | -----------------
Role administratora w pakietach Office 365 i Microsoft 365 planach biznesowych | [Role administratora usługi Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Niedostępne
Azure Active Directory (Azure AD) i Azure AD Identity Protection| [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Kontrola dostępu oparta na rolach programu Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Program PowerShell dla programu Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Role administratora usługi Azure AD](directory-assign-admin-roles.md)<br>Także [rola administratora programu SharePoint w pakiecie Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Zespoły/Skype dla firm | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Centrum zabezpieczeń i zgodności (usługa Office 365 Advanced Threat Protection, Ochrona usługi Exchange Online, Information Protection) | [Role administratora usługi Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Program Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Wynik zabezpieczony | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Menedżer zgodności | [Role Menedżera zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Niedostępne
Azure Information Protection | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Dokumentacja interfejsu API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Grupy ról usługi Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Niedostępne
Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender | [Kontrola dostępu oparta na rolach w usłudze Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Niedostępne
Privileged Identity Management | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Usługa Intune | [Kontrola dostępu oparta na rolach usługi Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Pulpit zarządzany | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Następne kroki

* [Jak przypisać lub usunąć role administratorów usługi Azure AD](directory-manage-roles-portal.md)
* [Informacje o rolach administratorów usługi Azure AD](directory-assign-admin-roles.md)
