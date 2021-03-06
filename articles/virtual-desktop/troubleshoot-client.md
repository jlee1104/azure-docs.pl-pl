---
title: Rozwiązywanie problemów z pulpitem wirtualnym pulpitu pulpitu wirtualnego pulpitu pulpitu pulpitu pulpitu systemu Windows — Azure
description: Jak rozwiązać problemy podczas konfigurowania połączeń klientów w środowisku dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127510"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Rozwiązywanie problemów z klientem pulpitu zdalnego

W tym artykule opisano typowe problemy z klientem pulpitu zdalnego i sposób ich rozwiązywania.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Klient pulpitu zdalnego dla systemu Windows 7 lub Windows 10 przestaje odpowiadać lub nie można go otworzyć

Użyj następujących poleceń cmdlet programu PowerShell, aby wyczyścić rejestry klientów poza pasmem (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Przejdź do **pozycji %AppData%\RdClientRadc** i usuń całą zawartość.

Odinstaluj i zainstaluj ponownie klienta pulpitu zdalnego dla systemów Windows 7 i Windows 10.

## <a name="web-client-wont-open"></a>Klient sieci Web nie zostanie otwarty

Najpierw przetestuj połączenie internetowe, otwierając inną stronę internetową w przeglądarce; na przykład [www.bing.com](https://www.bing.com).

Użyj **nslookup,** aby potwierdzić, że usługa DNS może rozwiązać problem nazwy FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Spróbuj połączyć się z innym klientem, takim jak klient pulpitu zdalnego dla systemu Windows 7 lub Windows 10, i sprawdź, czy możesz otworzyć klienta sieci Web.

### <a name="opening-another-site-fails"></a>Otwarcie innej witryny kończy się niepowodzeniem

Jest to zwykle spowodowane problemami z połączeniem sieciowym lub awarią sieci. Zalecamy skontaktowanie się z pomocą techniczną sieci.

### <a name="nslookup-cannot-resolve-the-name"></a>Funkcja Nslookup nie może rozpoznać nazwy

Jest to zwykle spowodowane problemami z połączeniem sieciowym lub awarią sieci. Zalecamy skontaktowanie się z pomocą techniczną sieci.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Klient nie może się połączyć, ale inni klienci w sieci mogą się połączyć

Jeśli przeglądarka zaczyna działać lub przestaje działać podczas korzystania z klienta internetowego, postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać ten problem:

1. Uruchom ponownie przeglądarkę.
2. Wyczyść pliki cookie przeglądarki. Zobacz [Jak usunąć pliki cookie w programie Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Wyczyść pamięć podręczną przeglądarki. Zobacz [wyczyść pamięć podręczną przeglądarki dla przeglądarki](https://binged.it/2RKyfdU).
4. Otwórz przeglądarkę w trybie prywatnym.

## <a name="web-client-stops-responding-or-disconnects"></a>Klient sieci Web przestaje odpowiadać lub rozłącza

Spróbuj połączyć się przy użyciu innej przeglądarki lub klienta.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Inne przeglądarki i klienci również działają nieprawidłowo lub nie otwierają

Jeśli problemy będą się powtarzać nawet po przełączeniu przeglądarek, problem może nie dotyczyć przeglądarki, ale sieci. Zalecamy skontaktowanie się z pomocą techniczną sieci.

## <a name="web-client-keeps-prompting-for-credentials"></a>Klient sieci Web podtrzymuje monity o poświadczenia

Jeśli klient sieci Web ciągle monituje o poświadczenia, wykonaj następujące instrukcje:

1. Upewnij się, że adres URL klienta internetowego jest poprawny.
2. Upewnij się, że poświadczenia, których używasz, są dla środowiska pulpitu wirtualnego systemu Windows powiązanego z adresem URL.
3. Wyczyść pliki cookie przeglądarki. Aby uzyskać więcej informacji, zobacz [Jak usunąć pliki cookie w programie Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Wyczyść pamięć podręczną przeglądarki. Aby uzyskać więcej informacji, zobacz [Czyszczenie pamięci podręcznej przeglądarki dla przeglądarki](https://binged.it/2RKyfdU).
5. Otwórz przeglądarkę w trybie prywatnym.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia puli dzierżawy i hosta w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli dzierżawy i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej (VM) na pulpicie wirtualnym systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Pulpit wirtualny systemu Windows PowerShell](troubleshoot-powershell.md).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrażaniem szablonów Menedżera zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).