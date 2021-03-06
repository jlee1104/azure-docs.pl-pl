---
title: Rozwiązywanie problemów z połączeniami — usługa Azure Database for PostgreSQL — single server
description: Dowiedz się, jak rozwiązywać problemy z połączeniem z usługą Azure Database for PostgreSQL — Single Server.
keywords: połączenie postgresql,parametry połączenia,problemy z łącznością,błąd przejściowy,błąd połączenia
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c74b819893133116b8ac6905988f3fe11220ed95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770071"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Rozwiązywanie problemów z połączeniem z usługą Azure Database for PostgreSQL — single server

Problemy z połączeniem mogą być spowodowane przez różne rzeczy, w tym:

* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowe dane logowania
* Maksymalny limit osiągnięty w niektórych zasobach usługi Azure Database dla postgreSql
* Problemy z infrastrukturą usługi
* Konserwacja wykonywana w serwisie
* Alokacja obliczeniowa serwera jest zmieniana przez skalowanie liczby procesorów wirtualnych lub przejście do innej warstwy usług

Ogólnie rzecz biorąc problemy z połączeniem z usługą Azure Database for PostgreSQL można sklasyfikować w następujący sposób:

* Błędy przejściowe (krótkotrwałe lub przerywane)
* Błędy trwałe lub nieprzejściowe (błędy, które regularnie się powtarzają)

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów z błędami przejściowymi

Błędy przejściowe występują podczas konserwacji, system napotyka błąd ze sprzętem lub oprogramowaniem lub zmieniasz liczbę komputerów wirtualnych lub warstwę usług serwera. Usługa Azure Database for PostgreSQL ma wbudowaną wysoką dostępność i jest przeznaczona do automatycznego ograniczania tego typu problemów. Jednak aplikacja traci połączenie z serwerem na krótki okres czasu zazwyczaj mniej niż 60 sekund, co najwyżej. Niektóre zdarzenia mogą czasami trwać dłużej, aby ograniczyć, na przykład gdy duża transakcja powoduje długotrwałe odzyskiwanie.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki mające na celu rozwiązanie problemów z łącznością przejściową

1. Sprawdź [pulpit nawigacyjny usługi platformy Microsoft Azure pod](https://azure.microsoft.com/status) kątem wszelkich znanych awarii, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje, które łączą się z usługą w chmurze, takich jak Usługa Azure Database dla PostgreSQL należy spodziewać się błędów przejściowych i zaimplementować logikę ponawiania do obsługi tych błędów zamiast napawania ich jako błędy aplikacji dla użytkowników. Przejrzyj [obsługę błędów łączności przejściowej dla usługi Azure Database for PostgreSQL,](concepts-connectivity.md) aby uzyskać najlepsze rozwiązania i wskazówki dotyczące projektowania dotyczące obsługi błędów przejściowych.
3. Gdy serwer zbliża się do swoich limitów zasobów, błędy mogą wydawać się przejściowym problemem łączności. Zobacz [Ograniczenia w bazie danych platformy Azure dla PostgreSQL](concepts-limits.md).
4. Jeśli problemy z łącznością nadal lub jeśli czas trwania, dla którego aplikacja napotka błąd przekracza 60 sekund lub jeśli widzisz wiele wystąpień błędu w danym dniu, złóż żądanie pomocy technicznej platformy Azure, wybierając **pozycję Uzyskaj pomoc techniczną** w witrynie pomocy technicznej platformy [Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z błędami trwałymi

Jeśli aplikacja uporczywie nie może połączyć się z usługą Azure Database for PostgreSQL, zwykle wskazuje na problem z jednym z następujących czynności:

* Konfiguracja zapory serwera: Upewnij się, że zapora serwera Usługi Azure Database for PostgreSQL jest skonfigurowana tak, aby zezwalać na połączenia z klienta, w tym serwery proxy i bramy.
* Konfiguracja zapory klienta: zapora na kliencie musi zezwalać na połączenia z serwerem bazy danych. Adresy IP i porty serwera, których nie można zezwolić, a także nazwy aplikacji, takie jak PostgreSQL w niektórych zapór.
* Błąd użytkownika: Być może w nazwie użytkownika mogą być błędnie wpisywały parametry połączenia, takie jak nazwa serwera w ciągu połączenia lub brakujący * \@* sufiks nazwy serwera.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki mające na celu rozwiązanie problemów z trwałą łącznością

1. Skonfiguruj [reguły zapory,](howto-manage-firewall-using-portal.md) aby zezwolić na adres IP klienta. Tylko do celów testowania tymczasowego należy skonfigurować regułę zapory przy użyciu 0.0.0.0 jako początkowego adresu IP i używając 255.255.255.255 jako końcowego adresu IP. Spowoduje to otwarcie serwera na wszystkie adresy IP. Jeśli to rozwiąże problem z łącznością, usuń tę regułę i utwórz regułę zapory dla odpowiednio ograniczonego zakresu adresów IP lub adresów.
2. Na wszystkich zaporach między klientem a Internetem upewnij się, że port 5432 jest otwarty dla połączeń wychodzących.
3. Sprawdź parametry połączenia i inne ustawienia połączenia.
4. Sprawdź kondycję usługi na pulpicie nawigacyjnym. Jeśli uważasz, że wystąpiła awaria regionalna, zobacz [Omówienie ciągłości działania w usłudze Azure Database for PostgreSQL,](concepts-business-continuity.md) aby uzyskać instrukcje, aby odzyskać do nowego regionu.

## <a name="next-steps"></a>Następne kroki

* [Obsługa błędów łączności przejściowej dla usługi Azure Database dla postgreSQL](concepts-connectivity.md)
