---
title: Erstellen und Verwalten einer Windows-VM in Azure mithilfe von Python
description: Erfahren Sie, wie Sie Python zum Erstellen und Verwalten einer Windows-VM in Azure verwenden.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 06/22/2017
ms.author: cynthn
ms.custom: devx-track-python
ms.openlocfilehash: a7117d30eb1774753f21e82ad5f812a7309d8386
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483095"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Erstellen und Verwalten von Windows-VMs in Azure mithilfe von Python

Für einen [virtuellen Azure-Computer](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) sind mehrere unterstützende Azure-Ressourcen erforderlich. In diesem Artikel wird das Erstellen, Verwalten und Löschen von VM-Ressourcen mithilfe von Python behandelt. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Visual Studio-Projekts
> * Installieren von Paketen
> * Erstellen von Anmeldeinformationen
> * Erstellen von Ressourcen
> * Ausführen von Verwaltungsaufgaben
> * Löschen von Ressourcen
> * Ausführen der Anwendung

Die Ausführung dieser Schritte dauert etwa 20 Minuten.

## <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

1. Wenn Sie dies noch nicht getan haben, installieren Sie [Visual Studio](/visualstudio/install/install-visual-studio). Wählen Sie auf der Seite „Workloads“ **Python-Entwicklung** aus, und klicken Sie dann auf **Installieren**. In der Zusammenfassung können Sie sehen, dass **Python 3 64-Bit (3.6.0)** automatisch für Sie ausgewählt wurde. Wenn Sie Visual Studio bereits installiert haben, können Sie die Python-Workload mit dem Visual Studio-Startprogramm hinzufügen.
2. Klicken Sie nach dem Installieren und Starten von Visual Studio auf **Datei** > **Neu** > **Projekt**.
3. Klicken Sie auf **Vorlagen** > **Python** > **Python-Anwendung**, geben Sie *myPythonProject* als Namen für das Projekt ein, wählen Sie den Speicherort des Projekts aus, und klicken Sie dann auf **OK**.

## <a name="install-packages"></a>Installieren von Paketen

1. Klicken Sie im Projektmappen-Explorer unter *myPythonProject* mit der rechten Maustaste auf **Python-Umgebungen**, und wählen Sie dann **Virtuelle Umgebung hinzufügen** aus.
2. Akzeptieren Sie auf dem Bildschirm „Virtuelle Umgebung hinzufügen“ den Standardnamen *env*, stellen Sie sicher, dass *Python 3.6 (64-Bit)* als Basisinterpreter ausgewählt ist, und klicken Sie dann auf **Erstellen**.
3. Klicken Sie mit der rechten Maustaste auf die Umgebung *env*, die Sie erstellt haben, klicken Sie auf **Python-Paket installieren**, geben Sie *Azure* in das Suchfeld ein, und drücken Sie dann die EINGABETASTE.

Im Ausgabefenster sollte angezeigt werden, dass die Azure-Pakete erfolgreich installiert wurden. 

## <a name="create-credentials"></a>Erstellen von Anmeldeinformationen

Bevor Sie mit diesem Schritt beginnen, stellen Sie sicher, dass Sie über einen [Active Directory-Dienstprinzipal](../../active-directory/develop/howto-create-service-principal-portal.md) verfügen. Sie sollten auch die Anwendungs-ID, den Authentifizierungsschlüssel und die Mandanten-ID aufzeichnen. Sie benötigen diese Angaben in einem späteren Schritt.

1. Öffnen Sie die Datei *myPythonProject.py*, die erstellt wurde, und fügen Sie dann diesen Code hinzu, damit Ihre Anwendung ausgeführt werden kann:

    ```python
    if __name__ == "__main__":
    ```

2. Um den erforderlichen Code zu importieren, fügen Sie diese Anweisungen am Anfang der PY-Datei hinzu:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Fügen Sie als Nächstes in der PY-Datei nach den Importanweisungen Variablen hinzu, um im Code verwendete allgemeine Werte anzugeben:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Ersetzen Sie **subscription-id** durch Ihre Abonnement-ID.

4. Um die Active Directory-Anmeldeinformationen zu erstellen, die Sie benötigen, um Anforderungen zu erstellen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Ersetzen Sie **application-id**, **authentication-key** und **tenant-id** mit den Werten, die Sie zuvor beim Erstellen Ihres Azure Active Directory-Dienstprinzipals erfasst haben.

5. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Erstellen von Ressourcen
 
### <a name="initialize-management-clients"></a>Initialisieren von Verwaltungsclients

Verwaltungsclients sind zum Erstellen und Verwalten von Ressourcen mithilfe des Python-SDK in Azure erforderlich. Um den Verwaltungsclient zu erstellen, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Erstellen der VM und der unterstützenden Ressourcen

Alle Ressourcen müssen in einer [Ressourcengruppe](../../azure-resource-manager/management/overview.md) enthalten sein.

1. Um eine Ressourcengruppe zu erstellen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Verfügbarkeitsgruppen](tutorial-availability-sets.md) erleichtern die Verwaltung der virtuellen Computer, die von Ihrer Anwendung verwendet werden.

1. Um eine Verfügbarkeitsgruppe zu erstellen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

Eine öffentliche [IP-Adresse](../../virtual-network/public-ip-addresses.md) ist für die Kommunikation mit dem virtuellen Computer erforderlich.

