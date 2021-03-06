---
title: Dodawanie niestandardowego certyfikatu urzędu certyfikacji — usługa Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać niestandardowy certyfikat urzędu certyfikacji w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073600"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Jak dodać niestandardowy certyfikat urzędu certyfikacji w usłudze Azure API Management

Usługa Azure API Management umożliwia instalowanie certyfikatów urzędu certyfikacji na komputerze wewnątrz zaufanych magazynów certyfikatów głównych i pośrednich. Ta funkcja powinna być używana, jeśli usługi wymagają niestandardowego certyfikatu urzędu certyfikacji.

W tym artykule pokazano, jak zarządzać certyfikatami urzędu certyfikacji wystąpienia usługi usługi Azure API Management w witrynie Azure portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Przekazywanie certyfikatu urzędu certyfikacji

![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/00.png)

Wykonaj poniższe czynności, aby przekazać nowy certyfikat urzędu certyfikacji. Jeśli nie utworzono jeszcze wystąpienia usługi zarządzanie interfejsami API, zobacz samouczek [Tworzenie wystąpienia usługi zarządzania interfejsami API](get-started-create-service-instance.md).

1. Przejdź do wystąpienia usługi Azure API Management w witrynie Azure portal.

2. Z menu wybierz **certyfikaty urzędu** certyfikacji.

3. Kliknij przycisk **+ Dodaj**.  

    ![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/01.png)  

4. Wyszukaj certyfikat i zdecyduj o magazynie certyfikatów. Potrzebny jest tylko klucz publiczny, więc hasło nie jest wymagane.

    ![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/02.png)  

5. Kliknij przycisk **Zapisz**. Ta operacja może potrwać kilka minut.

    ![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Certyfikat urzędu certyfikacji można `New-AzApiManagementSystemCertificate` przekazać za pomocą polecenia programu Powershell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, kliknij menu kontekstowe **...** i wybierz polecenie **Usuń** obok certyfikatu.

![Usuwanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
