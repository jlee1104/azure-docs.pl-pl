---
title: Tworzenie kopii zapasowych plików i folderów — często zadawane pytania
description: Rozwiązuje często zadawane pytania dotyczące tworzenia kopii zapasowych plików i folderów za pomocą Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: adcbf5c3b404de46634423f8f59c4798d44bebe0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273425"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Często zadawane pytania dotyczące tworzenia kopii zapasowych plików i folderów

Ten artykuł zawiera odpowiedzi na często zadawane pytania abound tworzenia kopii zapasowych plików i folderów za pomocą agenta Microsoft Azure Recovery Services (MARS) w usłudze [Azure Backup](backup-overview.md) .

## <a name="configure-backups"></a>Skonfigurowanie funkcji tworzenia kopii zapasowych

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Gdzie można pobrać najnowszą wersję agenta MARS?

Najnowszy Agent MARS używany podczas tworzenia kopii zapasowych komputerów z systemem Windows Server, programu System Center DPM i serwera Microsoft Azure Backup jest dostępny do [pobrania](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Jak długo poświadczenia magazynu są prawidłowe?

Poświadczenia magazynu wygasają po upływie 48 godzin. Jeśli plik poświadczeń wygaśnie, Pobierz go ponownie z Azure Portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Z jakich dysków można tworzyć kopie zapasowe plików i folderów?

Nie można utworzyć kopii zapasowych następujących typów dysków i woluminów:

* Nośniki wymienne: wszystkie źródła elementów kopii zapasowych muszą być raportowane jako naprawione.
* Woluminy tylko do odczytu: wolumin musi być zapisywalny dla usługi kopiowania woluminów w tle (VSS) do działania.
* Woluminy offline: wolumin musi być w trybie online, aby usługa VSS mogła działać.
* Udziały sieciowe: wolumin musi być lokalny na serwerze, aby można było utworzyć kopię zapasową za pomocą usługi kopia online.
* Woluminy chronione przez funkcję BitLocker: wolumin musi zostać odblokowany, aby można było wykonać kopię zapasową.
* Identyfikacja systemu plików: jedyny obsługiwany system plików to system NTFS.

### <a name="what-file-and-folder-types-are-supported"></a>Jakie typy plików i folderów są obsługiwane?

[Dowiedz się więcej](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) o typach plików i folderów obsługiwanych na potrzeby tworzenia kopii zapasowych.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Czy mogę użyć agenta MARS do tworzenia kopii zapasowych plików i folderów na maszynie wirtualnej platformy Azure?  

Tak. Azure Backup zapewnia kopie zapasowe na poziomie maszyny wirtualnej dla maszyn wirtualnych platformy Azure przy użyciu rozszerzenia maszyny wirtualnej dla agenta maszyny wirtualnej platformy Azure. Jeśli chcesz utworzyć kopię zapasową plików i folderów w systemie operacyjnym Windows gościa na maszynie wirtualnej, możesz zainstalować agenta MARS.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Czy można użyć agenta MARS do tworzenia kopii zapasowych plików i folderów w magazynie tymczasowym dla maszyny wirtualnej platformy Azure?

Tak. Zainstaluj agenta MARS i Utwórz kopię zapasową plików i folderów w systemie operacyjnym Windows gościa w magazynie tymczasowym.

* Zadania tworzenia kopii zapasowej kończą się niepowodzeniem, gdy dane magazynu tymczasowego zostaną wyczyszczone.
* Jeśli dane magazynu tymczasowego zostaną usunięte, można przywrócić tylko do magazynu nietrwałego.

### <a name="how-do-i-register-a-server-to-another-region"></a>Jak mogę zarejestrować serwer w innym regionie?

Dane kopii zapasowej są wysyłane do centrum danych magazynu, w którym zarejestrowano serwer. Najprostszym sposobem zmiany centrum danych jest odinstalowanie i ponowne zainstalowanie agenta, a następnie zarejestrowanie go w nowym magazynie w wymaganym regionie.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Czy Agent MARS obsługuje deduplikację systemu Windows Server 2012?

Tak. Agent MARS konwertuje deduplikowane dane na normalne dane podczas przygotowywania operacji tworzenia kopii zapasowej. Następnie optymalizuje dane kopii zapasowej, szyfruje dane, a następnie wysyła do magazynu zaszyfrowane dane.

## <a name="manage-backups"></a>Zarządzanie kopiami zapasowymi

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Co się stanie w przypadku zmiany nazwy komputera z systemem Windows skonfigurowanego na potrzeby tworzenia kopii zapasowych?

W przypadku zmiany nazwy komputera z systemem Windows wszystkie aktualnie skonfigurowane kopie zapasowe są zatrzymywane.

* Należy zarejestrować nową nazwę komputera w magazynie kopii zapasowych.
* Po zarejestrowaniu nowej nazwy w magazynie Pierwsza operacja jest *pełną* kopią zapasową.
* Jeśli musisz odzyskać dane z kopii zapasowej do magazynu przy użyciu starej nazwy serwera, użyj opcji, aby przywrócić lokalizację alternatywną w Kreatorze odzyskiwania danych. [Dowiedz się więcej](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Jaka jest maksymalna długość ścieżki do pliku kopii zapasowej?

Agent MARS jest oparty na systemie plików NTFS i używa specyfikacji długości FilePath ograniczonej przez [interfejs API systemu Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Jeśli pliki, które mają być chronione, są dłuższe niż dozwolona wartość, Utwórz kopię zapasową folderu nadrzędnego lub dysku.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Jakie znaki są dozwolone w ścieżkach plików?

Agent MARS jest oparty na systemie plików NTFS i zezwala na [obsługiwane znaki](/windows/desktop/FileIO/naming-a-file#naming-conventions) w nazwach plików/ścieżkach.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Pojawia się ostrzeżenie "nie skonfigurowano kopii zapasowych platformy Azure dla tego serwera"

To ostrzeżenie może wystąpić, mimo że skonfigurowano zasady tworzenia kopii zapasowych, gdy ustawienia harmonogramu tworzenia kopii zapasowych przechowywane na serwerze lokalnym nie są takie same jak ustawienia przechowywane w magazynie kopii zapasowych.

* Gdy serwer lub ustawienia zostały odzyskane do znanego dobrego stanu, harmonogramy tworzenia kopii zapasowych mogą stać się niezsynchronizowane.
* Jeśli zostanie wyświetlone to ostrzeżenie, należy ponownie [skonfigurować](backup-azure-manage-windows-server.md) zasady tworzenia kopii zapasowych, a następnie uruchomić kopię zapasową na żądanie w celu ponownej synchronizacji serwera lokalnego z platformą Azure.

## <a name="manage-the-backup-cache-folder"></a>Zarządzanie folderem pamięci podręcznej kopii zapasowej

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Jaki jest minimalny wymagany rozmiar folderu pamięci podręcznej?

Rozmiar folderu pamięci podręcznej określa ilość danych, które można umieścić w kopii zapasowej.

* Woluminy folderu pamięci podręcznej powinny mieć wolne miejsce mające co najmniej 5-10% całkowitego rozmiaru danych kopii zapasowej.
* Jeśli ilość wolnego miejsca na woluminie jest mniejsza niż 5%, Zwiększ rozmiar woluminu lub Przenieś folder pamięci podręcznej na wolumin z wystarczającą ilością miejsca, wykonując następujące [kroki](#how-do-i-change-the-cache-location-for-the-mars-agent).
* W przypadku tworzenia kopii zapasowej stanu systemu Windows potrzeba dodatkowego 30-35 GB wolnego miejsca w woluminie zawierającym folder pamięci podręcznej.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Jak sprawdzić, czy folder z katalogiem tymczasowym jest prawidłowy i dostępny?

1. Domyślnie folder tymczasowy znajduje się w lokalizacji `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Upewnij się, że ścieżka lokalizacji folderu tymczasowego jest zgodna z wartościami wpisów kluczy rejestru wymienionych poniżej:

    | Ścieżka rejestru | Klucz rejestru | Wartość |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nowa lokalizacja folderu pamięci podręcznej* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nowa lokalizacja folderu pamięci podręcznej* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Jak mogę zmienić lokalizacji pamięci podręcznej agenta MARS?

1. Uruchom to polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, aby zatrzymać aparat kopii zapasowych:

    ```Net stop obengine```
2. Jeśli skonfigurowano kopię zapasową stanu systemu, Otwórz przystawkę Zarządzanie dyskami i Odinstaluj dyski z nazwami w formacie `"CBSSBVol_<ID>"`.
3. Domyślnie folder tymczasowy znajduje się w lokalizacji `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Skopiuj cały folder `\Scratch` na inny dysk, na którym jest wystarczająca ilość miejsca. Upewnij się, że zawartość jest kopiowana, a nie przeniesiona.
5. Zaktualizuj następujące wpisy rejestru ze ścieżką nowo przeniesionego folderu tymczasowego.

    | Ścieżka rejestru | Klucz rejestru | Wartość |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nowa lokalizacja folderu tymczasowego* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nowa lokalizacja folderu tymczasowego* |

6. Uruchom ponownie aparat kopii zapasowej w wierszu polecenia z podwyższonym poziomem uprawnień:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Uruchom kopię zapasową na żądanie. Po pomyślnym zakończeniu tworzenia kopii zapasowej za pomocą nowej lokalizacji można usunąć oryginalny folder pamięci podręcznej.

### <a name="where-should-the-cache-folder-be-located"></a>Gdzie ma znajdować się folder pamięci podręcznej?

Nie zaleca się stosowania następujących lokalizacji dla folderu pamięci podręcznej:

* Udział sieciowy/nośniki wymienne: folder pamięci podręcznej musi być lokalny dla serwera, którego kopie zapasowe mają być wykonywane przy użyciu kopii zapasowej online. Lokalizacje sieciowe lub nośniki wymienne, takie jak dyski USB, nie są obsługiwane.
* Woluminy offline: folder pamięci podręcznej musi być w trybie online dla oczekiwanej kopii zapasowej przy użyciu agenta Azure Backup

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Czy istnieją jakieś atrybuty folderu pamięci podręcznej, które nie są obsługiwane?

Następujące atrybuty folderu pamięci podręcznej ani ich kombinacje nie są obsługiwane:

* Zaszyfrowane
* Deduplikowane
* Skompresowane
* Rozrzedzone
* Punkt ponownej analizy

Folder pamięci podręcznej i dysk VHD metadanych nie mają wymaganych atrybutów dla agenta Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Czy istnieje sposób dostosowania przepustowości używanej do tworzenia kopii zapasowych?

Tak, możesz użyć opcji **Zmień właściwości** w agencie Mars, aby dostosować przepustowość i chronometraż. [Dowiedz się więcej](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Przywracanie

### <a name="manage"></a>Zarządzaj

**Czy mogę odzyskać, jeśli nie pamiętam mojego hasła?**
Agent Azure Backup wymaga hasła (podanego podczas rejestracji) do odszyfrowania danych kopii zapasowej podczas przywracania. Zapoznaj się z poniższymi scenariuszami, aby poznać opcje obsługi utraconego hasła:

| Oryginalna maszyna <br> *(maszyna źródłowa, w której zostały wykonane kopie zapasowe)* | Passphrase | Dostępne opcje |
| --- | --- | --- |
| Dostępne |Następuje |Jeśli oryginalny komputer (w którym zostały wykonane kopie zapasowe) jest dostępny i nadal zarejestrowany w tym samym magazynie Recovery Services, możesz ponownie wygenerować hasło, wykonując następujące [kroki](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase).  |
| Następuje |Następuje |Nie można odzyskać danych lub dane są niedostępne |

Należy wziąć pod uwagę następujące warunki:

* Po odinstalowaniu i ponownym zarejestrowaniu agenta na tym samym komputerze oryginalnym przy użyciu
  * To *samo hasło*, a następnie będzie można przywrócić dane kopii zapasowej.
  * *Innym hasłem*nie będzie można przywrócić danych kopii zapasowej.
* Jeśli Agent jest instalowany na *innym komputerze* z
  * To *samo hasło* (używane w oryginalnej maszynie), będzie można przywrócić dane kopii zapasowej.
  * *Innym hasłem*nie będzie można przywrócić danych kopii zapasowej.
* Jeśli oryginalny komputer jest uszkodzony (nie można ponownie wygenerować hasła za pośrednictwem konsoli MARS), ale można przywrócić lub uzyskać dostęp do oryginalnego folderu tymczasowego używanego przez agenta MARS, można przywrócić (jeśli nie pamiętasz hasła). Aby uzyskać więcej pomocy, skontaktuj się z działem obsługi klienta.

**Jak mogę Odzyskaj, Jeśli utracisz moją oryginalną maszynę (w której wykonano kopie zapasowe)?**

Jeśli masz takie samo hasło (podane podczas rejestracji) oryginalnego komputera, możesz przywrócić kopię zapasową danych na alternatywnej maszynie. Zapoznaj się z poniższymi scenariuszami, aby poznać Opcje przywracania.

| Oryginalna maszyna | Passphrase | Dostępne opcje |
| --- | --- | --- |
| Następuje |Dostępne |Agenta MARS można zainstalować i zarejestrować na innym komputerze z hasłem podanym podczas rejestracji oryginalnej maszyny. Wybierz **opcję odzyskiwania** > **inną lokalizację** , aby przeprowadzić przywracanie. Więcej informacji znajduje się w [tym](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine) artykule.
| Następuje |Następuje |Nie można odzyskać danych lub dane są niedostępne |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co się stanie w przypadku anulowania trwającego zadania przywracania?

W przypadku anulowania trwającego zadania przywracania proces przywracania zostanie zatrzymany. Wszystkie pliki przywrócone przed anulowaniem pozostają w skonfigurowanym miejscu docelowym (w lokalizacji oryginalnej lub alternatywnej) bez żadnych wycofywania.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>Czy Agent MARS wykonuje kopie zapasowe i przywraca listy ACL ustawione dla plików, folderów i woluminów?

* Agent MARS tworzy kopie zapasowe list ACL ustawionych dla plików, folderów i woluminów
* W przypadku opcji odzyskiwania przywracania woluminów Agent MARS udostępnia opcję pomijania przywracania uprawnień ACL do pliku lub folderu odzyskiwanego
* W przypadku poszczególnych opcji odzyskiwania plików i folderów Agent MARS zostanie przywrócony z uprawnieniami ACL (nie ma opcji pomijania przywracania listy ACL).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się](tutorial-backup-windows-server-to-azure.md) , jak utworzyć kopię zapasową komputera z systemem Windows.
