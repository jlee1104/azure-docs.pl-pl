---
title: 'Łączenie się z siecią wirtualną przy użyciu uwierzytelniania certyfikatu sieci VPN & P2S: portal'
titleSuffix: Azure VPN Gateway
description: Bezpieczne łączenie klientów systemów Windows, Mac OS X i Linux z siecią wirtualną platformy Azure przy użyciu certyfikatów P2S i certyfikatów z podpisem własnym lub certyfikatów urzędu certyfikacji. W tym artykule jest używana witryna Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: 013ebc2a1343c8eab3d477023e36660c93fa6da5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244487"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Konfigurowanie połączenia sieci VPN typu punkt-lokacja do sieci wirtualnej przy użyciu natywnego uwierzytelniania certyfikatów platformy Azure: portal Azure

Ten artykuł ułatwia bezpieczne łączenie poszczególnych klientów z systemem Windows, Linux lub Mac OS X z siecią wirtualną platformy Azure. Połączenia sieci VPN typu punkt-lokacja przydają się w przypadku, gdy celem użytkownika jest połączenie się z siecią wirtualną z lokalizacji zdalnej, podczas pracy zdalnej z domu lub konferencji. Możesz również użyć połączenia typu punkt-lokacja zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani publicznego adresu IP. Połączenie typu punkt-lokacja tworzy połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol) lub IKEv2. Aby uzyskać więcej informacji na temat połączeń sieci VPN typu punkt-lokacja, zobacz [About Point-to-Site VPN (Informacje o sieci VPN typu punkt-lokacja)](point-to-site-about.md).

