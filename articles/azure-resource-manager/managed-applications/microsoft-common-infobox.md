---
title: InfoBox — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. InfoBox dla Azure Portal. Służy do dodawania tekstu lub ostrzeżeń podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652478"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft. Common. InfoBox — element interfejsu użytkownika

Kontrolka, która dodaje pole informacji. Pole zawiera ważny tekst lub ostrzeżenia, które pomagają użytkownikom zrozumieć, jakie wartości zapewniają. Może także połączyć się z identyfikatorem URI, aby uzyskać więcej informacji.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Schemat

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Uwagi

* W przypadku `icon`, użyj **braku**, **info**, **Warning**lub **Error**.
* Właściwość `uri` jest opcjonalna.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
