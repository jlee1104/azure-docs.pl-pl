---
title: Omówienie interfejsu API Azure Relay | Microsoft Docs
description: Ten artykuł zawiera omówienie dostępnych Azure Relay interfejsów API (.NET Standard, .NET Framework, Node. js itp.).
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 41d9e2026c19c959dc6fe2546b0ef699571ec7cd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513970"
---
# <a name="available-relay-apis"></a>Dostępne interfejsy API przekazywania

## <a name="runtime-apis"></a>Interfejsy API środowiska uruchomieniowego

W poniższej tabeli wymieniono wszystkie aktualnie dostępne klientów środowiska uruchomieniowego przekaźnika.

Sekcja [dodatkowe informacje](#additional-information) zawiera więcej informacji o stanie każdej biblioteki środowiska uruchomieniowego.

| Język/platforma | Dostępna funkcja | Pakiet klienta | Repozytorium |
| --- | --- | --- | --- |
| .NET Standard | Hybrydowe | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF Relay | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | ND |
| Węzeł | Hybrydowe | [Obiekty WebSockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Obiekty WebSockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Żądania HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Dodatkowe informacje

#### <a name="net"></a>.NET

Ekosystem .NET ma wiele środowisk uruchomieniowych, dlatego istnieje wiele bibliotek .NET dla przekaźnika. Bibliotekę .NET Standard można uruchomić przy użyciu platformy .NET Core lub .NET Framework, podczas gdy biblioteka .NET Framework może być uruchamiana tylko w środowisku .NET Framework. Aby uzyskać więcej informacji na temat platformy .NET Framework, zobacz [wersje architektury](/dotnet/articles/standard/frameworks).

Biblioteka .NET Framework obsługuje tylko model programowania WCF i opiera się na zastrzeżonym protokole binarnym opartym na transportie `net.tcp` WCF. Ten protokół i Biblioteka są utrzymywane w celu zapewnienia zgodności z poprzednimi wersjami z istniejącymi aplikacjami.

Biblioteka .NET Standard jest oparta na otwartej definicji protokołu dla przekaźnika Połączenia hybrydowe, który kompiluje w przypadku połączeń HTTP i WebSockets. Biblioteka obsługuje abstrakcję strumienia za pośrednictwem obiektów WebSockets i prostego gestu interfejsu API odpowiedzi na żądanie w celu odpowiedzi na żądania HTTP. Przykład [interfejsu Web API](https://github.com/Azure/azure-relay-dotnet) pokazuje, jak zintegrować Połączenia hybrydowe z ASP.NET Core dla usług sieci Web.

#### <a name="nodejs"></a>Node.js

Moduły Połączenia hybrydowe wymienione w powyższej tabeli zastępują lub zmieniają istniejące Moduły Node. js z alternatywnymi implementacjami, które nasłuchują w usłudze Azure Relay, a nie na stosie sieci lokalnych.

Moduł `hyco-https` zmienia i częściowo zastępuje podstawowe Moduły Node. js `http` i `https`, dostarczając implementację odbiornika HTTPS zgodną z wieloma istniejącymi modułami Node. js i aplikacjami zależnymi od tych modułów podstawowych.

Moduły `hyco-ws` i `hyco-websocket` zmieniają popularne moduły `ws` i `websocket` dla środowiska Node. js, zapewniając alternatywne implementacje odbiorników, które umożliwiają modułom i aplikacjom korzystanie z każdego z modułów, aby działały za Połączenia hybrydowe Relay.

Szczegółowe informacje o tych modułach znajdują się w repozytorium GitHub [Azure-Relay-Node](https://github.com/Azure/azure-relay-node) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Relay, odwiedź następujące linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