1. Um eine öffentliche IP-Adresse für den virtuellen Computer zu erstellen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Ein virtueller Computer muss in einem Subnetz eines [virtuellen Netzwerks](../../virtual-network/virtual-networks-overview.md) enthalten sein.

1. Um ein virtuelles Netzwerk zu erstellen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Um ein Subnetz zum virtuellen Netzwerk hinzuzufügen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Ein virtueller Computer benötigt eine Netzwerkschnittstelle, um in dem virtuellen Netzwerk kommunizieren zu können.

1. Um eine Netzwerkschnittstelle zu erstellen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Nachdem Sie nun alle benötigten Ressourcen erstellt haben, können Sie einen virtuellen Computer erstellen.

1. Um den virtuellen Computer zu erstellen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > In diesem Tutorial wird eine virtuelle Maschine erstellt, auf dem eine Version des Windows Server-Betriebssystems ausgeführt wird. Weitere Informationen zur Auswahl von anderen Images finden Sie unter [Navigieren zu und Auswählen von Images virtueller Linux-Computer in Azure mithilfe der Befehlszeilenschnittstelle oder PowerShell](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Ausführen von Verwaltungsaufgaben

Während der Lebensdauer eines virtuellen Computers können Sie Verwaltungsaufgaben wie das Starten, Beenden oder Löschen eines virtuellen Computers ausführen. Darüber hinaus empfiehlt es sich, Code zum Automatisieren von wiederkehrenden oder komplexen Aufgaben zu erstellen.

### <a name="get-information-about-the-vm"></a>Abrufen von Informationen zur VM

1. Um Informationen zum virtuellen Computer abzurufen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Beenden des virtuellen Computers

Sie können einen virtuellen Computer beenden und sämtliche Einstellungen beibehalten – dafür werden allerdings weiterhin Gebühren berechnet –, oder Sie können einen virtuellen Computer beenden und die Zuordnung aufheben. Wenn die Zuordnung eines virtuellen Computers aufgehoben wird, wird auch die Zuordnung aller mit diesem Computer verknüpften Ressourcen aufgehoben, und die Abrechnung wird beendet.

1. Um den virtuellen Computer zu beenden, ohne die Zuordnung aufzuheben, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Wenn Sie die Zuordnung des virtuellen Computers aufheben möchten, ändern Sie den power_off-Aufruf in diesen Code:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Starten der VM

1. Um den virtuellen Computer zu starten, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Ändern der Größe der VM

Viele Aspekte der Bereitstellung müssen berücksichtigt werden, wenn Sie die Größe für den virtuellen Computer festlegen. Weitere Informationen finden Sie unter [VM-Größen](../sizes.md).

1. Um die Größe des virtuellen Computers zu ändern, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Hinzufügen eines Datenträgers zur VM

Virtuelle Computer können über einen oder mehrere [Datenträger](../managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) verfügen, die als VHDs gespeichert werden.

1. Um einen Datenträger zum virtuellen Computer hinzuzufügen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Löschen von Ressourcen

Da in Azure die genutzten Ressourcen in Rechnung gestellt werden, empfiehlt es sich grundsätzlich, nicht mehr benötigte Ressourcen zu löschen. Um den virtuellen Computer und alle unterstützenden Ressourcen zu löschen, müssen Sie lediglich die Ressourcengruppe löschen.

1. Um die Ressourcengruppe und alle Ressourcen zu löschen, fügen Sie diese Funktion nach den Variablen in der PY-Datei hinzu:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Um die Funktion aufzurufen, die Sie zuvor hinzugefügt haben, fügen Sie diesen Code unter der **if**-Anweisung am Ende der PY-Datei hinzu:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Speichern Sie *myPythonProject.py*.

## <a name="run-the-application"></a>Ausführen der Anwendung

1. Um die Konsolenanwendung auszuführen, klicken Sie in Visual Studio auf **Starten**.

2. Drücken Sie die **EINGABETASTE**, nachdem der Status der einzelnen Ressourcen zurückgegeben wurde. In den Statusinformationen sollte **Erfolgreich** als Bereitstellungsstatus angezeigt werden. Nachdem der virtuelle Computer erstellt wurde, können Sie alle Ressourcen löschen, die Sie erstellt haben. Bevor Sie die **EINGABETASTE** drücken, um mit dem Löschen der Ressourcen zu beginnen, können Sie sich ein paar Minuten Zeit nehmen, um deren Erstellung im Azure-Portal zu überprüfen. Wenn das Azure-Portal geöffnet ist, müssen Sie möglicherweise das Blatt aktualisieren, um neue Ressourcen zu sehen.  

    Die vollständige Ausführung dieser Konsolenanwendung sollte etwa fünf Minuten dauern. Nach Abschluss der Anwendung dauert es möglicherweise einige Minuten, bevor alle Ressourcen und die Ressourcengruppe gelöscht werden.


## <a name="next-steps"></a>Nächste Schritte

- Falls bei der Bereitstellung Probleme aufgetreten sind, empfiehlt sich unter Umständen die Lektüre des Artikels [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](../../azure-resource-manager/templates/deployment-history.md).
- Erfahren Sie mehr über die [Azure Python-Bibliothek](/python/api/overview/azure/?view=azure-python).