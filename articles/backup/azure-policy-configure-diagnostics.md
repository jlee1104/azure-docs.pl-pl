---
title: Konfigurowanie ustawień diagnostyki magazynu w odpowiedniej skali
description: Skonfiguruj ustawienia diagnostyki Log Analytics dla wszystkich magazynów w danym zakresie przy użyciu Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77584510"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Konfigurowanie ustawień diagnostyki magazynu w odpowiedniej skali

Rozwiązanie raportowania zapewniane przez Azure Backup wykorzystuje Log Analytics (LA). Aby dane z danego magazynu były wysyłane do LA, należy utworzyć dla tego magazynu [ustawienie diagnostyczne](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) .

Często ręczne dodanie ustawienia diagnostyki dla magazynu może być uciążliwym zadaniem. Ponadto wszystkie nowe magazyny tworzone również muszą mieć włączone ustawienia diagnostyczne, aby można było wyświetlać raporty dla tego magazynu. 

Aby uprościć tworzenie ustawień diagnostycznych na dużą skalę (z LA jako miejsce docelowe), Azure Backup zapewnia wbudowaną [Azure Policy](https://docs.microsoft.com/azure/governance/policy/). Ta zasada dodaje ustawienie diagnostyki LA do wszystkich magazynów w danej subskrypcji lub grupie zasobów. Poniższe sekcje zawierają instrukcje dotyczące korzystania z tych zasad.

## <a name="supported-scenarios"></a>Scenariusze obsługiwane

* Zasady mogą być stosowane jednocześnie do wszystkich magazynów Recovery Services w określonej subskrypcji (lub w grupie zasobów w ramach subskrypcji). Użytkownik przypisujący zasady musi mieć dostęp "właściciel" do subskrypcji, do której przypisane są zasady.

* Obszar roboczy LA określony przez użytkownika (do którego będą wysyłane dane diagnostyczne) może znajdować się w innej subskrypcji niż magazyny, do których te zasady są przypisane. Użytkownik musi mieć dostęp "czytelnik", "Współautor" lub "właściciel" do subskrypcji, w której znajduje się określony obszar roboczy LA.

* Zakres grupy zarządzania nie jest obecnie obsługiwany.

* Wbudowane zasady nie są obecnie dostępne w chmurach krajowych.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Przypisywanie wbudowanych zasad do zakresu

Aby przypisać zasady dla magazynów w wymaganym zakresie, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal i przejdź do pulpitu nawigacyjnego **zasad** .
2. Wybierz pozycję **definicje** w menu po lewej stronie, aby wyświetlić listę wszystkich wbudowanych zasad w ramach zasobów platformy Azure.
3. Filtrowanie listy dla **kategorii = Monitorowanie**. Zlokalizuj zasady o nazwie **[wersja zapoznawcza]: Wdróż ustawienia diagnostyczne magazynu Recovery Services do log Analytics obszaru roboczego dla kategorii określonych zasobów**.

![Blok definicji zasad](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Kliknij nazwę zasad. Nastąpi przekierowanie do szczegółowej definicji dla tych zasad.

![Szczegółowa definicja zasad](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Kliknij przycisk **Przypisz** w górnej części bloku. Przekierowuje to do bloku **przypisywanie zasad** .

6. W obszarze **podstawowe**kliknij trzy kropki obok pola **zakres** . Spowoduje to otwarcie bloku prawego kontekstu, w którym można wybrać subskrypcję zasad, która ma zostać zastosowana. Opcjonalnie można również wybrać grupę zasobów, aby zasady były stosowane tylko w przypadku magazynów w określonej grupie zasobów.

![Podstawowe informacje dotyczące przypisywania zasad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. W obszarze **Parametry**wprowadź następujące informacje:

* **Nazwa profilu** — nazwa, która zostanie przypisana do ustawień diagnostycznych utworzonych przez zasady.
* **Log Analytics obszar roboczy** — log Analytics obszar roboczy, do którego ma zostać skojarzone ustawienie diagnostyczne. Dane diagnostyczne wszystkich magazynów w zakresie przypisania zasad zostaną wypchnięte do określonego obszaru roboczego LA.

* **Nazwa tagu wykluczenia (opcjonalnie) i wartość tagu wykluczenia (opcjonalnie)** — możesz zdecydować się na wykluczenie z przypisania zasad magazynów zawierających określoną nazwę tagu i wartość. Na przykład, jeśli **nie** chcesz dodać ustawienia diagnostycznego do tych magazynów, dla których tag "istest" ma ustawioną wartość "yes", musisz wprowadzić "istest" w polu **nazwa znacznika wykluczenia** i "yes" w polu **wartość znacznika wykluczenia** . Jeśli którykolwiek z tych dwóch pól lub oba te pola są puste, zasady zostaną zastosowane do wszystkich odpowiednich magazynów niezależnie od tagów, które zawierają.

![Parametry przypisania zasad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Utwórz zadanie korygowania** — po przypisaniu zasad do zakresu wszelkie nowe magazyny utworzone w tym zakresie automatycznie otrzymują skonfigurowane ustawienia diagnostyki La (w ciągu 30 minut od momentu utworzenia magazynu). Aby dodać ustawienia diagnostyki do istniejących magazynów w zakresie, można wyzwolić zadanie korygowania przy użyciu czasu przypisania zasad. Aby wyzwolić zadanie korygowania, zaznacz pole wyboru **Utwórz zadanie korygowania**. 

![Korygowanie przypisań zasad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Przejdź do karty **Recenzja + tworzenie** , a następnie kliknij przycisk **Utwórz**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Pod jakimi warunkami zadanie korygowania będzie stosowane do magazynu?

Zadanie korygowania jest stosowane do magazynów, które są niezgodne zgodnie z definicją zasad. Magazyn nie jest zgodny, jeśli spełnia jeden z następujących warunków:

* Brak ustawienia diagnostyki dla magazynu.
* Ustawienia diagnostyczne są obecne dla magazynu, ale żadne z tych ustawień nie ma **wszystkich** zdarzeń specyficznych dla zasobów, które zostały włączone przy użyciu La jako miejsca docelowego, i wybranego **zasobu** zaznaczonego w przełączniku. 

Nawet jeśli użytkownik ma magazyn z włączonym zdarzeniem AzureBackupReport w trybie AzureDiagnostics (który jest obsługiwany przez raporty kopii zapasowych [), zadanie](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)korygowania nadal będzie dotyczyło tego magazynu, ponieważ tryb specyficzny dla zasobów jest zalecanym sposobem tworzenia ustawień diagnostycznych.

Ponadto jeśli użytkownik ma magazyn z tylko podzbiorem sześciu zdarzeń specyficznych dla zasobów, zadanie korygowania będzie dotyczyło tego magazynu, ponieważ raporty kopii zapasowych będą działały zgodnie z oczekiwaniami tylko wtedy, gdy są włączone wszystkie sześć zdarzeń specyficznych dla zasobów.

> [!NOTE]
>
> Jeśli magazyn ma istniejące ustawienie diagnostyczne z **podzbiorem kategorii specyficznych dla zasobów** , które zostały skonfigurowane do wysyłania danych do określonego obszaru roboczego La, powiedz "Workspace x", a następnie zadanie korygowania zakończy się niepowodzeniem (dla danego magazynu), jeśli docelowy obszar roboczy La określony w przypisaniu zasad jest **taki sam jak** "Workspace x". 
>
>Wynika to z faktu, że jeśli zdarzenia włączone przez dwa różne ustawienia diagnostyki tego samego zasobu **nakładają** się na niektóre formy, ustawienia nie mogą mieć tego samego obszaru roboczego La jako miejsca docelowego. Konieczne będzie ręczne rozwiązanie tego błędu, przechodzenie do odpowiedniego magazynu i skonfigurowanie ustawienia diagnostyki z innym obszarem roboczym LA jako miejscem docelowym.
>
> Należy pamiętać, że zadanie korygujące **nie** powiedzie się, jeśli istniejące ustawienie diagnostyczne AzureBackupReport włączone z obszarem roboczym X jako miejsce docelowe, ponieważ w tym przypadku nie będzie nakładać się między zdarzeniami włączonymi przez istniejące ustawienie i zdarzeniami włączonymi przez ustawienie utworzone przez zadanie korygowania.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak używać raportów kopii zapasowych](https://docs.microsoft.com/azure/backup/configure-reports)
* [Dowiedz się więcej o Azure Policy](https://docs.microsoft.com/azure/governance/policy/)
* [Użyj Azure Policy, aby włączyć funkcję autotworzenia kopii zapasowych dla wszystkich maszyn wirtualnych w zakresie](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
