---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: 3a63fd96b09910b0cd7ee8c3ab9947b034173c8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "71836291"
---
1. Określ subskrypcję, która została na białej liście dla plików NetApp platformy Azure:
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. Zarejestruj dostawcę zasobów platformy Azure: 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```