---
title: Tworzenie maszyny wirtualnej platformy Azure i zarządzanie nią przy użyciu języka Java
description: Użyj java i usługi Azure Resource Manager, aby wdrożyć maszynę wirtualną i wszystkie jej zasoby pomocnicze.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 35d5569cb36cb538585b9d2c85a392b668e9fc34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944499"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi na platformie Azure przy użyciu języka Java

[Maszyna wirtualna platformy Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potrzebuje kilku zasobów platformy Azure. W tym artykule opisano tworzenie zasobów maszyn wirtualnych, zarządzanie nimi i usuwanie ich przy użyciu języka Java. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu Maven
> * Dodawanie zależności
> * Tworzenie poświadczeń
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

To trwa około 20 minut, aby wykonać te kroki.

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

1. Jeśli jeszcze tego nie zrobiono, zainstaluj program [Java](https://aka.ms/azure-jdks).
2. Zainstaluj [Maven](https://maven.apache.org/download.cgi).
3. Utwórz nowy folder i projekt:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Dodawanie zależności

1. W `testAzureApp` folderze otwórz `pom.xml` plik i dodaj &lt;konfigurację kompilacji do projektu,&gt; aby umożliwić tworzenie aplikacji:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Dodaj zależności, które są potrzebne do uzyskania dostępu do narzędzia Azure Java SDK.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Zapisz plik.

## <a name="create-credentials"></a>Tworzenie poświadczeń

Przed rozpoczęciem tego kroku upewnij się, że masz dostęp do [jednostki usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, które są potrzebne w późniejszym kroku.

### <a name="create-the-authorization-file"></a>Tworzenie pliku autoryzacji

1. Utwórz plik `azureauth.properties` o nazwie i dodaj do niego te właściwości:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Zamień ** &lt;identyfikator&gt; subskrypcji** identyfikatorem subskrypcji, ** &lt;identyfikatorem&gt; aplikacji** identyfikatorem usługi Active Directory na identyfikator aplikacji usługi Active Directory, ** &lt;kluczem&gt; uwierzytelniania** kluczem aplikacji i ** &lt;identyfikatorem dzierżawy&gt; ** identyfikatorem dzierżawy.

2. Zapisz plik.
3. Ustaw zmienną środowiskową o nazwie AZURE_AUTH_LOCATION w powłoce z pełną ścieżką do pliku uwierzytelniania.

### <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz `App.java` plik `src\main\java\com\fabrikam` w obszarze i upewnij się, że ta instrukcja pakietu jest u góry:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. W instrukcji pakietu dodaj następujące instrukcje importu:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Aby utworzyć poświadczenia usługi Active Directory, które należy wykonać żądania, dodaj ten kod do głównej metody klasy App:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Wszystkie zasoby muszą znajdować się w [grupie zasobów](../../azure-resource-manager/management/overview.md).

Aby określić wartości dla aplikacji i utworzyć grupę zasobów, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Tworzenie zestawu dostępności

[Zestawy dostępności](tutorial-availability-sets.md) ułatwiają utrzymanie maszyn wirtualnych używanych przez aplikację.

Aby utworzyć zestaw dostępności, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP

[Publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) jest potrzebny do komunikowania się z maszyną wirtualną.

Aby utworzyć publiczny adres IP dla maszyny wirtualnej, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Maszyna wirtualna musi znajdować się w podsieci [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

Aby utworzyć podsieć i sieć wirtualną, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Tworzenie interfejsu sieciowego

Maszyna wirtualna potrzebuje interfejsu sieciowego do komunikowania się w sieci wirtualnej.

Aby utworzyć interfejs sieciowy, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Teraz, gdy utworzono wszystkie zasoby pomocnicze, można utworzyć maszynę wirtualną.

Aby utworzyć maszynę wirtualną, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> W tym samouczku utworzy się maszynę wirtualną z uruchomieniem wersji systemu operacyjnego Windows Server. Aby dowiedzieć się więcej na temat wybierania innych obrazów, zobacz [Nawigowanie i wybieranie obrazów maszyn wirtualnych platformy Azure za pomocą programu Windows PowerShell i interfejsu wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Jeśli chcesz użyć istniejącego dysku zamiast obrazu w portalu marketplace, użyj tego kodu: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Wykonywanie zadań zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można utworzyć kod do automatyzacji powtarzających się lub złożonych zadań.

Gdy trzeba zrobić coś z maszyną wirtualną, należy uzyskać wystąpienie. Dodaj ten kod do bloku try metody głównej:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Uzyskaj informacje o maszynie wirtualnej

Aby uzyskać informacje o maszynie wirtualnej, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Można zatrzymać maszynę wirtualną i zachować wszystkie jej ustawienia, ale nadal być naliczane za nią lub można zatrzymać maszynę wirtualną i przydzielić ją. Gdy maszyna wirtualna jest cofnięta alokacja, wszystkie zasoby skojarzone z nią są również cofnięte i kończy się rozliczenia dla niego.

Aby zatrzymać maszynę wirtualną bez rozdzielania jej, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Jeśli chcesz zmienić alokację maszyny wirtualnej, zmień wywołanie usługi PowerOff na ten kod:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej

Aby uruchomić maszynę wirtualną, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Ponowne rozmiary maszyny Wirtualnej

Wiele aspektów wdrażania należy wziąć pod uwagę przy podejmowaniu decyzji o rozmiarze maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](sizes.md).  

Aby zmienić rozmiar maszyny wirtualnej, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Dodawanie dysku danych do maszyny wirtualnej

Aby dodać dysk danych do maszyny wirtualnej o rozmiarze 2 GB, ma jednostkę LUN 0 i typ buforowania ReadWrite, dodaj ten kod do bloku try w metodzie głównej:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Usuwanie zasobów

Ponieważ są naliczane opłaty za zasoby używane na platformie Azure, zawsze jest dobrą praktyką, aby usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyny wirtualne i wszystkie zasoby pomocnicze, wszystko, co musisz zrobić, to usunąć grupę zasobów.

1. Aby usunąć grupę zasobów, dodaj ten kod do bloku try w metodzie głównej:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Zapisz plik App.java.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Powinno upłynąć około pięciu minut, aby ta aplikacja konsoli działała całkowicie od początku do końca.

1. Aby uruchomić aplikację, użyj tego polecenia Maven:

    ```
    mvn compile exec:java
    ```

2. Przed naciśnięciem **klawisza Enter,** aby rozpocząć usuwanie zasobów, może upłynąć kilka minut, aby zweryfikować tworzenie zasobów w witrynie Azure portal. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o korzystaniu z [bibliotek platformy Azure w języku Java](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

