---
title: 'Skrypt programu PowerShell: tworzenie nowego udziału danych platformy Azure | Dokumenty firmy Microsoft'
description: Ten skrypt programu PowerShell tworzy nowy udział danych w ramach istniejącego konta udziału danych.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307259"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Tworzenie udziału danych na platformie Azure za pomocą programu PowerShell

Ten skrypt programu PowerShell tworzy nowy udział danych w ramach istniejącego konta udziału danych.

## <a name="sample-script"></a>Przykładowy skrypt

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Nowy-AzDataShare](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Tworzy udział danych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell udostępniania danych platformy Azure można znaleźć w [przykładach programu PowerShell udostępniania danych platformy Azure.](../../samples-powershell.md)
