---
title: Erstellen und Verwalten eines virtuellen Azure-Computers mit Java
description: Verwenden Sie Java und Azure Resource Manager, um einen virtuellen Computer und alle unterstützenden Ressourcen bereitzustellen.
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/17/2017
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: f571dc5f4f3aee2405d4968b050bd2dd92a709db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012747"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Erstellen und Verwalten von virtuellen Windows-Computern in Azure mithilfe von Java

Für einen [virtuellen Azure-Computer](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) sind mehrere unterstützende Azure-Ressourcen erforderlich. In diesem Artikel wird das Erstellen, Verwalten und Löschen virtueller Computerressourcen mithilfe von Java behandelt. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Maven-Projekts
> * Hinzufügen von Abhängigkeiten
> * Erstellen von Anmeldeinformationen
> * Erstellen von Ressourcen
> * Ausführen von Verwaltungsaufgaben
> * Löschen von Ressourcen
> * Ausführen der Anwendung

Die Ausführung dieser Schritte dauert etwa 20 Minuten.

## <a name="create-a-maven-project"></a>Erstellen eines Maven-Projekts

1. Installieren Sie [Java](/azure/developer/java/fundamentals/java-jdk-long-term-support), falls Sie dies noch nicht getan haben.
2. Installieren Sie [Maven](https://maven.apache.org/download.cgi).
3. Erstellen Sie einen neuen Ordner und das Projekt:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

1. Öffnen Sie im Ordner `testAzureApp` die Datei `pom.xml`, und fügen Sie die Buildkonfiguration zu &lt;project&gt; hinzu, um das Erstellen Ihrer Anwendung zu ermöglichen:

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

2. Fügen Sie die Abhängigkeiten hinzu, die für den Zugriff auf das Azure Java SDK erforderlich sind.

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

3. Speichern Sie die Datei .

## <a name="create-credentials"></a>Erstellen von Anmeldeinformationen

Bevor Sie mit diesem Schritt beginnen, stellen Sie sicher, dass Sie Zugriff auf einen [Active Directory-Dienstprinzipal](../../active-directory/develop/howto-create-service-principal-portal.md) haben. Sie sollten auch die Anwendungs-ID, den Authentifizierungsschlüssel und die Mandanten-ID aufzeichnen. Sie benötigen diese Angaben in einem späteren Schritt.

### <a name="create-the-authorization-file"></a>Erstellen der Autorisierungsdatei

1. Erstellen Sie eine Datei namens `azureauth.properties`, und fügen Sie ihr diese Eigenschaften hinzu:

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

    Ersetzen Sie **&lt;subscription-id&gt;** durch Ihre Abonnement-ID, **&lt;application-id&gt;** durch die Active Directory-Anwendungs-ID, **&lt;authentication-key&gt;** durch den Anwendungsschlüssel und **&lt;tenant-id&gt;** durch die Mandanten-ID.

2. Speichern Sie die Datei .
3. Legen Sie eine Umgebungsvariable namens AZURE_AUTH_LOCATION mit dem vollständigen Pfad zur Authentifizierungsdatei in Ihrer Shell fest.

### <a name="create-the-management-client"></a>Erstellen des Verwaltungsclients

1. Öffnen Sie die Datei `App.java` in `src\main\java\com\fabrikam`, und stellen Sie sicher, dass sich diese Paketanweisung ganz oben befindet:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Fügen Sie unterhalb der Paketanweisung folgende Importanweisungen hinzu:
   
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

2. Um die Active Directory-Anmeldeinformationen zu erstellen, die Sie zum Senden von Anforderungen benötigen, fügen Sie der main-Methode der App-Klasse folgenden Code hinzu:
   
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

## <a name="create-resources"></a>Erstellen von Ressourcen

### <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Alle Ressourcen müssen in einer [Ressourcengruppe](../../azure-resource-manager/management/overview.md) enthalten sein.

Um Werte für die Anwendung anzugeben und die Ressourcengruppe zu erstellen, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Erstellen der Verfügbarkeitsgruppe

[Verfügbarkeitsgruppen](tutorial-availability-sets.md) erleichtern die Verwaltung der virtuellen Computer, die von Ihrer Anwendung verwendet werden.

Um die Verfügbarkeitsgruppe zu erstellen, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Erstellen der öffentlichen IP-Adresse

Eine öffentliche [IP-Adresse](../../virtual-network/public-ip-addresses.md) ist für die Kommunikation mit dem virtuellen Computer erforderlich.

Um die öffentliche IP-Adresse für den virtuellen Computer zu erstellen, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

Ein virtueller Computer muss in einem Subnetz eines [virtuellen Netzwerks](../../virtual-network/virtual-networks-overview.md) enthalten sein.

Um ein Subnetz und ein virtuelles Netzwerk zu erstellen, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

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

### <a name="create-the-network-interface"></a>Erstellen der Netzwerkschnittstelle

Ein virtueller Computer benötigt eine Netzwerkschnittstelle, um in dem virtuellen Netzwerk kommunizieren zu können.

Um eine Netzwerkschnittstelle zu erstellen, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

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

### <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers

Nachdem Sie nun alle benötigten Ressourcen erstellt haben, können Sie einen virtuellen Computer erstellen.

Um den virtuellen Computer zu erstellen, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

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
> In diesem Tutorial wird eine virtuelle Maschine erstellt, auf dem eine Version des Windows Server-Betriebssystems ausgeführt wird. Weitere Informationen zur Auswahl von anderen Images finden Sie unter [Navigieren zu und Auswählen von Images virtueller Linux-Computer in Azure mithilfe der Befehlszeilenschnittstelle oder PowerShell](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Wenn Sie einen vorhandenen Datenträger anstelle eines Marketplace-Images verwenden möchten, verwenden Sie diesen Code: 

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

## <a name="perform-management-tasks"></a>Ausführen von Verwaltungsaufgaben

Während der Lebensdauer eines virtuellen Computers können Sie Verwaltungsaufgaben wie das Starten, Beenden oder Löschen eines virtuellen Computers ausführen. Darüber hinaus empfiehlt es sich, Code zum Automatisieren von wiederkehrenden oder komplexen Aufgaben zu erstellen.

Wenn Sie mit dem virtuellen Computer Aufgaben ausführen müssen, benötigen Sie eine Instanz des Computers. Fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Abrufen von Informationen zur VM

Um Informationen zum virtuellen Computer abzurufen, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

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

### <a name="stop-the-vm"></a>Beenden des virtuellen Computers

Sie können einen virtuellen Computer beenden und sämtliche Einstellungen beibehalten – dafür werden allerdings weiterhin Gebühren berechnet –, oder Sie können einen virtuellen Computer beenden und die Zuordnung aufheben. Wenn die Zuordnung eines virtuellen Computers aufgehoben wird, wird auch die Zuordnung aller mit diesem Computer verknüpften Ressourcen aufgehoben, und die Abrechnung wird beendet.

Um den virtuellen Computer zu beenden, ohne seine Zuordnung aufzuheben, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Wenn Sie die Zuordnung des virtuellen Computers aufheben möchten, ändern Sie den Aufruf zum Herunterfahren in folgenden Code:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Starten der VM

Um den virtuellen Computer zu starten, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Ändern der Größe der VM

Viele Aspekte der Bereitstellung müssen berücksichtigt werden, wenn Sie die Größe für den virtuellen Computer festlegen. Weitere Informationen finden Sie unter [VM-Größen](../sizes.md).  

Um die Größe des virtuellen Computers zu ändern, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Hinzufügen eines Datenträgers zur VM

Um dem virtuellen Computer einen 2 GB großen Datenträger hinzuzufügen, der die LUN 0 und den Cachetyp „ReadWrite“ aufweist, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Löschen von Ressourcen

Da in Azure die genutzten Ressourcen in Rechnung gestellt werden, empfiehlt es sich grundsätzlich, nicht mehr benötigte Ressourcen zu löschen. Um den virtuellen Computer und alle unterstützenden Ressourcen zu löschen, müssen Sie lediglich die Ressourcengruppe löschen.

1. Um die Ressourcengruppe zu löschen, fügen Sie dem try-Block in der main-Methode folgenden Code hinzu:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Speichern Sie die Datei „App.java“.

## <a name="run-the-application"></a>Ausführen der Anwendung

Die vollständige Ausführung dieser Konsolenanwendung sollte etwa fünf Minuten dauern.

1. Um die Anwendung auszuführen, verwenden Sie folgenden Maven-Befehl:

    ```
    mvn compile exec:java
    ```

2. Bevor Sie die **EINGABETASTE** drücken, um das Löschen der Ressourcen zu starten, können Sie sich ein paar Minuten Zeit nehmen, um die Erstellung der Ressourcen im Azure-Portal zu überprüfen. Klicken Sie auf den Bereitstellungsstatus, um Informationen zur Bereitstellung anzuzeigen.


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr zur Verwendung der [Azure-Bibliotheken für Java](/java/azure/java-sdk-azure-overview).