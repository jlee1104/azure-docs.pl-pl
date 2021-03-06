---
title: Omówienie interfejsów API węzła Azure Relay | Microsoft Docs
description: Ten artykuł zawiera omówienie interfejsu API środowiska Node. js dla usługi Azure Relay. Przedstawiono w nim również, jak używać pakietu węzła Hyco-WS.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 2877284c419da4999e23490fc986e5da44e5d92e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514514"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Omówienie interfejsu API usługi Relay Połączenia hybrydowe Node

## <a name="overview"></a>Przegląd

Pakiet węzła [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) dla Azure Relay połączenia hybrydowe jest oparty i rozszerza pakiet npm ["ws"](https://www.npmjs.com/package/ws) . Ten pakiet ponownie eksportuje wszystkie eksporty tego pakietu podstawowego i dodaje nowe eksporty, które umożliwiają integrację z funkcją Azure Relay Połączenia hybrydowe Service. 

Istniejące aplikacje, które `require('ws')` mogą używać tego pakietu z `require('hyco-ws')` zamiast tego, które umożliwiają również scenariusze hybrydowe, w których aplikacja może nasłuchiwać połączeń protokołu WebSocket lokalnie z "wewnątrz zapory" i przez Połączenia hybrydowe wszystkie w tym samym czasie.
  
## <a name="documentation"></a>Dokumentacja

Interfejsy API są [udokumentowane w głównym pakiecie "ws"](https://github.com/websockets/ws/blob/master/doc/ws.md). W tym artykule opisano, jak ten pakiet różni się od tej linii bazowej. 

Kluczowe różnice między pakietem podstawowym a tym "Hyco-WS" jest dodanie nowej klasy serwera, wyeksportowanej przez `require('hyco-ws').RelayedServer`i kilku metod pomocnika.

### <a name="package-helper-methods"></a>Metody pomocnika pakietu

Istnieje kilka metod narzędzi dostępnych w ramach eksportu pakietu, do których można się odwołać w następujący sposób:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Metody pomocnika są przeznaczone do użycia z tym pakietem, ale mogą być również używane przez serwer węzła do umożliwienia klientom sieci Web lub do tworzenia odbiorników lub nadawców. Serwer używa tych metod przez przekazanie ich identyfikatorów URI, które osadzają tokeny krótkotrwałe. Te identyfikatory URI mogą być również używane z typowymi stosami protokołu WebSocket, które nie obsługują ustawiania nagłówków HTTP dla uzgadniania protokołu WebSocket. Osadzanie tokenów autoryzacji w identyfikatorze URI jest obsługiwane głównie dla tych scenariuszy użycia w bibliotece. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Tworzy prawidłowy identyfikator URI odbiornika połączenia hybrydowego Azure Relay dla danego obszaru nazw i ścieżki. Ten identyfikator URI może być następnie używany z wersją przekaźnika klasy WebSocketServer.

- `namespaceName` (wymagane) — kwalifikowana dla domeny nazwa przestrzeni nazw Azure Relay, która ma być używana.
- `path` (wymagane) — nazwa istniejącego Azure Relay połączenia hybrydowego w tej przestrzeni nazw.
- `token` (opcjonalnie) — wcześniej wystawiony token dostępu przekaźnikowego, który jest osadzony w identyfikatorze URI odbiornika (zobacz Poniższy przykład).
- `id` (opcjonalnie) — identyfikator śledzenia, który umożliwia kompleksowe śledzenie procesów diagnostycznych.

Wartość `token` jest opcjonalna i powinna być używana tylko wtedy, gdy nie jest możliwe wysyłanie nagłówków HTTP wraz z uzgadnianiem protokołu WebSocket, podobnie jak w przypadku stosu protokołu WebSocket w formacie W3C.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Tworzy prawidłowy identyfikator URI Azure Relay wysyłania połączenia hybrydowego dla danego obszaru nazw i ścieżki. Ten identyfikator URI może być używany z dowolnym klientem protokołu WebSocket.

- `namespaceName` (wymagane) — kwalifikowana dla domeny nazwa przestrzeni nazw Azure Relay, która ma być używana.
- `path` (wymagane) — nazwa istniejącego Azure Relay połączenia hybrydowego w tej przestrzeni nazw.
- `token` (opcjonalnie) — wcześniej wystawiony token dostępu przekaźnikowego, który został osadzony w wysyłającym identyfikatorze URI (zobacz Poniższy przykład).
- `id` (opcjonalnie) — identyfikator śledzenia, który umożliwia kompleksowe śledzenie procesów diagnostycznych.

Wartość `token` jest opcjonalna i powinna być używana tylko wtedy, gdy nie jest możliwe wysyłanie nagłówków HTTP wraz z uzgadnianiem protokołu WebSocket, podobnie jak w przypadku stosu protokołu WebSocket w formacie W3C.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Tworzy token sygnatury dostępu współdzielonego (SAS) Azure Relay dla danego docelowego identyfikatora URI, reguły SAS i klucza reguły SAS, który jest ważny przez daną liczbę sekund lub godzinę od bieżącego momentu, gdy argument wygaśnięcia zostanie pominięty.

- `uri` (wymagane) — identyfikator URI, dla którego ma zostać wystawiony token. Identyfikator URI jest znormalizowany do korzystania ze schematu HTTP, a informacje o ciągu zapytania są usuwane.
- `ruleName` (wymagane) — Nazwa reguły SAS dla jednostki reprezentowanej przez dany identyfikator URI lub dla przestrzeni nazw reprezentowanej przez część hosta identyfikatora URI.
- `key` (wymagane) — prawidłowy klucz dla reguły sygnatury dostępu współdzielonego. 
- `expirationSeconds` (opcjonalnie) — liczba sekund do wygaśnięcia wygenerowanego tokenu. Jeśli nie zostanie określony, wartość domyślna to 1 godzina (3600).

Wystawiony token przyznaje prawa skojarzone z określoną regułą SAS dla danego czasu trwania.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Ta metoda jest funkcjonalnie równoważna metodzie `createRelayToken` udokumentowanej wcześniej, ale zwraca token prawidłowo dołączony do wejściowego identyfikatora URI.

### <a name="class-wsrelayedserver"></a>Klasa WS. RelayedServer

Klasa `hycows.RelayedServer` jest alternatywą dla klasy `ws.Server`, która nie nasłuchuje w sieci lokalnej, ale deleguje dostęp do usługi Azure Relay.

Dwie klasy są najczęściej zgodne z umową, co oznacza, że istniejąca Aplikacja korzystająca z klasy `ws.Server` można łatwo zmienić, aby używać przekazanej wersji. Główne różnice znajdują się w konstruktorze i dostępnych opcjach.

#### <a name="constructor"></a>Konstruktor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

Konstruktor `RelayedServer` obsługuje inny zestaw argumentów niż `Server`, ponieważ nie jest odbiornikiem autonomicznym lub może być osadzony w istniejącej platformie odbiornika HTTP. Dostępne są również mniej opcji, ponieważ zarządzanie przy użyciu protokołu WebSocket jest w dużym stopniu delegowane do usługi przekazywania.

Argumenty konstruktora:

- `server` (wymagane) — w pełni kwalifikowany identyfikator URI dla nazwy połączenia hybrydowego, na którym nasłuchuje, zwykle skonstruowany za pomocą metody pomocnika WebSocket. createRelayListenUri ().
- `token` (wymagane) — ten argument zawiera wcześniej wystawiony ciąg tokenu lub funkcję wywołania zwrotnego, która może zostać wywołana w celu uzyskania takiego ciągu tokenu. Opcja wywołania zwrotnego jest preferowana, ponieważ umożliwia odnowienie tokenu.

#### <a name="events"></a>Wydarzenia

wystąpienia `RelayedServer` emitują trzy zdarzenia, które umożliwiają obsługę żądań przychodzących, ustanawianie połączeń i wykrywanie warunków błędu. Musisz subskrybować zdarzenie `connect`, aby obsługiwać komunikaty. 

##### <a name="headers"></a>nagłówki

```JavaScript 
function(headers)
```

Zdarzenie `headers` jest wywoływane tuż przed zaakceptowaniem połączenia przychodzącego, co umożliwia modyfikację nagłówków do wysłania do klienta. 

##### <a name="connection"></a>połączenie

```JavaScript
function(socket)
```

Emitowane po zaakceptowaniu nowego połączenia z użyciem protokołu WebSocket. Obiekt jest typu `ws.WebSocket`, tak samo jak z pakietem podstawowym.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Jeśli serwer źródłowy emituje błąd, jest przekazywany w tym miejscu.  

#### <a name="helpers"></a>Pomocnicy

W celu uproszczenia uruchamiania przekazywanego serwera i natychmiastowego subskrybowania połączeń przychodzących pakiet ujawnia prostą funkcję pomocnika, która również jest używana w przykładach w następujący sposób:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Ta metoda wywołuje konstruktora w celu utworzenia nowego wystąpienia RelayedServer, a następnie subskrybuje podane wywołanie zwrotne do zdarzenia "Connection".
 
##### <a name="relayedconnect"></a>relayedConnect

Po prostu dublowanie `createRelayedServer` pomocnika w funkcji `relayedConnect` tworzy połączenie z klientem i subskrybuje zdarzenie "Open" w wytworzonym gnieździe.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Azure Relay, odwiedź następujące linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Dostępne interfejsy API przekazywania](relay-api-overview.md)
