---
title: 'Samouczek: wykrywanie i wyświetlanie danych twarzy na obrazie za pomocą zestawu SDK platformy .NET'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację systemu Windows, która używa usługi Twarz do wykrywania i kadrowania twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a5cf3c59c94134e1d0751c1467cd324a95c366eb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78898817"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Samouczek: Tworzenie aplikacji Windows Presentation Framework (WPF) do wyświetlania danych twarzy na obrazie

W tym samouczku dowiesz się, jak korzystać z usługi Azure Face za pośrednictwem zestawu SDK klienta platformy .NET w celu wykrycia twarzy w obrazie, a następnie przedstawienia tych danych w interfejsie użytkownika. Utworzysz aplikację WPF, która wykrywa twarze, rysuje ramkę wokół każdej ściany i wyświetla opis twarzy na pasku stanu. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie aplikacji WPF
> - Instalowanie biblioteki klienta Face
> - Korzystanie z biblioteki klienta do wykrywania twarzy na obrazie
> - Rysowanie ramki wokół każdej wykrytej twarzy
> - Wyświetlanie opisu wyróżnionej twarzy na pasku stanu

![Zrzut ekranu przedstawiający wykryte twarze otoczone prostokątami](../Images/getting-started-cs-detected.png)

Kompletny przykładowy kod jest dostępny w repozytorium [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) (Przykład rozpoznawania twarzy w języku C# za pomocą usług Cognitive) w witrynie GitHub.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. 


## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji Face. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Możesz też postępować zgodnie z instrukcjami w aplikacji [Utwórz konto usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby zasubskrybować usługę Face i uzyskać klucz. Następnie [należy utworzyć zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu `FACE_SUBSCRIPTION_KEY` punktu `FACE_ENDPOINT`końcowego klucza i usługi, odpowiednio o nazwie i ,.
- Dowolna wersja [programu Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Wykonaj następujące kroki, aby utworzyć nowy projekt aplikacji WPF.

1. W programie Visual Studio otwórz okno dialogowe Nowy projekt. Rozwiń węzeł **Zainstalowane** i węzeł **Visual C#**, a następnie wybierz pozycję **Aplikacja WPF (.NET Framework)**.
1. Nazwij aplikację **FaceTutorial**, a następnie kliknij przycisk **OK**.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**, a następnie znajdź i zainstaluj następujący pakiet:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.5.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)

## <a name="add-the-initial-code"></a>Dodawanie początkowego kodu

W tej sekcji dodasz podstawową strukturę aplikacji bez funkcji specyficznych dla rozpoznawania twarzy.

### <a name="create-the-ui"></a>Tworzenie interfejsu użytkownika

Otwórz *MainWindow.xaml* i zastąp&mdash;zawartość następującym kodem, ten kod tworzy okno interfejsu użytkownika. I `FacePhoto_MouseMove` `BrowseButton_Click` metody są programy obsługi zdarzeń, które zostaną zdefiniowane później.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Tworzenie głównej klasy

Otwórz plik *MainWindow.xaml.cs* i dodaj przestrzenie nazw biblioteki klienta oraz inne wymagane przestrzenie nazw. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Następnie wstaw poniższy kod w klasie **MainWindow**. Ten kod tworzy **wystąpienie FaceClient** przy użyciu klucza subskrypcji i punktu końcowego.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Następnie dodaj konstruktor **MainWindow.** Sprawdza ciąg adresu URL punktu końcowego, a następnie kojarzy go z obiektem klienta.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Na koniec dodaj do klasy metody **BrowseButton_Click** i **FacePhoto_MouseMove**. Metody te odpowiadają programom obsługi zdarzeń zadeklarowanym w *pliku MainWindow.xaml*. Metoda **BrowseButton_Click** tworzy element **OpenFileDialog**, który pozwala użytkownikowi wybrać obraz jpg. Następnie wyświetla ona obraz w głównym oknie. Wstawisz pozostały kod metod **BrowseButton_Click** i **FacePhoto_MouseMove** w kolejnych krokach. Zwróć też uwagę na odwołanie `faceList`&mdash; listę obiektów **DetectedFace**. To odwołanie jest, gdzie aplikacja będzie przechowywać i wywoływać rzeczywiste dane twarzy.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Testowanie aplikacji

Naciśnij pozycję **Uruchom** w menu, aby przetestować aplikację. Po otwarciu okna aplikacji kliknij pozycję **Browse** (Przeglądaj) w lewym dolnym rogu. Powinno pojawić się okno dialogowe **Otwieranie pliku**. Wybierz obraz z systemu plików i sprawdź, czy został wyświetlony w oknie. Następnie zamknij aplikację i przejdź do kolejnego kroku.

![Zrzut ekranu przedstawiający niezmodyfikowany obraz z twarzami](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Przekazywanie obrazu i wykrywanie twarzy

Aplikacja będzie wykrywać twarze, wywołując metodę **FaceClient.Face.DetectWithStreamAsync**, która opakowuje interfejs API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) na potrzeby przekazywania obrazu lokalnego.

Wstaw następującą metodę w klasie **MainWindow** poniżej metody **FacePhoto_MouseMove**. Ta metoda definiuje listę atrybutów twarzy do pobrania i odczytuje przesłany plik obrazu do **strumienia**. Następnie oba te obiekty zostaną przekazane do wywołania metody **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Rysowanie prostokątów wokół twarzy

Dodasz teraz kod, który będzie rysował prostokąt wokół każdej wykrytej twarzy na obrazie. W klasie **MainWindow** wstaw następujący kod na końcu metody **BrowseButton_Click**, poniżej wiersza `FacePhoto.Source = bitmapSource`. Ten kod wypełnia listę wykrytych twarzy z wywołania **UploadAndDetectFaces**. Następnie zostanie narysowany prostokąt wokół każdej twarzy, a zmodyfikowany obraz zostanie wyświetlony w głównym oknie.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Opisywanie twarzy

Dodaj następującą metodę do klasy **MainWindow**, poniżej metody **UploadAndDetectFaces**. Ta metoda konwertuje pobrane atrybuty twarzy na ciąg opisujący ścianę.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Wyświetlanie opisu twarzy

Dodaj następujący kod do metody **FacePhoto_MouseMove**. Ta procedura obsługi zdarzeń wyświetla ciąg opisu twarzy w elemencie `faceDescriptionStatusBar` po zatrzymaniu kursora na prostokącie wykrytej twarzy.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

Uruchom aplikację i przejdź do obrazu zawierającego twarz. Zaczekaj kilka sekund, aby umożliwić usłudze rozpoznawania twarzy udzielenie odpowiedzi. Wokół każdej twarzy na obrazie powinien pojawić się czerwony prostokąt. Gdy przesuniesz wskaźnik myszy na prostokąt otaczający twarz, powinien pojawić się opis tej twarzy na pasku stanu.

![Zrzut ekranu przedstawiający wykryte twarze otoczone prostokątami](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono podstawowy proces korzystania z zestawu SDK platformy .NET dla usługi rozpoznawania twarzy oraz utworzono aplikację do wykrywania i oznaczania ramkami twarzy na obrazie. Teraz dowiedz się więcej o szczegółach wykrywania twarzy.

> [!div class="nextstepaction"]
> [Jak wykrywać twarze na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
