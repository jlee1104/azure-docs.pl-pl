---
title: Tworzenie niestandardowych zasad zabezpieczeń w Azure Security Center | Microsoft Docs
description: Definicje zasad niestandardowych platformy Azure monitorowane przez Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 34dbace304ccf70891ef53dd768de60d87e26967
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666639"
---
# <a name="using-custom-security-policies-preview"></a>Korzystanie z niestandardowych zasad zabezpieczeń (wersja zapoznawcza)

Aby pomóc w zabezpieczeniu systemów i środowiska, Azure Security Center generuje zalecenia dotyczące zabezpieczeń. Zalecenia te są oparte na najlepszych rozwiązaniach branżowych, które są włączone do ogólnych, domyślnych zasad zabezpieczeń dostarczonych wszystkim klientom. Mogą również pochodzić z Security Center znajomości standardów branżowych i prawnych.

Za pomocą tej funkcji w wersji zapoznawczej możesz dodać własne inicjatywy *niestandardowe* . Następnie otrzymasz zalecenia, jeśli środowisko nie będzie zgodne z tworzonymi zasadami. Wszelkie utworzone inicjatywy niestandardowe będą wyświetlane wraz z wbudowanymi inicjatywami na pulpicie nawigacyjnym zgodności z przepisami, które opisano w samouczku [poprawianie zgodności z przepisami](security-center-compliance-dashboard.md).

Zgodnie z [opisem w dokumentacji](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) Azure Policy, gdy określisz lokalizację dla inicjatywy niestandardowej, musi to być grupa zarządzania lub subskrypcja. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Aby dodać inicjatywę niestandardową do subskrypcji 

1. Na pasku bocznym Security Center Otwórz stronę **zasady zabezpieczeń** .

1. Wybierz subskrypcję lub grupę zarządzania, do której chcesz dodać inicjatywę niestandardową.

    [![wybrać subskrypcję, dla której chcesz utworzyć zasady niestandardowe](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Należy dodać niestandardowe standardy na poziomie subskrypcji (lub nowszym), aby były oceniane i wyświetlane w Security Center. 
    >
    > Dodanie niestandardowego standardu powoduje przypisanie *inicjatywy* do tego zakresu. Dlatego zalecamy wybranie najszerszego zakresu wymaganego dla tego przydziału.

1. Na stronie zasady zabezpieczeń w obszarze inicjatywy niestandardowe (wersja zapoznawcza) kliknij pozycję **Dodaj inicjatywę niestandardową**.

    [![kliknij pozycję * * Dodaj inicjatywę niestandardową * *](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Zostanie wyświetlona następująca strona:

    ![Tworzenie lub Dodawanie zasad](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na stronie Dodawanie niestandardowych inicjatyw Przejrzyj listę zasad niestandardowych już utworzonych w organizacji. Jeśli zobaczysz, że chcesz ją przypisać do swojej subskrypcji, kliknij przycisk **Dodaj**. Jeśli na liście nie ma inicjatywy, która spełnia Twoje wymagania, Pomiń ten krok.

1. Aby utworzyć nową inicjatywę niestandardową:

    1. Kliknij przycisk **Utwórz nowy**.
    1. Wprowadź lokalizację i nazwę definicji.
    1. Wybierz zasady do uwzględnienia i kliknij przycisk **Dodaj**.
    1. Wprowadź wszelkie wymagane parametry.
    1. Kliknij pozycję **Zapisz**.
    1. Na stronie Dodaj niestandardowe inicjatywy kliknij przycisk Odśwież, a Twoja nowa inicjatywa będzie wyświetlana jako dostępna.
    1. Kliknij pozycję **Dodaj** i przypisz ją do subskrypcji.

    > [!NOTE]
    > Tworzenie nowych inicjatyw wymaga poświadczeń właściciela subskrypcji. Aby uzyskać więcej informacji na temat ról platformy Azure, zobacz [uprawnienia w Azure Security Center](security-center-permissions.md).

    Twoja nowa inicjatywa zacznie obowiązywać i zobaczysz wpływ na dwa sposoby:

    * Na pasku bocznym Security Center w obszarze Zasady & Zgodność wybierz pozycję **zgodność z przepisami**. Zostanie otwarty pulpit nawigacyjny zgodności pokazujący nową inicjatywę niestandardową wraz z wbudowaną inicjatywą.
    
    * Jeśli środowisko nie będzie zgodne ze zdefiniowanymi zasadami, zaczniesz otrzymywać zalecenia.

1. Aby zobaczyć, jakie są zalecenia dotyczące zasad, kliknij przycisk **zalecenia** na pasku bocznym, aby otworzyć stronę zalecenia. Zalecenia będą wyświetlane z etykietą "niestandardowy" i będą dostępne w ciągu około godziny.

    [![zalecenia niestandardowe](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia niestandardowych zasad zabezpieczeń. 

Inne powiązane materiały można znaleźć w następujących artykułach: 

- [Omówienie zasad zabezpieczeń](tutorial-security-policy.md)
- [Lista wbudowanych zasad zabezpieczeń](security-center-policy-definitions.md)