---
title: Rozwiązywanie problemów z usługą Azure bastionu | Microsoft Docs
description: W tym artykule dowiesz się, jak rozwiązywać problemy z usługą Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512953"
---
# <a name="troubleshoot-azure-bastion"></a>Rozwiązywanie problemów z usługą Azure bastionu

W tym artykule opisano sposób rozwiązywania problemów z usługą Azure bastionu.

## <a name="nsg"></a>Nie można utworzyć elementu sieciowej grupy zabezpieczeń na AzureBastionSubnet

**P:** Gdy próbuję utworzyć sieciowej grupy zabezpieczeń w podsieci Azure bastionu, otrzymuję następujący błąd: *"sieciowa Grupa zabezpieczeń <NSG name> nie ma niezbędnych reguł dla usługi Azure bastionu Subnet AzureBastionSubnet"* .

Odp **.:** Jeśli utworzysz i zastosujesz sieciowej grupy zabezpieczeń do *AzureBastionSubnet*, upewnij się, że dodano następujące reguły w sieciowej grupy zabezpieczeń. Jeśli nie dodasz tych reguł, tworzenie sieciowej grupy zabezpieczeń/aktualizacja zakończy się niepowodzeniem.

1. Łączność między płaszczyznami kontrolnymi — ruch przychodzący na 443 z bramy
2. Rejestrowanie diagnostyczne i inne — ruch wychodzący w dniu 443 do AzureCloud (Tagi regionalne w tym tagu usługi nie są jeszcze obsługiwane).
3. Docelowa maszyna wirtualna — ruch wychodzący dla 3389 i 22 do VirtualNetwork

Przykład reguł sieciowej grupy zabezpieczeń jest dostępny dla celów referencyjnych w [szablonie szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące sieciowej grupy zabezpieczeń dla usługi Azure bastionu](bastion-nsg.md).

## <a name="sshkey"></a>Nie można użyć mojego klucza SSH z usługą Azure bastionu

**P:** Gdy próbuję przeglądać mój plik klucza SSH, otrzymuję następujący komunikat o błędzie: *"klucz prywatny SSH musi zaczynać się od-----rozpocznij-----klucza prywatnego RSA i kończy się na-----końcowym kluczu prywatnym rsa-----"* .

Odp **.:** Usługa Azure bastionu obsługuje tylko klucze SSH RSA, w tym momencie. Upewnij się, że przeszukiwany jest klucz prywatny RSA dla SSH, z kluczem publicznym zainicjowanym na docelowej maszynie wirtualnej. 

Na przykład można użyć następującego polecenia, aby utworzyć nowy klucz SSH RSA:

**ssh-keygen-t RSA-b 4096-C "email@domain.com"**

Dane wyjściowe:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="domain"></a>Nie można zalogować się do maszyny wirtualnej z systemem Windows przyłączonym do domeny

**P:** Nie mogę nawiązać połączenia z maszyną wirtualną z systemem Windows, która jest przyłączona do domeny.

Odp **.:** Usługa Azure bastionu obsługuje logowanie za pomocą maszyny wirtualnej przyłączonej do domeny w przypadku logowania za pomocą hasła użytkownika w domenie. Podczas określania poświadczeń domeny w Azure Portal należy zalogować się przy użyciu formatu UPN (username@domain) zamiast formatu *DOMENA\nazwa* użytkownika. Jest to obsługiwane w przypadku maszyn wirtualnych przyłączonych do domeny lub przyłączonych do hybryd (zarówno przyłączonych do domeny, jak i przyłączonych do usługi Azure AD). Nie jest obsługiwana w przypadku maszyn wirtualnych obsługujących tylko usługę Azure AD.

## <a name="filetransfer"></a>Problemy dotyczące transferu plików

**P:** Czy transfer plików jest obsługiwany przez usługę Azure bastionu?

Odp **.:** Transfer plików nie jest w tej chwili obsługiwany. Pracujemy nad dodaniem obsługi.

## <a name="blackscreen"></a>Czarny ekran w Azure Portal

**P:** Podczas próby nawiązania połączenia przy użyciu usługi Azure bastionu pojawia się czarny ekran w Azure Portal.

Odp **.:** Dzieje się tak, gdy występuje problem z łącznością sieciową między przeglądarką sieci Web i usługą Azure bastionu (Zapora internetowa klienta może blokować ruch usługi WebSockets lub podobną) lub między usługą Azure bastionu i docelową maszyną wirtualną. Większość przypadków obejmuje sieciowej grupy zabezpieczeń zastosowany do AzureBastionSubnet lub w docelowej podsieci maszyny wirtualnej, która blokuje ruch RDP/SSH w sieci wirtualnej. Zezwól na ruch w sieci WebSockets w zaporze internetowej klienta i sprawdź sieciowych grup zabezpieczeń w docelowej podsieci maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [często zadawane pytania](bastion-faq.md)dotyczące usługi bastionu.