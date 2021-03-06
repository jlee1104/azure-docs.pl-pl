---
title: Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services
description: Zawiera opis sposobu tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services przy użyciu Azure Backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273516"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services

W tym artykule opisano sposób tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services przy użyciu usługi [Azure Backup](backup-overview.md) .

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Przygotowywanie maszyn wirtualnych platformy Azure.
> * Utwórz magazyn.
> * Odnajdywanie maszyn wirtualnych i Konfigurowanie zasad tworzenia kopii zapasowych.
> * Włącz tworzenie kopii zapasowych dla maszyn wirtualnych platformy Azure.
> * Uruchom proces tworzenia początkowej kopii zapasowej.

> [!NOTE]
> W tym artykule opisano sposób konfigurowania magazynu i wybierania maszyn wirtualnych do utworzenia kopii zapasowej. Jest to przydatne, jeśli chcesz utworzyć kopię zapasową wielu maszyn wirtualnych. Alternatywnie można [utworzyć kopię zapasową pojedynczej maszyny wirtualnej platformy Azure](backup-azure-vms-first-look-arm.md) bezpośrednio z ustawień maszyny wirtualnej.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Przejrzyj](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturę kopii zapasowych maszyny wirtualnej platformy Azure.
* [Dowiedz się więcej o](backup-azure-vms-introduction.md) Kopia zapasowa maszyny wirtualnej platformy Azure i rozszerzenie kopii zapasowej.
* Przed skonfigurowaniem kopii zapasowej [zapoznaj się z matrycą pomocy technicznej](backup-support-matrix-iaas.md) .

Ponadto istnieje kilka rzeczy, które mogą być konieczne w pewnych okolicznościach:

* **Zainstaluj agenta maszyny wirtualnej na maszynie wirtualnej**: Azure Backup tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przez zainstalowanie rozszerzenia agenta maszyny wirtualnej platformy Azure uruchomionego na tym komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure Marketplace, Agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę wirtualną lub migrujesz maszynę lokalną, może być konieczne [ręczne zainstalowanie agenta](#install-the-vm-agent).

## <a name="create-a-vault"></a>Tworzenie magazynu

 Magazyn przechowuje kopie zapasowe i punkty odzyskiwania utworzone wraz z upływem czasu, a następnie przechowuje zasady tworzenia kopii w programie skojarzonych z maszynami z kopii zapasowej. Utwórz magazyn w następujący sposób:

1. Zaloguj się do [Azure portal](https://portal.azure.com/).
2. W polu wyszukiwania wpisz **Recovery Services**. W obszarze **usługi**kliknij pozycję **magazyny Recovery Services**.

     ![Wyszukaj Recovery Services magazyny](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. W menu **magazyny Recovery Services** kliknij pozycję **+ Dodaj**.

     ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. W obszarze **magazyn Recovery Services**wpisz przyjazną nazwę, aby zidentyfikować magazyn.
    * Nazwa musi być unikalna w ramach subskrypcji platformy Azure.
    * Może zawierać od 2 do 50 znaków.
    * Musi rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.
5. Wybierz subskrypcję platformy Azure, grupę zasobów i region geograficzny, w którym ma zostać utworzony magazyn. Następnie kliknij pozycję **Utwórz**.
    * Utworzenie magazynu może chwilę potrwać.
    * Monitoruj powiadomienia o stanie w prawym górnym rogu portalu.

Po utworzeniu magazynu pojawi się on na liście magazyny Recovery Services. Jeśli Twój magazyn nie jest widoczny, wybierz pozycję **Odśwież**.

![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure Backup teraz umożliwia dostosowanie nazwy grupy zasobów utworzonej przez usługę Azure Backup. Aby uzyskać więcej informacji, zobacz [Azure Backup grupę zasobów dla Virtual Machines](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie magazyny korzystają z [magazynu geograficznie nadmiarowego (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zalecamy użycie GRS.
* Dla tańszej opcji można użyć [magazynu lokalnie nadmiarowego (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Zmodyfikuj typ replikacji magazynu w następujący sposób:

1. W nowym magazynie kliknij pozycję **Właściwości** w sekcji **Ustawienia** .
2. W obszarze **Właściwości**, w obszarze **Konfiguracja kopii zapasowej**, kliknij przycisk **Aktualizuj**.
3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

      ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu magazynu i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, należy ponownie utworzyć magazyn.

## <a name="apply-a-backup-policy"></a>Stosowanie zasad kopii zapasowych

Skonfiguruj zasady tworzenia kopii zapasowych dla magazynu.

1. W magazynie kliknij pozycję **+ kopia zapasowa** w sekcji **Przegląd** .

   ![Przycisk Kopia zapasowa](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. W > **cel kopii zapasowej** , **gdzie jest uruchomione Twoje obciążenie?** wybierz **platformę Azure**. W **czym chcesz utworzyć kopię zapasową?** wybierz **maszynę wirtualną** >  **OK**. Spowoduje to zarejestrowanie rozszerzenia maszyny wirtualnej w magazynie.

   ![Okienka celu tworzenia kopii zapasowych i tworzenia kopii zapasowych](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. W obszarze **zasady tworzenia kopii zapasowych**wybierz zasady, które chcesz skojarzyć z magazynem.
    * Zasady domyślne tworzą kopię zapasową maszyny wirtualnej raz dziennie. Codzienne kopie zapasowe są przechowywane przez 30 dni. Migawki odzyskiwania natychmiastowego są przechowywane przez dwa dni.
    * Jeśli nie chcesz używać zasad domyślnych, wybierz pozycję **Utwórz nową**, a następnie utwórz zasady niestandardowe zgodnie z opisem w następnej procedurze.

      ![Domyślne zasady kopii zapasowych](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. W obszarze **Wybieranie maszyn wirtualnych**Wybierz Maszyny wirtualne, których kopię zapasową chcesz utworzyć przy użyciu zasad. Następnie kliknij przycisk **OK**.

   * Wybrane maszyny wirtualne zostały zweryfikowane.
   * Maszyny wirtualne można wybrać tylko w tym samym regionie, w którym znajduje się magazyn.
   * Kopie zapasowe maszyn wirtualnych można wykonywać tylko w pojedynczym magazynie.

     ![Okienko "Wybieranie maszyn wirtualnych"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. W obszarze **kopia zapasowa**kliknij pozycję **Włącz kopię zapasową**. Spowoduje to wdrożenie zasad w magazynie i na maszynach wirtualnych, a następnie zainstalowanie rozszerzenia kopii zapasowej na agencie maszyny wirtualnej działającym na maszynie wirtualnej platformy Azure.

     ![Przycisk "Włącz kopię zapasową"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po włączeniu kopii zapasowej:

* Usługa Backup instaluje rozszerzenie kopii zapasowej, niezależnie od tego, czy maszyna wirtualna jest uruchomiona.
* Początkowa kopia zapasowa będzie uruchamiana zgodnie z harmonogramem tworzenia kopii zapasowych.
* Gdy wykonywane są kopie zapasowe, należy pamiętać, że:
  * Uruchomiona maszyna wirtualna ma największą szansę na przechwycenie punktu odzyskiwania spójnego na poziomie aplikacji.
  * Jednak nawet jeśli maszyna wirtualna jest wyłączona, zostanie utworzona jej kopia zapasowa. Taka maszyna wirtualna jest nazywana maszyną wirtualną w trybie offline. W takim przypadku punkt odzyskiwania będzie spójny z awarią.
* Bezpośrednie połączenie wychodzące nie jest wymagane, aby można było tworzyć kopie zapasowe maszyn wirtualnych platformy Azure.

### <a name="create-a-custom-policy"></a>Tworzenie zasad niestandardowych

Jeśli wybrano opcję utworzenia nowych zasad tworzenia kopii zapasowych, Wypełnij ustawienia zasad.

1. W polu **Nazwa zasad**Określ zrozumiałą nazwę.
2. W polu **harmonogram tworzenia kopii zapasowych**Określ, kiedy mają być pobierane kopie zapasowe. Codzienne lub cotygodniowe wykonywanie kopii zapasowych maszyn wirtualnych platformy Azure.
3. W obszarze **natychmiastowe przywracanie**Określ, jak długo mają być przechowywane migawki lokalnie przywracane.
    * Podczas przywracania kopia zapasowa dysków maszyny wirtualnej jest kopiowana z magazynu w sieci do lokalizacji magazynu odzyskiwania. Za pomocą natychmiastowego przywracania można korzystać z migawek przechowywanych lokalnie, które są wykonywane podczas zadania tworzenia kopii zapasowej, bez czekania na przesłanie danych kopii zapasowej do magazynu.
    * Migawki do natychmiastowego przywrócenia można zachować przez od 1 do pięciu dni. Ustawienie domyślne to dwa dni.
4. W obszarze **Zakres przechowywania**Określ, jak długo mają być przechowywane codziennie lub cotygodniowe punkty kopii zapasowych.
5. W obszarze **przechowywanie miesięcznego punktu kopii zapasowej**Określ, czy chcesz przechowywać kopię zapasową codziennych, czy cotygodniowych kopii zapasowych.
6. Kliknij przycisk **OK** , aby zapisać zasady.

    ![Nowe zasady tworzenia kopii zapasowych](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup nie obsługuje automatycznego dopasowywania zegara w przypadku zmian w ramach zmiany czasu dla kopii zapasowych maszyny wirtualnej platformy Azure. Po wystąpieniu zmian czasu należy ręcznie zmodyfikować zasady tworzenia kopii zapasowych zgodnie z potrzebami.

## <a name="trigger-the-initial-backup"></a>Wyzwalanie początkowej kopii zapasowej

Początkowa kopia zapasowa będzie uruchamiana zgodnie z harmonogramem, ale można ją uruchomić od razu w następujący sposób:

1. W menu magazyn kliknij pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej**kliknij pozycję **maszyna wirtualna platformy Azure**.
3. Na liście **elementy kopii zapasowej** kliknij przycisk wielokropka (...).
4. Kliknij pozycję **Utwórz kopię zapasową teraz**.
5. W obszarze **kopia zapasowa**Użyj formantu kalendarza, aby wybrać ostatni dzień przechowywania punktu odzyskiwania. Następnie kliknij przycisk **OK**.
6. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > **w toku**. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.

## <a name="verify-backup-job-status"></a>Sprawdź stan zadania tworzenia kopii zapasowej

Szczegóły zadania kopii zapasowej dla każdej kopii zapasowej maszyny wirtualnej składają się z dwóch faz, fazy **migawki** i fazy **transferu danych do magazynu** .<br/>
Faza migawki gwarantuje dostępność punktu odzyskiwania przechowywanego wraz z dyskami do **natychmiastowego przywrócenia** i jest dostępna przez maksymalnie pięć dni w zależności od przechowywania migawek skonfigurowanego przez użytkownika. Transfer danych do magazynu tworzy punkt odzyskiwania w magazynie do długoterminowego przechowywania. Transfer danych do magazynu rozpoczyna się dopiero po zakończeniu fazy migawki.

  ![Stan zadania tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Istnieją dwa **podzadania** uruchomione w zapleczu — jeden dla zadania tworzenia kopii zapasowej frontonu, który można sprawdzić w bloku szczegóły **zadania tworzenia kopii zapasowej** w następujący sposób:

  ![Stan zadania tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Zakończenie fazy **transferu danych do magazynu** może zająć wiele dni w zależności od rozmiaru dysków, liczby zmian na dysk i kilku innych czynników.

Stan zadania może się różnić w zależności od następujących scenariuszy:

**Zdjęcie** | **Transferowanie danych do magazynu** | **Stan zadania**
--- | --- | ---
Zakończone | W toku | W toku
Zakończone | Pominięto | Zakończone
Zakończone | Zakończone | Zakończone
Zakończone | Niepowodzenie | Ukończono z ostrzeżeniem
Niepowodzenie | Niepowodzenie | Niepowodzenie

Teraz dzięki tej możliwości dla tej samej maszyny wirtualnej dwa kopie zapasowe mogą działać równolegle, ale w każdej fazie (migawka, transfer danych do magazynu) można uruchomić tylko jedno zadanie podrzędne. W związku z tym w przypadku zadania tworzenia kopii zapasowej w toku zakończyło się niepowodzeniem wykonywania kopii zapasowej w następnym dniu. Kopie zapasowe kolejnych dni mogą mieć ukończoną migawkę **,** podczas gdy zadanie tworzenia kopii zapasowej w danym dniu jest w toku.
Przyrostowy punkt odzyskiwania utworzony w magazynie będzie przechwytywał wszystkie zmiany z ostatniego punktu odzyskiwania utworzonego w magazynie. Użytkownik nie ma wpływu na koszty.

## <a name="optional-steps"></a>Kroki opcjonalne

### <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny wirtualnej

Azure Backup tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przez zainstalowanie rozszerzenia agenta maszyny wirtualnej platformy Azure uruchomionego na komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure Marketplace, Agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę wirtualną lub migrujesz maszynę lokalną, może być konieczne ręczne zainstalowanie agenta, zgodnie z podsumowaniem w tabeli.

**MASZYN** | **Szczegóły**
--- | ---
**Windows** | 1. [Pobierz i zainstaluj](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) plik msi agenta.<br/><br/> 2. Zainstaluj program z uprawnieniami administratora na komputerze.<br/><br/> 3. Sprawdź instalację. W *C:\WindowsAzure\Packages* na maszynie wirtualnej kliknij prawym przyciskiem myszy pozycję **WaAppAgent. exe** > **Właściwości**. Na karcie **szczegóły** **Wersja produktu** powinna mieć wartość 2.6.1198.718 lub wyższą.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że nie są uruchomione żadne operacje tworzenia kopii zapasowej, a [następnie ponownie zainstaluj agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Zainstaluj program przy użyciu KCO lub pakietu DEB z repozytorium pakietu dystrybucji. Jest to preferowana metoda instalowania i uaktualniania agenta systemu Linux platformy Azure. Wszyscy [pozatwierdzeni dostawcy dystrybucji](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrują pakiet agenta platformy Azure z systemem Linux z obrazami i repozytoriami. Agent jest dostępny w serwisie [GitHub](https://github.com/Azure/WALinuxAgent), ale nie zalecamy instalacji.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że nie są uruchomione żadne operacje tworzenia kopii zapasowej, i zaktualizuj pliki binarne.

>[!NOTE]
> **Azure Backup teraz obsługuje selektywne tworzenie kopii zapasowych i przywracanie dysków przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.**
>
>Obecnie Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (systemu operacyjnego i danych) w maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowej maszyny wirtualnej. Funkcja wykluczania dysku umożliwia utworzenie kopii zapasowej jednego lub kilku z wielu dysków z danymi na maszynie wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb tworzenia kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dotyczące dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzestawu dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i magazynu.
>
>**Aby utworzyć konto w wersji zapoznawczej, Zapisz się do nas na AskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Następne kroki

* Rozwiązywanie problemów z [agentami maszyn wirtualnych platformy Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) lub [kopiami zapasowymi maszyny wirtualnej platformy Azure](backup-azure-vms-troubleshoot.md).
* [Przywróć](backup-azure-arm-restore-vms.md) Maszyny wirtualne platformy Azure.
