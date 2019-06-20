---
title: Verwenden der Dienstverwaltungs-API (Python) – Featureleitfaden
description: Hier erfahren Sie, wie die programmgesteuerte Durchführung gängiger Dienstverwaltungsaufgaben aus Python funktioniert.
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 573c6d3ded8fea58e0c9ba1afa7da2d8dd0fce91
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525528"
---
# <a name="use-service-management-from-python"></a>Verwenden der Dienstverwaltung aus Python
In diesem Leitfaden wird die programmgesteuerte Durchführung gängiger Dienstverwaltungsaufgaben aus Python erläutert. Die **ServiceManagementService**-Klasse im [Azure SDK für Python](https://github.com/Azure/azure-sdk-for-python) unterstützt den programmgesteuerten Zugriff auf viele der Dienstverwaltungsfunktionen, die im [Azure-Portal][management-portal] zur Verfügung stehen. Sie können mit diesen Funktionen Clouddienste, Bereitstellungen, Datenverwaltungsdienste und virtuelle Computer erstellen, aktualisieren und löschen. Diese Funktionalität kann bei der Erstellung von Anwendungen hilfreich sein, die programmgesteuert auf Dienstverwaltungsfunktionen zugreifen müssen.

## <a name="WhatIs"> </a>Was ist die Dienstverwaltung?
Die Azure-Dienstverwaltungs-API bietet programmgesteuerten Zugriff auf viele der im [Azure-Portal][management-portal] verfügbaren Dienstverwaltungsfunktionen. Mithilfe des Azure SDK für Python können Sie Ihre Clouddienste und Speicherkonten verwalten.

Um die Dienstverwaltungs-API verwenden zu können, müssen Sie [ein Azure-Konto erstellen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"></a>Konzepte
Das Azure SDK für Python umfasst die [Dienstverwaltungs-API][svc-mgmt-rest-api], die eine REST-API ist. Alle API-Vorgänge werden über SSL ausgeführt und mithilfe von X.509 v3-Zertifikaten gegenseitig authentifiziert. Auf den Verwaltungsdienst kann von einem Dienst, der in Azure ausgeführt wird, zugegriffen werden. Der Zugriff kann auch direkt über das Internet erfolgen, und zwar von jeder Anwendung aus, die HTTPS-Anforderungen senden und HTTPS-Antworten empfangen kann.

## <a name="Installation"></a>Installation
Alle in diesem Artikel beschriebenen Features sind im `azure-servicemanagement-legacy`-Paket verfügbar, das Sie mit pip installieren können. Weitere Informationen zur Installation (z.B. wenn Python für Sie neu ist) finden Sie unter [Installieren von Python und Azure SDK](../python-how-to-install.md).

## <a name="Connect"> </a>Herstellen einer Verbindung mit der Dienstverwaltung
Um eine Verbindung mit dem Dienstverwaltungs-Endpunkt herzustellen, benötigen Sie Ihre Azure-Abonnement-ID und ein gültiges Verwaltungszertifikat. Ihre Abonnement-ID können Sie über das [Azure-Portal][management-portal] abrufen.

> [!NOTE]
> Es ist möglich, bei der Ausführung unter Windows Zertifikate zu verwenden, die mit OpenSSL erstellt wurden. Dafür ist mindestens Python 2.7.4 erforderlich. Es wird empfohlen, OpenSSL anstelle von PFX zu verwenden, da die Unterstützung für die PFX-Zertifikate wahrscheinlich zu einem späteren Zeitpunkt entfernt wird.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Verwaltungszertifikate unter Windows/Mac/Linux (OpenSSL)
Sie können Ihr Verwaltungszertifikat mithilfe von [OpenSSL](https://www.openssl.org/) erstellen. Sie müssen zwei Zertifikate erstellen, eins für den Server (eine `.cer`-Datei) und eins für den Client (eine `.pem`-Datei). Führen Sie zum Erstellen der `.pem` -Datei Folgendes aus:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Führen Sie zum Erstellen des `.cer` -Zertifikats Folgendes aus:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Weitere Informationen zu Azure-Zertifikaten finden Sie unter [Übersicht über Zertifikate für Azure Cloud Services](cloud-services-certs-create.md). Eine vollständige Beschreibung von OpenSSL-Parametern finden Sie in der Dokumentation unter [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html).

Nach der Erstellung dieser Dateien laden Sie die `.cer`-Datei in Azure hoch. Wählen Sie im [Azure-Portal][management-portal] auf der Registerkarte **Einstellungen** die Option **Hochladen** aus. Notieren Sie sich, wo Sie die `.pem`-Datei gespeichert haben.

Erstellen Sie nach dem Abrufen Ihrer Abonnement-ID ein Zertifikat, laden Sie die `.cer`-Datei in Azure hoch, und stellen Sie eine Verbindung mit dem Azure-Verwaltungsendpunkt her. Sie stellen die Verbindung her, indem Sie die Abonnement-ID und den Pfad zur `.pem`-Datei an **ServiceManagementService** übergeben.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Im vorstehenden Beispiel ist `sms` ein **ServiceManagementService** -Objekt. Die **ServiceManagementService** -Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten.

### <a name="management-certificates-on-windows-makecert"></a>Verwaltungszertifikate unter Windows (MakeCert)
Mithilfe von `makecert.exe` können Sie auf Ihrem Computer ein selbstsigniertes Verwaltungszertifikat generieren. Öffnen Sie eine **Visual Studio-Eingabeaufforderung** als **Administrator**, und geben Sie den folgenden Befehl ein. Ersetzen Sie dabei *AzureCertificate* durch den gewünschten Zertifikatnamen:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Mit dem Befehl wird die `.cer`-Datei erstellt und im **persönlichen** Zertifikatspeicher installiert. Weitere Informationen finden Sie unter [Übersicht über Zertifikate für Azure Cloud Services](cloud-services-certs-create.md).

Nach der Erstellung des Zertifikats laden Sie die `.cer`-Datei in Azure hoch. Wählen Sie im [Azure-Portal][management-portal] auf der Registerkarte **Einstellungen** die Option **Hochladen** aus.

Erstellen Sie nach dem Abrufen Ihrer Abonnement-ID ein Zertifikat, laden Sie die `.cer`-Datei in Azure hoch, und stellen Sie eine Verbindung mit dem Azure-Verwaltungsendpunkt her. Sie stellen die Verbindung her, indem Sie die Abonnement-ID und den Speicherort des Zertifikats in Ihrem **persönlichen** Zertifikatspeicher an **ServiceManagementService** übergeben (ersetzen Sie erneut *AzureCertificate* durch den Namen Ihres Zertifikats).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Im vorstehenden Beispiel ist `sms` ein **ServiceManagementService** -Objekt. Die **ServiceManagementService** -Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten.

## <a name="ListAvailableLocations"> </a>Auflisten der verfügbaren Standorte
Um die für das Hosten von Diensten verfügbaren Standorte aufzulisten, verwenden Sie die **list\_locations**-Methode.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Wenn Sie einen Clouddienst oder einen Speicherdienst erstellen, müssen Sie einen gültigen Standort angeben. Die Methode **list\_locations** gibt stets eine aktuelle Liste der derzeit verfügbaren Standorte zurück. Zum Zeitpunkt der Erstellung dieses Dokuments waren folgende Standorte verfügbar:

* Europa, Westen
* Nordeuropa
* Asien, Südosten
* Asien, Osten
* USA (Mitte)
* USA Nord Mitte
* USA Süd Mitte
* USA (Westen)
* USA (Osten)
* Japan (Osten)
* Japan, Westen
* Brasilien (Süden)
* Australien (Osten)
* Australien, Südosten

## <a name="CreateCloudService"> </a>Erstellen eines Clouddiensts
Wenn Sie eine Anwendung erstellen und in Azure ausführen, werden der Code und die Konfiguration gemeinsam als Azure-[Clouddienst][cloud service] bezeichnet. (In älteren Azure-Versionen wurde die Bezeichnung *gehosteter Dienst* verwendet.) Sie können die **create\_hosted\_service**-Methode zum Erstellen eines neuen gehosteten Diensts verwenden. Sie erstellen den Dienst, indem Sie einen Namen für den gehosteten Dienst (der in Azure eindeutig sein muss), eine Bezeichnung (automatisch Base64-codiert), eine Beschreibung und einen Standort angeben.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Mit der **list\_hosted\_services**-Methode können Sie alle gehosteten Dienste für Ihr Abonnement auflisten.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Wenn Sie Informationen zu einem bestimmten gehosteten Dienst abrufen möchten, übergeben Sie den Namen des gehosteten Diensts an die **get\_hosted\_service\_properties**-Methode.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Nachdem Sie einen Clouddienst erstellt haben, können Sie Ihren Code mit der **create\_deployment**-Methode für den Dienst bereitstellen.

## <a name="DeleteCloudService"> </a>Löschen eines Clouddiensts
Sie können einen Clouddienst löschen, indem Sie den Dienstnamen an die **delete\_hosted\_service**-Methode übergeben.

    sms.delete_hosted_service('myhostedservice')

Bevor Sie einen Dienst löschen können, müssen zunächst alle Bereitstellungen für den Dienst gelöscht werden. Weitere Informationen finden Sie unter [Löschen einer Bereitstellung](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Löschen einer Bereitstellung
Verwenden Sie zum Löschen einer Bereitstellung die Methode **delete\_deployment**. Im folgenden Beispiel wird eine Bereitstellung namens `v1` gelöscht:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Erstellen eines Speicherdiensts
Ein [Speicherdienst](../storage/common/storage-create-storage-account.md) gewährt Ihnen Zugriff auf Azure-[Blobs](../storage/blobs/storage-python-how-to-use-blob-storage.md), -[Tabellen](../cosmos-db/table-storage-how-to-use-python.md) und -[Warteschlangen](../storage/queues/storage-python-how-to-use-queue-storage.md). Um einen Speicherdienst zu erstellen, benötigen Sie einen Namen für den Dienst (zwischen 3 und 24 Kleinbuchstaben, muss innerhalb von Azure eindeutig sein). Sie benötigen außerdem eine Beschreibung, eine Bezeichnung (bis zu 100 Zeichen; wird automatisch Base64-codiert) und einen Standort. Im folgenden Beispiel wird ein Speicherdienst durch Angabe eines Standorts erstellt:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Im vorstehenden Beispiel kann der Status des Vorgangs **create\_storage\_account** abgerufen werden, indem das von **create\_storage\_account** zurückgegebene Ergebnis an die **get\_operation\_status**-Methode übergeben wird. 

Mit der **list\_storage\_accounts**-Methode können Sie Ihre Speicherkonten und deren Eigenschaften auflisten.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Löschen eines Speicherdiensts
Sie löschen einen Speicherdienst, indem Sie den Speicherdienstnamen an die **delete\_storage\_account**-Methode übergeben. Beim Löschen eines Speicherdiensts werden alle im Dienst gespeicherten Daten gelöscht (Blobs, Tabellen und Warteschlangen).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Auflisten der verfügbaren Betriebssysteme
Mit der **list\_operating\_systems**-Methode können Sie die für das Hosten von Diensten verfügbaren Betriebssysteme auflisten.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternativ können Sie die **list\_operating\_system\_families**-Methode verwenden, die die Betriebssysteme nach Familie gruppiert.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Erstellen eines Betriebssystemimages
Wenn Sie dem Imagerepository ein Betriebssystemimage hinzufügen möchten, verwenden Sie die **add\_os\_image**-Methode.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Mit der Methode **list\_os\_images** können Sie die verfügbaren Betriebssystemimages auflisten. Die Liste umfasst alle Plattformimages und Benutzerimages.

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Löschen eines Betriebssystemimages
Verwenden Sie zum Löschen eines Betriebssystemimages die **delete\_os\_image**-Methode.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Erstellen eines virtuellen Computers
Zum Erstellen eines virtuellen Computers müssen Sie zunächst einen [Clouddienst](#CreateCloudService)erstellen. Anschließend erstellen Sie die Bereitstellung des virtuellen Computers mit der **create\_virtual\_machine\_deployment**-Methode.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Löschen eines virtuellen Computers
Zum Löschen eines virtuellen Computers löschen Sie zunächst die Bereitstellung mit der **delete\_deployment**-Methode.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Anschließend kann der Clouddienst mit der **delete\_hosted\_service**-Methode gelöscht werden.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Erstellen eines virtuellen Computers aus einem erfassten Image eines virtuellen Computers
Um ein VM-Image zu erfassen, rufen Sie zuerst die **capture\_vm\_image**-Methode auf.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Um zu überprüfen, ob Sie das Image erfolgreich erfasst haben, verwenden Sie die **list\_vm\_images**-API. Stellen Sie sicher, dass Ihr Image in den Ergebnissen angezeigt wird.

    images = sms.list_vm_images()

Um schließlich den virtuellen Computer mithilfe des erfassten Image zu erstellen, verwenden Sie die **create\_virtual\_machine\_deployment**-Methode wie zuvor, übergeben diesmal aber „vm_image_name“.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Weitere Informationen zum Erfassen eines virtuellen Linux-Computers im klassischen Bereitstellungsmodell finden Sie unter [Erfassen eines virtuellen Linux-Computers](../virtual-machines/linux/classic/capture-image-classic.md).

Weitere Informationen zum Erfassen eines virtuellen Windows-Computers im klassischen Bereitstellungsmodell finden Sie unter [Erfassen eines virtuellen Windows-Computers](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"> </a>Nächste Schritte
Da Sie nun mit den Grundlagen der Dienstverwaltung vertraut sind, können Sie auf die [vollständige API-Referenzdokumentation für das Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) zugreifen und komplexe Aufgaben mühelos durchführen, um Ihre Python-Anwendung zu verwalten.

Weitere Informationen finden Sie im [Python Developer Center](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