![Łączenie komputera z siecią wirtualną platformy Azure — diagram połączenie typu punkt-lokacja](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architektura

Natywne połączenia uwierzytelniania certyfikatów platformy Azure typu punkt-lokacja używają następujących elementów, które można skonfigurować w ramach tego ćwiczenia:

* Brama sieci VPN oparta na trasie.
* Klucz publiczny (plik cer) dla certyfikatu głównego, przekazany na platformę Azure. Przekazany certyfikat jest uznawany za certyfikat zaufany i używany do uwierzytelniania.
* Certyfikat klienta generowany na podstawie certyfikatu głównego. Certyfikat klienta instalowany na każdym komputerze klienckim, który będzie łączyć się z siecią wirtualną. Ten certyfikat jest używany do uwierzytelniania klientów.
* Konfiguracja klienta sieci VPN. Pliki konfiguracji klienta sieci VPN zawierają informacje niezbędne klientowi do połączenia się z siecią wirtualną. Pliki te służą do konfigurowania istniejącego, natywnego dla systemu operacyjnego klienta sieci VPN . Każdy klient, który nawiązuje połączenie, musi być skonfigurowany przy użyciu ustawień w tych plikach konfiguracji.

#### <a name="example-values"></a><a name="example"></a>Przykładowe wartości

Następujących wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w niniejszym artykule:

* **Nazwa sieci wirtualnej:** VNet1
* **Przestrzeń adresowa:** 10.1.0.0/16<br>W tym przykładzie zostanie wykorzystana tylko jedna przestrzeń adresowa. Istnieje możliwość użycia więcej niż jednej przestrzeni adresowej dla sieci wirtualnej.
* **Nazwa podsieci:** Frontend
* **Zakres adresów podsieci:** 10.1.0.0/24
* **Subskrypcja:** jeśli masz więcej niż jedną subskrypcję, sprawdź, czy korzystasz z właściwej.
* **Grupa zasobów:** TestRG1
* **Lokalizacja:** Wschodnie stany USA
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nazwa bramy sieci wirtualnej:** Wirtualna 1GW
* **Typ bramy:** VPN
* **Typ sieci VPN:** oparta na trasach
* **Publiczny adres IP:** VNet1GWpip
* **Typ połączenia:** Punkt-lokacja
* **Pula adresów klienta:** 172.16.201.0/24<br>Klienci sieci VPN połączeni z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z puli adresów klientów.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. Tworzenie sieci wirtualnej

Przed rozpoczęciem sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Tworzenie bramy sieci wirtualnej

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

>[!NOTE]
>Jednostka SKU bramy podstawowej nie obsługuje uwierzytelniania IKEv2 lub RADIUS. Jeśli planujesz połączenie klientów mac z siecią wirtualną, nie należy używać podstawowej jednostki SKU.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. Generowanie certyfikatów

Certyfikaty są używane przez platformę Azure do uwierzytelniania klientów łączących się z siecią wirtualną za pośrednictwem połączenia sieci VPN typu punkt-lokacja. Po uzyskaniu certyfikatu głównego należy [przekazać](#uploadfile) informacje o kluczu publicznym do platformy Azure. Certyfikat główny jest następnie uznawany przez platformę Azure za zaufany dla połączeń typu punkt-lokacja z siecią wirtualną. Ponadto certyfikaty klienta są generowane na podstawie zaufanego certyfikatu głównego, a następnie instalowane na każdym komputerze klienckim. Certyfikat klienta jest używany do uwierzytelniania klienta, gdy inicjuje on połączenie z siecią wirtualną. 

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. Uzyskaj plik cer dla certyfikatu głównego

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. Generowanie certyfikatu klienta

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. Dodaj pulę adresów klienta

Pula adresów klienta to określony przez Ciebie zakres prywatnych adresów IP. Klienci łączący się dynamicznie przez połączenie VPN typu punkt-lokacja otrzymują adres IP z tego zakresu. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której się łączysz, ani na sieć wirtualną, z którą chcesz się łączyć. Jeśli skonfigurujesz wiele protokołów, a protokół SSTP jest jednym z protokołów, skonfigurowana pula adresów jest dzielona między skonfigurowane protokoły jednakowo.

1. Po utworzeniu bramy sieci wirtualnej przejdź do sekcji **Ustawienia** na stronie bramy sieci wirtualnej. W sekcji **Ustawienia** wybierz pozycję **Konfiguracja typu punkt-lokacja**. Wybierz **pozycję Konfiguruj teraz,** aby otworzyć stronę konfiguracji.

   ![Strona połączenia typu punkt-lokacja](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-configure.png "Konfiguracja z punktu do lokalizacji teraz")
2. Na stronie **konfiguracji punkt-lokacja** można skonfigurować różne ustawienia. Jeśli na tej stronie nie jest widoczny typ tunelu lub typ uwierzytelniania, brama korzysta z podstawowej jednostki SKU. Podstawowa jednostka SKU nie obsługuje uwierzytelniania za pomocą protokołu IKEv2 ani usługi RADIUS. Jeśli chcesz użyć tych ustawień, musisz usunąć i ponownie utworzyć bramę przy użyciu innej jednostki SKU bramy.

   [![Strona konfiguracji punkt-lokacja](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-address.png "określanie puli adresów")](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-expanded.png#lightbox)
3. W **polu Pula adresów** dodaj zakres prywatnych adresów IP, którego chcesz użyć. Klienci sieci VPN dynamicznie otrzymują adres IP z określonego zakresu. Minimalna maska podsieci wynosi 29 bitów dla aktywnej/pasywnej i 28-bitowej dla konfiguracji aktywnej/aktywnej.
4. Przejdź do następnej sekcji, aby skonfigurować typ tunelu.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. Konfigurowanie typu tunelu

Można wybrać typ tunelu. Opcje tunelu to OpenVPN, SSTP i IKEv2.

* Klient strongSwan w systemach Android i Linux oraz natywny klient sieci VPN IKEv2 w systemach iOS i OSX będą używać do łączenia się tylko tuneli IKEv2.
* Klienci systemu Windows najpierw spróbujĄ IKEv2, a jeśli to się nie połączy, wracają do protokołu SSTP.
* Za pomocą klienta OpenVPN można połączyć się z typem tunelu OpenVPN.

![Typ tunelu](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel.png "określanie typu tunelu")

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. Konfigurowanie typu uwierzytelniania

W przypadku **typu Uwierzytelnianie**wybierz **certyfikat platformy Azure**.

  ![Typ uwierzytelniania](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication-type.png "określanie typu uwierzytelniania")

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Prześlij dane certyfikatu publicznego certyfikatu głównego

Możesz przekazać łącznie do 20 zaufanych certyfikatów głównych. Po przekazaniu danych certyfikatu publicznego platforma Azure może używać go do uwierzytelniania klientów, którzy mają zainstalowany certyfikat klienta wygenerowany na podstawie zaufanego certyfikatu głównego. Przekaż informacje o kluczu publicznym certyfikatu głównego na platformę Azure.

1. Certyfikaty są dodawane na stronie **Konfiguracja punktu do lokacji** w sekcji **Certyfikat główny**.
2. Upewnij się, że certyfikat główny został wyeksportowany jako plik X.509 (cer) zaszyfrowany z użyciem algorytmu Base-64. Certyfikat należy wyeksportować w takim formacie, aby możliwe było jego otwarcie za pomocą edytora tekstów.
3. Otwórz certyfikat za pomocą edytora tekstów, takiego jak Notatnik. Podczas kopiowania danych dotyczących certyfikatu upewnij się, że kopiujesz tekst jako jeden ciągły wiersz bez znaków powrotu karetki i wysuwu wiersza. Może zajść potrzeba zmodyfikowania widoku w edytorze tekstu na potrzeby pokazywania symboli lub pokazywania wszystkich znaków, aby wyświetlić znaki powrotu karetki i wysuwu wiersza. Skopiuj tylko następującą sekcję jako jeden ciągły wiersz:

   ![Dane dotyczące certyfikatu](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png "kopiowanie danych certyfikatu głównego")
4. Wklej dane dotyczące certyfikatu w polu **Dane certyfikatu publicznego**. **Nazwij** certyfikat, a następnie wybierz pozycję **Zapisz**. Możesz dodać maksymalnie 20 zaufanych certyfikatów głównych.

   ![Wklejenie danych certyfikatu](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png "wklejanie danych certyfikatu")
5. Wybierz **pozycję Zapisz** u góry strony, aby zapisać wszystkie ustawienia konfiguracji.

   ![Zapisz konfigurację](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png "zapisz konfigurację")

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. Zainstaluj wyeksportowany certyfikat klienta

Jeśli chcesz utworzyć połączenie punkt-lokacja z komputerem klienckim innym niż użyty do wygenerowania certyfikatów klienta, należy zainstalować certyfikat klienta. Podczas instalowania certyfikatu klienta potrzebne jest hasło, które zostało utworzone w trakcie eksportowania certyfikatu klienta.

Upewnij się, że certyfikat klienta został wyeksportowany jako plik pfx wraz z całym łańcuchem certyfikatów (jest to ustawienie domyślne). W przeciwnym razie informacje o certyfikacie głównym nie będą dostępne na komputerze klienckim i klient nie będzie mógł się poprawnie uwierzytelnić.

Aby zapoznać się z krokami instalacji, zobacz [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md) (Instalowanie certyfikatu klienta).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. Generowanie i instalowanie pakietu konfiguracyjnego klienta sieci VPN

Pliki konfiguracji klienta sieci VPN zawierają ustawienia do konfigurowania urządzeń pod kątem łączenia się z siecią wirtualną przez połączenie punkt-lokacja. Aby uzyskać instrukcje dotyczące generowania i instalowania plików konfiguracji klienta sieci VPN, zobacz [Create and install VPN client configuration files for native Azure certificate authentication P2S configurations](point-to-site-vpn-client-configuration-azure-cert.md) (Tworzenie i instalowanie plików konfiguracji klienta sieci VPN dla konfiguracji połączeń punkt-lokacja z natywnym uwierzytelnianiem certyfikatów platformy Azure).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Połącz się z platformą Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Aby połączyć się z klienta sieci VPN w systemie Windows

>[!NOTE]
>Musisz mieć uprawnienia administratora na komputerze klienckim z systemem Windows, z którym nawiązujesz połączenie.
>
>

1. Aby nawiązać połączenie z siecią wirtualną na komputerze klienckim, przejdź do połączeń sieci VPN i wyszukaj wcześniej utworzone połączenie sieci VPN. Połączenie będzie miało taką samą nazwę jak sieć wirtualna. Wybierz przycisk **Połącz**. Może pojawić się komunikat podręczny, który odwołuje się do użycia certyfikatu. Wybierz **pozycję Kontynuuj,** aby używać uprawnień podwyższonego poziomu.

2. Na stronie stanu **Połączenie** wybierz przycisk **Połącz**, aby rozpocząć połączenie. Jeśli widzisz ekran **Wybierz certyfikat**, sprawdź, czy wyświetlany certyfikat klienta to ten, który ma zostać użyty do nawiązania połączenia. Jeśli tak nie jest, użyj strzałki rozwijanej, aby wybrać właściwy certyfikat, a następnie wybierz **przycisk OK**.

   ![Łączenie klienta sieci VPN z platformą Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png "nawiązywania połączenia")
3. Połączenie zostało ustanowione.

   ![Ustanowiono połączenie](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png "nawiązane połączenie")

#### <a name="troubleshoot-windows-p2s-connections"></a>Rozwiązywanie problemów z połączeniami punkt-lokacja w systemie Windows

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Aby połączyć się z klienta sieci VPN na komputerze Mac

W oknie dialogowym Sieć znajdź profil klienta, którego chcesz użyć, określ ustawienia z [pliku VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac), a następnie wybierz pozycję **Połącz**.

Szczegółowe instrukcje można uzyskać szczegółowe instrukcje w ciemięgi [— Mac (OS X).](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) Jeśli występują problemy z nawiązaniem połączenia, sprawdź, czy brama sieci wirtualnej nie używa podstawowej jednostki SKU. Podstawowa jednostka SKU nie jest obsługiwana dla klientów komputerów Mac.

  ![Połączenie z komputerem Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png "Połącz")

## <a name="to-verify-your-connection"></a><a name="verify"></a>Aby zweryfikować połączenie

Te instrukcje dotyczą klientów w systemie Windows.

1. Aby sprawdzić, czy połączenie sieci VPN jest aktywne, otwórz wiersz polecenia z podwyższonym poziomem uprawnień, a następnie uruchom polecenie *ipconfig/all*.
2. Przejrzyj wyniki. Zwróć uwagę na fakt, że otrzymany adres IP należy do określonej podczas konfiguracji połączenia punkt-lokacja puli adresów klienta sieci VPN. Wyniki są podobne, jak w następującym przykładzie:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

Te instrukcje dotyczą klientów w systemie Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Dodawanie lub usuwanie zaufanych certyfikatów głównych

Zaufane certyfikaty główne można dodawać do platformy Azure lub z niej usuwać. Po usunięciu certyfikatu głównego klienci, którzy mają certyfikat wygenerowany na podstawie tego certyfikatu głównego, nie będą w stanie się uwierzytelnić i w związku z tym nie będą mogli nawiązywać połączeń. Jeśli chcesz, aby klienci mogli uwierzytelniać się i nawiązywać połączenia, musisz zainstalować nowy certyfikat klienta wygenerowany na podstawie certyfikatu głównego, który jest traktowany jako zaufany przez platformę Azure (przekazany na tę platformę).

### <a name="to-add-a-trusted-root-certificate"></a>Aby dodać zaufany certyfikat główny

Na platformie Azure można dodać maksymalnie 20 plików cer zaufanego certyfikatu głównego. Aby uzyskać instrukcje, zobacz sekcję [Przekazywanie zaufanego certyfikatu głównego](#uploadfile) w tym artykule.

### <a name="to-remove-a-trusted-root-certificate"></a>Usuwanie zaufanego certyfikatu głównego

1. Aby usunąć zaufany certyfikat główny, przejdź do strony **Konfiguracja punktu do lokacji** dla bramy Twojej sieci wirtualnej.
2. W sekcji **Certyfikat główny** na stronie znajdź certyfikat, który chcesz usunąć.
3. Wybierz wielokropek obok certyfikatu, a następnie wybierz "Usuń".

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Aby odwołać certyfikat klienta

Certyfikaty klienta można odwołać. Lista odwołania certyfikatów umożliwia dokonanie selektywnej odmowy połączenia punkt-lokacja w oparciu o indywidualne certyfikaty klienta. Różni się to od usuwania zaufanego certyfikatu głównego. Jeśli usuniesz plik cer zaufanego certyfikatu głównego z platformy Azure, spowoduje to odwołanie dostępu dla wszystkich certyfikatów klienta wygenerowanych lub podpisanych przez odwołany certyfikat główny. Odwołanie certyfikatu klienta zamiast certyfikatu głównego pozwala dalej używać innych certyfikatów wygenerowanych na podstawie certyfikatu głównego do uwierzytelniania.

Częstą praktyką jest użycie certyfikatu głównego do zarządzania dostępem na poziomach zespołu lub organizacji przy jednoczesnym korzystaniu z odwołanych certyfikatów klienta dla bardziej precyzyjnej kontroli dostępu poszczególnych użytkowników.

### <a name="revoke-a-client-certificate"></a>Odwołanie certyfikatu klienta

Certyfikat klienta można odwołać przez dodanie odcisku palca do listy odwołania.

1. Pobierz odcisk palca certyfikatu klienta. Aby uzyskać więcej informacji, zobacz [Jak pobrać odcisk palca certyfikatu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Skopiuj informacje do edytora tekstu i usuń wszelkie spacje, tak aby powstał ciąg bez odstępów.
3. Przejdź do strony **Konfiguracja punktu do lokacji** bramy sieci wirtualnej. Korzystając z tej samej strony, [przekazano zaufany certyfikat główny](#uploadfile).
4. W sekcji **Odwołane certyfikaty** wprowadź przyjazną nazwę certyfikatu (nie musi to być nazwa pospolita certyfikatu).
5. Skopiuj i wklej ciąg odcisku palca do pola **Odcisk palca**.
6. Odcisk palca zostanie zweryfikowany i automatycznie dodany do listy odwołania. Na ekranie zobaczysz komunikat informujący o aktualizowaniu listy. 
7. Po zakończeniu aktualizowania nie będzie można już używać certyfikatu do nawiązywania połączeń. Klienci, którzy spróbują połączyć się za pomocą tego certyfikatu, zobaczą komunikat informujący o tym, że certyfikat nie jest już ważny.

## <a name="point-to-site-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące punktu do lokalizacji

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Następne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/) (Maszyny wirtualne). Aby dowiedzieć się więcej o sieci i maszynach wirtualnych, zobacz [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshooting Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
