---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70049023"
---
<!-- put the ## header in the file that includes this file -->

W tej sekcji utworzysz tożsamość urządzenia w rejestrze tożsamości w centrum IoT Hub. Urządzenie nie może połączyć się z koncentratorem, chyba że ma wpis w rejestrze tożsamości. Więcej informacji znajduje się w temacie [IoT Hub developer guide](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations) (Usługa IoT Hub — przewodnik dewelopera).

1. W menu nawigacyjnym centrum IoT otwórz pozycję **Urządzenia IoT**, a następnie wybierz pozycję **Nowy,** aby dodać urządzenie w centrum IoT hub.

    ![Tworzenie tożsamości urządzenia w portalu](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. W **obszarze Utwórz urządzenie**podaj nazwę nowego urządzenia, na przykład **myDeviceId**, i wybierz pozycję **Zapisz**. Ta akcja tworzy tożsamość urządzenia dla centrum IoT hub.

   ![Dodawanie nowego urządzenia](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Po utworzeniu urządzenia otwórz je z listy w okienku **Urządzenia IoT**. Skopiuj **podstawowy ciąg połączenia,** aby użyć go później.

    ![Parametry połączenia urządzenia](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. Przechowuje identyfikatory urządzeń i klucze, które będą używane jako poświadczenia zabezpieczeń, oraz flagę włączone/wyłączone, która umożliwia wyłączenie dostępu do poszczególnych urządzeń. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Więcej informacji znajduje się w temacie [IoT Hub Developer Guide](../articles/iot-hub/iot-hub-devguide-identity-registry.md) (Usługa IoT Hub — przewodnik dewelopera).
