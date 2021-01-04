---
title: Erstellen einer ASE v1
description: Beschreibung der Schritte zum Erstellen einer App Service-Umgebung v1. Dieses Dokument wird nur für Kunden bereitgestellt, die die ASE-Legacyumgebung v1 verwenden.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 543050bc899c257c4ad5e0d0c399a1de6f0f58f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005203"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Erstellen einer App Service-Umgebung v1 

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v1 behandelt. Für die App Service-Umgebung steht eine neuere Version zur Verfügung. Diese ist benutzerfreundlicher und basiert auf einer leistungsfähigeren Infrastruktur. Weitere Informationen zu dieser neuen Version finden Sie unter [Einführung in die App Service-Umgebung](intro.md).
> 

### <a name="overview"></a>Übersicht
Die App Service-Umgebung (ASE) ist eine Premium-Dienstoption von Azure App Service mit erweiterter Konfigurationsmöglichkeit, die in den mehrinstanzenfähigen Stamps nicht verfügbar ist. Mit dem ASE-Feature wird im Wesentlichen Azure App Service im virtuellen Netzwerk eines Kunden bereitgestellt. Um ein besseres Verständnis der Möglichkeiten von App Service-Umgebungen zu erhalten, lesen Sie die Dokumentation [Was ist eine App Service-Umgebung?][WhatisASE].

### <a name="before-you-create-your-ase"></a>Bevor Sie Ihre ASE erstellen
Es ist wichtig, die Aspekte zu berücksichtigen, die Sie ändern können. Die folgenden Aspekte Ihrer ASE können Sie nicht ändern, nachdem sie erstellt wurde:

* Standort
* Subscription
* Ressourcengruppe
* Verwendetes VNET
* Verwendetes Subnetz 
* Subnetzgröße

Stellen Sie bei der Auswahl eines VNET und Angabe eines Subnetzes sicher, dass es auf künftiges Wachstum ausgelegt ist. 

### <a name="creating-an-app-service-environment-v1"></a>Erstellen einer App Service-Umgebung v1
Um eine App Service-Umgebung v1 zu erstellen, können Sie den Azure Marketplace nach ***App Service-Umgebung v1** _ durchsuchen oder _ *Ressource erstellen** -> **Web + Mobil** -> **App Service-Umgebung** verwenden. So erstellen Sie eine ASEv1

1. Geben Sie den Namen Ihrer ASE an. Der für die ASE angegebene Name wird für die in der ASE erstellten Apps verwendet. Wenn der Name der App Service-Umgebung „appsvcenvdemo“ lautet, erhält die Unterdomäne den Namen *appsvcenvdemo.p.azurewebsites.net*. Wenn Sie also eine App namens *mytestapp* erstellt haben, kann diese über *mytestapp.appsvcenvdemo.p.azurewebsites.net* aufgerufen werden. Sie dürfen im Namen Ihrer ASE keine Leerzeichen verwenden. Bei Verwendung von Großbuchstaben im Namen wird der Domänenname dennoch vollständig in Kleinbuchstaben geschrieben. Bei Verwendung eines ILB wird Ihr ASE-Name nicht in Ihrer Unterdomäne verwendet, sondern stattdessen explizit während der ASE-Erstellung angegeben.
   
    ![Screenshot mit dem Erstellen einer App Service-Umgebung (ASE)][1]
2. Wählen Sie Ihr Abonnement aus. Das für Ihre ASE verwendete Abonnement gilt auch für alle Apps, die Sie in dieser ASE erstellen. Sie können Ihre ASE nicht in einem VNET platzieren, das einem anderen Abonnement angehört.
3. Wählen eine neue Ressourcengruppe aus, oder geben Sie eine an. Die Ressourcengruppe, die für Ihre ASE verwendet wird, muss identisch mit derjenigen sein, die für das VNET verwendet wird. Wenn Sie ein bereits vorhandenes VNET auswählen, wird die Auswahl der Ressourcengruppe für Ihre ASE gemäß VNET aktualisiert.
   
    ![Screenshot mit der Auswahl oder der Änderung einer neuen Ressourcengruppe][2]
