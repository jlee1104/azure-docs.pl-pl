---
title: 'Drzewo decyzyjnej grupy wieloklasowej: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać wieloklasowego modułu drzewa decyzyjnego w Azure Machine Learning do tworzenia klasyfikatora przy użyciu danych z etykietami.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920063"
---
# <a name="multiclass-boosted-decision-tree"></a>Wieloklasowe wzmocnione drzewo decyzyjne

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu uczenia maszynowego, który jest oparty na algorytmie drzew z podwyższaną decyzją.

Wzmocnione drzewo decyzyjne to metoda uczenia się, w której drugie drzewo jest zgodne z błędami pierwszego drzewa, trzecie drzewo jest odpowiednie dla błędów pierwszego i drugiego drzewa i tak dalej. Przewidywania opierają się na rozwiązaniu wszystkich drzew jednocześnie.

## <a name="how-to-configure"></a>Jak skonfigurować 

Ten moduł tworzy niepociąg model klasyfikacji. Ponieważ klasyfikacja jest metodą uczenia nadzorowanego, potrzebny jest *oznaczony zestaw danych* , który zawiera kolumnę etykiety z wartością dla wszystkich wierszy.

Ten typ modelu można przeszkolić przy użyciu [modelu uczenia](././train-model.md). 

1.  Dodaj moduł **drzewa decyzyjnej wieloklasowej** do potoku.

1.  Określ sposób, w jaki model ma być szkolony przez ustawienie opcji **tworzenia trybu Trainer** .

    + **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.
    
    + **Zakres parametrów**: Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić odchylenia parametrów. Wybierz zakres wartości do iteracji, a [Parametry dostrojenia modelu](tune-model-hyperparameters.md) przechodzą na wszystkie możliwe kombinacje ustawień, które podano, aby określić parametry, które generują optymalne wyniki.  

1. **Maksymalna liczba liści na drzewo** ogranicza maksymalną liczbę węzłów terminalu (liści), które można utworzyć w dowolnym drzewie.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Minimalna liczba próbek na węzeł liścia** wskazuje liczbę przypadków wymaganych do utworzenia dowolnego węzła terminalu w drzewie.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **Szybkość uczenia** definiuje rozmiar kroku podczas uczenia się. Wprowadź liczbę z zakresu od 0 do 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Liczba skonstruowanych drzew** wskazuje całkowitą liczbę drzew decyzyjnych do utworzenia w całości. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale zwiększy się czas uczenia.

1. **Inicjator liczb losowych** opcjonalnie ustawia nieujemną liczbę całkowitą do użycia jako losową wartość inicjatora. Określenie inicjatora zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](./train-model.md) .

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
