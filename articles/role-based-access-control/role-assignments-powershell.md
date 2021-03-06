---
title: Dodawanie lub usuwanie przypisań ról za pomocą funkcji RBAC i azure powershell
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure użytkownikom, grupom, jednostkom usług lub tożsamościom zarządzanym przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC) i programu Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283214"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i azure powershell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]W tym artykule opisano sposób przypisywania ról przy użyciu programu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnień, takich jak [Administrator dostępu do użytkownika](built-in-roles.md#user-access-administrator) lub [Właściciel](built-in-roles.md#owner)
- [Program PowerShell w usłudze Azure Cloud Shell](/azure/cloud-shell/overview) lub [usłudze Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Pobierz identyfikatory obiektów

Aby dodać lub usunąć przypisania ról, może być konieczne określenie unikatowego identyfikatora obiektu. Identyfikator ma format: `11111111-1111-1111-1111-111111111111`. Identyfikator można uzyskać za pomocą witryny Azure portal lub programu Azure PowerShell.

### <a name="user"></a>Użytkownik

Aby uzyskać identyfikator obiektu dla użytkownika usługi Azure AD, można użyć [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Grupa

Aby uzyskać identyfikator obiektu dla grupy usługi Azure AD, można użyć [get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Aplikacja

Aby uzyskać identyfikator obiektu dla jednostki usługi Azure AD (tożsamość używana przez aplikację), można użyć [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Dla jednostki usługi należy użyć identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W RBAC, aby udzielić dostępu, należy dodać przypisanie roli.

### <a name="user-at-a-resource-group-scope"></a>Użytkownik w zakresie grupy zasobów

Aby dodać przypisanie roli dla użytkownika w zakresie grupy zasobów, użyj funkcji [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Używanie unikatowego identyfikatora roli

Istnieje kilka razy, gdy nazwa roli może ulec zmianie, na przykład:

- Używasz własnej roli niestandardowej i zdecydujesz się zmienić nazwę.
- Używasz roli podglądu, która ma **(Podgląd)** w nazwie. Po zwolnieniu roli rola zostanie zmieniona.

> [!IMPORTANT]
> Wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nawet jeśli nazwa roli zostanie zmieniona, identyfikator roli nie ulegnie zmianie. Jeśli używasz skryptów lub automatyzacji do tworzenia przypisań ról, najlepszym rozwiązaniem jest użycie unikatowego identyfikatora roli zamiast nazwy roli. W związku z tym jeśli rola zostanie zmieniona, skrypty są bardziej prawdopodobne, aby działać.

Aby dodać przypisanie roli przy użyciu unikatowego identyfikatora roli zamiast nazwy roli, użyj [funkcji New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

Poniższy przykład przypisuje rolę [współautora maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do *\@alain example.com* użytkownika w zakresie grupy zasobów *sprzedaży farmaceutycznej.* Aby uzyskać unikatowy identyfikator roli, można użyć [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) lub zobacz [Wbudowane role dla zasobów platformy Azure.](built-in-roles.md)

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Grupowanie w zakresie zasobu

Aby dodać przypisanie roli dla grupy w zakresie zasobów, użyj funkcji [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Aby uzyskać informacje o tym, jak uzyskać identyfikator obiektu grupy, zobacz [Pobierz identyfikatory obiektów](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Aplikacja w zakresie subskrypcji

Aby dodać przypisanie roli dla aplikacji w zakresie subskrypcji, użyj [new-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Aby uzyskać informacje o tym, jak uzyskać identyfikator obiektu aplikacji, zobacz [Pobierz identyfikatory obiektów](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Użytkownik w zakresie grupy zarządzania

Aby dodać przypisanie roli dla użytkownika w zakresie grupy zarządzania, użyj [funkcji New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **Grupy zarządzania** w witrynie Azure portal lub można użyć [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W RBAC, aby usunąć dostęp, należy usunąć przypisanie roli za pomocą [usuń AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Poniższy przykład usuwa przypisanie roli *współautora maszyny wirtualnej* z *\@alain example.com* użytkownika w grupie zasobów *sprzedaży farmacji:*

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Poniższy przykład usuwa <role_name> roli z <object_id> w zakresie subskrypcji.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

Poniższy przykład usuwa <role_name> rolę z <object_id> w zakresie grupy zarządzania.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Jeśli zostanie wyświetlony komunikat o błędzie: "Podane informacje nie są mapowane na `-Scope` `-ResourceGroupName` przypisanie roli", upewnij się, że określono również parametry lub. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z programem RBAC dla zasobów platformy Azure](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Następne kroki

- [Lista przypisań ról przy użyciu usługi Azure RBAC i azure powershell](role-assignments-list-powershell.md)
- [Samouczek: Udzielanie grupowego dostępu do zasobów platformy Azure przy użyciu funkcji RBAC i programu Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Samouczek: Tworzenie roli niestandardowej dla zasobów platformy Azure przy użyciu programu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Zarządzanie zasobami za pomocą programu Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
