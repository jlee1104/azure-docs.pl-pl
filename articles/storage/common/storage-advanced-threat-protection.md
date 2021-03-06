---
title: Skonfiguruj zaawansowaną ochronę przed zagrożeniami
titleSuffix: Azure Storage
description: Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla usługi Azure Storage, aby wykrywać anomalie w działaniu konta i otrzymywać powiadomienia o potencjalnie szkodliwych próbach uzyskania dostępu do konta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: d97b63aa87bfd8cf8ca1a3f5b213d6b16ca066df
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616850"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności eksperta zabezpieczeń ani zarządzania systemami monitorowania zabezpieczeń.

Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. Te alerty zabezpieczeń są zintegrowane z usługą [Azure Security Center](https://azure.microsoft.com/services/security-center/)i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń.

> [!NOTE]
> Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage jest obecnie dostępna tylko dla magazynu obiektów BLOB. Nie jest ona dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych. Aby uzyskać szczegółowe informacje o cenach, w tym bezpłatną 30-dniową wersję próbną, zobacz [stronę z cennikiem Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage umożliwia pobieranie dzienników diagnostycznych żądań odczytu, zapisu i usuwania do magazynu obiektów BLOB w celu wykrywania zagrożeń. Aby zbadać alerty z zaawansowanej ochrony przed zagrożeniami, można wyświetlić powiązane działanie magazynu za pomocą rejestrowania analityka magazynu. Aby uzyskać więcej informacji, zobacz **Konfigurowanie rejestrowania** w obszarze [Monitorowanie konta magazynu w Azure Portal](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami

Można skonfigurować zaawansowaną ochronę przed zagrożeniami na kilka sposobów, które opisano w poniższych sekcjach.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Uruchom [Azure Portal](https://portal.azure.com/).
1. Przejdź do konta usługi Azure Storage. W obszarze **Ustawienia**wybierz pozycję **Zabezpieczenia zaawansowane**.
1. Wybierz link **Ustawienia** na stronie Konfiguracja zabezpieczeń zaawansowanych.
1. Ustaw **Zaawansowane zabezpieczenia** **na włączone**.
1. Kliknij przycisk **Zapisz** , aby zapisać nowe lub zaktualizowane zasady.

    ![Włączanie zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Gdy subskrybujesz warstwę Standardowa w Azure Security Center, zaawansowana ochrona przed zagrożeniami jest automatycznie skonfigurowana na wszystkich kontach magazynu. Możesz włączyć lub wyłączyć zaawansowaną ochronę przed zagrożeniami dla kont magazynu w ramach określonej subskrypcji w następujący sposób:

1. Uruchom **Azure Security Center** w [Azure Portal](https://portal.azure.com).
1. W menu głównym kliknij pozycję **Cennik ustawienia &** .
1. Kliknij subskrypcję, którą chcesz włączyć lub wyłączyć ochronę przed zagrożeniami dla swoich kont magazynu.

    ![Wybieranie subskrypcji](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Kliknij pozycję **warstwa cenowa**.
1. W sekcji **Wybieranie warstwy cenowej według typu zasobu** w wierszu **konta magazynu** kliknij pozycję **włączone** lub **wyłączone**.

    ![Włącz ATP w Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Kliknij przycisk **Save** (Zapisz).

### <a name="template"></a>[Szablon](#tab/template)

Użyj szablonu Azure Resource Manager, aby wdrożyć konto usługi Azure Storage z włączoną funkcją zaawansowanej ochrony przed zagrożeniami. Aby uzyskać więcej informacji, zobacz [konto magazynu z zaawansowaną ochroną przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Użyj Azure Policy, aby włączyć zaawansowaną ochronę przed zagrożeniami na kontach magazynu w ramach określonej subskrypcji lub grupy zasobów.

1. Uruchom stronę **definicje zasad** platformy Azure.

1. Wyszukaj zasady **Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu** .

     ![Zasady wyszukiwania](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Wybierz subskrypcję platformy Azure lub grupę zasobów.

    ![Wybierz subskrypcję lub grupę](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Przypisz zasady.

    ![Strona definicji zasad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

Użyj poleceń interfejsu API REST, aby utworzyć, zaktualizować lub pobrać ustawienie zaawansowanej ochrony przed zagrożeniami dla określonego konta magazynu.

* [Zaawansowana ochrona przed zagrożeniami — tworzenie](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Zaawansowana ochrona przed zagrożeniami — Pobierz](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Użyj następujących poleceń cmdlet programu PowerShell:

* [Włącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Uzyskaj zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Wyłącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Eksploruj anomalie zabezpieczeń

Gdy wystąpią anomalie aktywności magazynu, otrzymasz powiadomienie e-mail z informacjami o podejrzanym zdarzeniu zabezpieczeń. Szczegóły zdarzenia obejmują:

* Charakter anomalii
* Nazwa konta magazynu
* Czas zdarzenia
* Typ magazynu
* Możliwe przyczyny
* Kroki badania
* Kroki zaradcze

Wiadomość e-mail zawiera również szczegółowe informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia.

![Adres e-mail alertu zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Bieżące alerty zabezpieczeń można przeglądać i zarządzać nimi z poziomu [kafelka alerty zabezpieczeń](../../security-center/security-center-managing-and-responding-alerts.md)Azure Security Center. Kliknięcie określonego alertu zawiera szczegółowe informacje i akcje związane z badaniem bieżącego zagrożenia i rozwiązywanie przyszłych zagrożeń.

![Adres e-mail alertu zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby dostępu do kont magazynu lub korzystania z nich. Aby uzyskać listę alertów dotyczących usługi Azure Storage, zobacz sekcję dotyczącą **magazynu** w temacie [Ochrona przed zagrożeniami dla usług danych w Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [dziennikach na kontach usługi Azure Storage](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Dowiedz się więcej o [Azure Security Center](../../security-center/security-center-intro.md)
