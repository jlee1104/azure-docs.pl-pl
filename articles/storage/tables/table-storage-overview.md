---
title: Wprowadzenie do Magazynu tabel — magazyn obiektów na platformie Azure | Microsoft Docs
description: Przechowywanie danych strukturalnych w chmurze za pomocą Magazynu tabel Azure, magazyn danych NoSQL.
services: storage
author: SnehaGunda
ms.service: storage
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c850d2b01e098a10aacf383f0ed4340cb9e64e10
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129790"
---
# <a name="introduction-to-table-storage-in-azure"></a>Wprowadzenie do Magazynu tabel na platformie Azure

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage to usługa, która przechowuje dane NoSQL ze strukturą w chmurze, udostępniając magazyn par klucz-atrybut z projektem bez schematu. Ponieważ Magazyn tabel nie ma schematu, łatwo zaadaptować dane do rozwijających się potrzeb aplikacji. Dla większości aplikacji dostęp do danych w usłudze Table Storage jest szybki i ekonomiczny, jest też zazwyczaj tańszy od tradycyjnego rozwiązania SQL dla podobnych ilości danych.

Usługa Table Storage umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkowników dla aplikacji internetowych, książki adresowe, informacje o urządzeniach i inne typy metadanych, których wymaga Twoja usługa. W tabeli można przechowywać dowolną liczbę jednostek, a konto magazynu może zawierać dowolną liczbę tabel w granicach pojemności konta magazynu.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.

* [Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)

* Przejrzyj dokumentację referencyjną usługi Table service, aby uzyskać szczegółowe informacje o dostępnych interfejsach API:

    * [Dokumentacja biblioteki klienta usługi Storage dla programu .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [Dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dd179355)