4. Treffen Sie Ihre Auswahl für „Virtuelles Netzwerk“ und „Speicherort“. Sie können ein neues VNET erstellen oder ein bereits vorhandenes VNET auswählen. Wenn Sie ein neues VNET auswählen, können Sie einen Namen und Speicherort angeben. Das neue VNET hat den Adressbereich 192.168.250.0/23 und ein Subnetz namens **default**, das als 192.168.250.0/24 definiert ist. Sie können auch einfach ein bereits vorhandenes klassisches oder Resource Manager-VNET auswählen. Die Auswahl des VIP-Typs bestimmt, ob auf Ihre ASE ein direkter Zugriff aus dem Internet möglich ist (extern) oder ob ein interner Load Balancer (ILB) verwendet wird. Mehr hierzu erfahren Sie unter [Verwenden eines internen Lastenausgleichs mit einer App Service-Umgebung][ILBASE]. Wenn Sie einen VIP-Adresstyp des Typs „Extern“ auswählen, können Sie auswählen, mit wie vielen externen IP-Adressen das System für IP-SSL-Zwecke erstellt wird. Bei Auswahl von „Intern“ müssen Sie die Unterdomäne angeben, die Ihrer ASE verwenden soll. ASEs können in virtuellen Netzwerken bereitgestellt werden, die *entweder* öffentliche Adressbereiche *oder* RFC1918-Adressräume (d.h. private Adressen) verwenden. Um ein virtuelles Netzwerk mit einem öffentlichen Adressbereich zu verwenden, müssen Sie das VNET vorab erstellen. Bei Auswahl eines bereits bestehenden VNET müssen Sie während der Erstellung der ASE ein neues Subnetz erstellen. **Eine vorab erstelltes Subnetz kann nicht im Portal verwendet werden. Sie können eine ASE mit einem bereits vorhandenen Subnetz erstellen, wenn Sie Ihre ASE mithilfe einer Resource Manager-Vorlage erstellen.** Zum Erstellen einer App anhand einer Vorlage befolgen Sie die Angaben unter [Erstellen einer App Service-Umgebung aus einer Vorlage][ILBAseTemplate] und [Erstellen einer ILB-ASE mit Azure Resource Manager-Vorlagen][ASEfromTemplate].

### <a name="details"></a>Details
Eine ASE wird mit zwei Front-Ends und zwei Workern erstellt. Die Front-Ends agieren als HTTP/HTTPS-Endpunkte und senden Datenverkehr an die Workers, d.h. die Rollen, die Ihre Apps hosten. Sie können die Menge nach der Erstellung der ASE anpassen und sogar automatische Skalierungsregeln für diese Ressourcenpools einrichten. Weitere Informationen zur manuellen Skalierung, Verwaltung und Überwachung einer App Service-Umgebung finden Sie hier: [Konfigurieren einer App Service-Umgebung][ASEConfig] 

Nur die eine ASE darf im Subnetz vorhanden sein, das von der ASE verwendet wird. Das Subnetz kann für nichts anderes als die ASE verwendet werden.

### <a name="after-app-service-environment-v1-creation"></a>Nach dem Erstellen einer App Service-Umgebung v1
Nach der Erstellung einer App Service-Umgebung können Sie die folgenden Einstellungen anpassen:

* Anzahl von Front-Ends (Minimum: 2)
* Anzahl von Workern (Minimum: 2)
* Menge der für IP-SSL verfügbaren IP-Adressen
* Größen der Compute-Ressourcen, die von den Front-Ends oder den Workern verwendet werden (Mindestgröße für Front-Ends ist P2)

Weitere Informationen zur manuellen Skalierung, Verwaltung und Überwachung von App Service-Umgebungen finden Sie hier: [Konfigurieren einer App Service-Umgebung][ASEConfig] 

Informationen zur automatischen Skalierung finden Sie in der folgenden Anleitung: [Konfigurieren der Autoskalierung für eine App Service-Umgebung][ASEAutoscale]

Es gibt zusätzliche Abhängigkeiten, die nicht zur Anpassung zur Verfügung stehen, wie beispielsweise Datenbank und Speicher. Diese werden von Azure verwaltet und sind im System enthalten. Der Systemspeicher unterstützt bis zu 500 GB für die gesamte App Service-Umgebung, und die Datenbank wird von Azure nach Bedarf an die Staffelung des Systems angepasst.

## <a name="getting-started"></a>Erste Schritte
Informationen zum Einstieg in App Service-Umgebungen v1 finden Sie unter [Einführung in die App Service-Umgebung v1][WhatisASE].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
