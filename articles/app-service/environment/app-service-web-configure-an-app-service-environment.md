---
title: Konfigurieren einer App Service-Umgebung v1 – Azure
description: Konfiguration, Verwaltung und Überwachung der App Service-Umgebung v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c0b4117f6e7b48dce1746ad6eb3dbe29c0d16af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130618"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurieren einer App Service-Umgebung v1

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v1 behandelt.  Für die App Service-Umgebung steht eine neuere Version zur Verfügung. Diese ist benutzerfreundlicher und basiert auf einer leistungsfähigeren Infrastruktur. Weitere Informationen zu dieser neuen Version finden Sie unter [Einführung in die App Service-Umgebung](intro.md).
> 

## <a name="overview"></a>Übersicht
Eine Azure App Service-Umgebung (ASE) besteht aus mehreren Hauptkomponenten:

* Computeressourcen, die unter dem gehosteten Dienst der App Service-Umgebung ausgeführt werden
* Storage
* Einer Datenbank
* Einem Azure Virtual Network (VNet) vom Typ „Klassisch“ (V1) oder „Resource Manager“ (V2) 
* Einem Subnetz mit darin ausgeführtem gehosteten Dienst der App Service-Umgebung

### <a name="compute-resources"></a>Computeressourcen
Sie verwenden die Computeressourcen für Ihre vier Ressourcenpools.  Jede App Service-Umgebung verfügt über eine Gruppe von Front-Ends und drei mögliche Workerpools. Sie müssen nicht alle drei Workerpools verwenden und können auch nur einen oder zwei nutzen.

Die Hosts in den Ressourcenpools (Front-Ends und Worker) sind für die Mandanten nicht direkt zugänglich. Sie können nicht das Remotedesktopprotokoll (RDP) verwenden, um eine Verbindung damit herzustellen, die Bereitstellung zu ändern oder als Administrator dafür zu fungieren.

Sie können die Menge und Größe für den Ressourcenpool festlegen. In einer ASE verfügen Sie über vier Größenoptionen (P1 bis P4). Ausführliche Informationen zu diesen Größen und den dazugehörigen Preisen finden Sie unter [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/).
Das Ändern der Menge oder Größe wird als Skalierungsvorgang bezeichnet.  Es kann jeweils immer nur ein Skalierungsvorgang ausgeführt werden.

**Front-Ends**: Die Front-Ends sind die HTTP/HTTPS-Endpunkte für die Apps in Ihrer App Service-Umgebung. Auf den Front-Ends führen Sie keine Workloads aus.

* Eine ASE verfügt anfänglich über zwei P2-Einheiten. Diese Größe ist für Entwicklungs-/Testworkloads und einfache Produktionsworkloads ausreichend. P3-Einheiten werden dringend für mittlere bis große Produktionsworkloads empfohlen.
* Für mittlere bis große Produktionsworkloads werden mindestens vier P3-Einheiten empfohlen, um sicherzustellen, dass bei einer geplanten Wartung genügend Front-Ends ausgeführt werden. Bei geplanten Wartungsaktivitäten wird jeweils ein Front-End heruntergefahren. Die insgesamt verfügbare Front-End-Kapazität wird durch Wartungsaktivitäten also verringert.
* Die Bereitstellung von Front-Ends kann bis zu einer Stunde dauern. 
* Um eine noch genauere Skalierung zu erzielen, sollten Sie den CPU-Prozentsatz, den Arbeitsspeicher-Prozentsatz und Metriken zu aktiven Anforderungen für den Front-End-Pool überwachen. Wenn die CPU- oder Arbeitsspeicher-Prozentsätze beim Ausführen von P3-Einheiten über einem Wert von 70 liegen, sollten Sie mehr Front-Ends hinzufügen. Wenn der Wert für die aktiven Anforderungen durchschnittlich bei 15.000 bis 20.000 Anforderungen pro Front-End liegt, sollten Sie ebenfalls weitere Front-Ends hinzufügen. Bei der Ausführung von P3-Einheiten ist das Ziel also, für CPU und Arbeitsspeicher einen Prozentsatz von weniger als 70% und für aktive Anforderungen im Durchschnitt eine Anzahl von weniger als 15.000 Anforderungen pro Front-End zu erreichen.  

**Worker**: In den Workern werden Ihre Apps tatsächlich ausgeführt. Wenn Sie Ihre App Service-Pläne zentral hochskalieren, werden für diesen Vorgang Worker im zugeordneten Workerpool genutzt.

* Es ist nicht möglich, umgehend Worker hinzuzufügen. Die Bereitstellung kann bis zu einer Stunde dauern.
* Die Skalierung der Größe einer Computeressource für einen Pool dauert weniger als eine Stunde pro Updatedomäne. Eine App Service-Umgebung verfügt über 20 Updatedomänen. Wenn Sie die Computegröße eines Workerpools mit zehn Instanzen skalieren, kann der Vorgang also bis zu zehn Stunden dauern.
* Wenn Sie die Größe der Computeressourcen in einem Workerpool ändern, verursacht dies möglicherweise einen Kaltstart der Apps, die in diesem Workerpool ausgeführt werden.

Die Größe von Computeressourcen eines Workerpools, in dem keine Apps ausgeführt werden, lässt sich wie folgt am schnellsten ändern:

* Skalieren Sie die Workermenge zentral auf „2“ herunter.  „2“ ist die Mindestgröße für zentrales Herunterskalieren im Portal. Das Aufheben der Zuordnung Ihrer Instanzen dauert einige Minuten. 
* Wählen Sie die neue Computegröße und die Anzahl von Instanzen. Anschließend dauert es noch bis zu zwei Stunden, bis der Vorgang abgeschlossen ist.

Wenn für Ihre Apps mehr Computeressourcen erforderlich sind, sind die vorstehenden Schritte keine geeignete Lösung. Anstatt die Größe des Workerpools zu ändern, in dem die Apps gehostet werden, können Sie einen anderen Workerpool mit Workern der gewünschten Größe füllen und Ihre Apps in diesen Pool verschieben.

* Erstellen Sie die zusätzlichen Instanzen der erforderlichen Computegröße in einem anderen Workerpool. Dies dauert bis zu einer Stunde.
* Weisen Sie Ihre App Service-Pläne, über die die Apps mit den höheren Ressourcenanforderungen gehostet werden, dem neu konfigurierten Workerpool zu. Dies ist ein schneller Vorgang, der in weniger als einer Minute abgeschlossen sein sollte.  
* Skalieren Sie den ersten Workerpool zentral herunter, wenn Sie die nicht genutzten Instanzen nicht mehr benötigen. Dieser Vorgang dauert einige Minuten.

**Automatische Skalierung**: Ein Tool, das Sie beim Verwalten Ihres Computerressourcenverbrauchs unterstützen kann, ist die automatische Skalierung. Sie können die automatische Skalierung für Front-End- oder Workerpools nutzen. Es ist beispielsweise möglich, die Instanzen eines beliebigen Pooltyps am Morgen zu erhöhen und am Abend wieder zu reduzieren. Sie können auch Instanzen hinzufügen, wenn die Anzahl von Workern, die in einem Workerpool verfügbar sind, unter einen bestimmten Schwellenwert fällt.

Wenn Sie für Metriken eines Computeressourcenpools Regeln für eine automatische Skalierung festlegen möchten, sollten Sie den erforderlichen Zeitaufwand für die Bereitstellung beachten. Weitere Einzelheiten zur automatischen Skalierung von App Service-Umgebungen finden Sie unter [Automatische Skalierung und App Service-Umgebungen][ASEAutoscale].

### <a name="storage"></a>Storage
Jede App Service-Umgebung ist mit 500 GB Speicherplatz konfiguriert. Dieser Speicherplatz wird von allen Apps in der App Service-Umgebung verwendet. Er ist Teil der App Service-Umgebung, und derzeit ist eine Umstellung auf Ihren Speicherplatz nicht möglich. Wenn Sie Anpassungen am Routing oder an der Sicherheit Ihres virtuellen Netzwerks vornehmen, muss sichergestellt sein, dass weiterhin auf Azure Storage zugegriffen werden kann. Andernfalls ist die App Service-Umgebung nicht funktionsfähig.

### <a name="database"></a>Datenbank
Die Datenbank enthält Informationen, die die Umgebung definieren, und außerdem Details zu den darin ausgeführten Apps. Dies ist ein Teil des Azure-Abonnements. Es besteht keine Möglichkeit, diesen Teil direkt zu ändern. Wenn Sie Anpassungen am Routing oder an der Sicherheit Ihres virtuellen Netzwerks vornehmen, muss sichergestellt sein, dass weiterhin auf SQL Azure zugegriffen werden kann. Andernfalls ist die App Service-Umgebung nicht funktionsfähig.

### <a name="network"></a>Netzwerk
Beim VNet, das mit Ihrer App Service-Umgebung verwendet wird, kann es sich um ein Netzwerk handeln, das Sie beim Erstellen der App Service-Umgebung eingerichtet haben, oder um ein bereits vorab erstelltes Netzwerk. Wenn Sie das Subnetz während der Erstellung der App Service-Umgebung erstellen, wird erzwungen, dass sich die App Service-Umgebung in derselben Ressourcengruppe wie das virtuelle Netzwerk befindet. Wenn sich die von Ihrer App Service-Umgebung verwendete Ressourcengruppe von der Ihres VNET unterscheiden soll, müssen Sie Ihre App Service-Umgebung mithilfe einer Resource Manager-Vorlage erstellen.

Für das virtuelle Netzwerk, das für eine App Service-Umgebung verwendet wird, gelten einige Einschränkungen:

* Das virtuelle Netzwerk muss ein regionales virtuelles Netzwerk sein.
* Es muss ein Subnetz mit mindestens acht Adressen vorhanden sein, in dem die ASE bereitgestellt wird.
* Wenn ein Subnetz zum Hosten einer App Service-Umgebung verwendet wird, kann der Adressbereich des Subnetzes nicht mehr geändert werden. Um zukünftigem Wachstum der ASE gerecht werden zu können, sollte das Subnetz daher mindestens 64 Adressen umfassen.
* Im Subnetz kann sich ausschließlich die ASE befinden.

Anders als beim gehosteten Dienst, der die App Service-Umgebung umfasst, werden das [virtuelle Netzwerk][virtualnetwork] und das Subnetz vom Benutzer gesteuert.  Sie können das virtuelle Netzwerk über die Benutzeroberfläche des virtuellen Netzwerks oder PowerShell verwalten.  Eine ASE kann in einem klassischen VNet oder einem Resource Manager-VNet bereitgestellt werden.  Die Benutzeroberflächen des Portals und der API unterscheiden sich bei klassischen VNets und Resource Manager-VNets etwas, aber die ASE-Oberfläche ist identisch.

Für das VNet, das zum Hosten einer ASE genutzt wird, können entweder private RFC1918-IP-Adressen oder öffentliche IP-Adressen verwendet werden.  Wenn Sie einen IP-Bereich verwenden möchten, der nicht durch RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) abgedeckt ist, müssen Sie Ihr VNet und Subnetz für die ASE vor der Erstellung der ASE erstellen.

Da diese Funktion den Azure App Service in Ihr virtuelles Netzwerk versetzt, können die in Ihrer App Service-Umgebung gehosteten Apps jetzt direkt auf Ressourcen zugreifen, die über ExpressRoute oder Site-to-Site-VPNs (Virtual Private Networks) zur Verfügung gestellt werden. Die Apps in Ihrer App Service-Umgebung benötigen keine zusätzlichen Netzwerkfunktionen für den Zugriff auf Ressourcen, die für das virtuelle Netzwerk verfügbar sind, in dem Ihre App Service-Umgebung gehostet wird. Dies bedeutet, dass Sie keine VNET-Integration oder Hybridverbindungen verwenden müssen, um Ressourcen in das virtuelle Netzwerk zu verlagern oder damit zu verbinden. Sie können jedoch nach wie vor beide Funktionen nutzen, um auf Ressourcen in Netzwerken zuzugreifen, die nicht mit dem virtuellen Netzwerk verbunden sind.

Zum Beispiel können Sie mithilfe der VNET-Integration ein virtuelles Netzwerk integrieren, das Teil Ihres Abonnements, aber nicht mit dem virtuellen Netzwerk verbunden ist, in dem sich Ihre App Service-Umgebung befindet. Sie können auch weiterhin Hybridverbindungen verwenden, um ganz normal auf Ressourcen in anderen Netzwerken zuzugreifen.  

Falls Ihr virtuelles Netzwerk mit einem ExpressRoute-VPN konfiguriert ist, müssen Sie mit einigen Routinganforderungen einer App Service-Umgebung vertraut sein. Es gibt einige Konfigurationen für benutzerdefiniertes Routing (UDR, User-Defined Routing), die nicht mit einer App Service-Umgebung kompatibel sind. Weitere Informationen zur Ausführung einer ASE in einem virtuellen Netzwerk mit ExpressRoute finden Sie unter [Details zur Netzwerkkonfiguration für App Service-Umgebungen mit ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Sichern des eingehenden Datenverkehrs
Es gibt zwei Hauptmethoden, um eingehenden Datenverkehr für Ihre ASE zu steuern.  Sie können Netzwerksicherheitsgruppen (NSGs) verwenden, um zu steuern, welche IP-Adressen auf Ihre ASE zugreifen können. Dies ist unter [Steuern des eingehenden Datenverkehrs in einer App Service-Umgebung](app-service-app-service-environment-control-inbound-traffic.md) beschrieben. Außerdem können Sie die ASE mit einem internen Load Balancer (ILB) konfigurieren.  Sie können diese Funktionen auch zusammen nutzen, wenn Sie den Zugriff auf die ILB-ASE mit NSGs einschränken möchten.

Wenn Sie eine ASE erstellen, wird im virtuellen Netzwerk eine VIP erstellt.  Es gibt zwei VIP-Typen: extern und intern.  Wenn Sie eine ASE mit einer externen VIP erstellen, kann auf Ihre Apps in der ASE mit einer über das Internet routbaren IP-Adresse zugegriffen werden. Bei Auswahl der internen Option wird die ASE mit einem ILB konfiguriert und ist nicht direkt über das Internet zugänglich.  Für eine ILB-ASE ist trotzdem eine externe VIP erforderlich, aber sie wird nur für den Zugriff zum Durchführen der Azure-Verwaltung und -Wartung verwendet.  

Während der ILB-ASE-Erstellung geben Sie die von der ILB-ASE verwendete Unterdomäne an und müssen Ihr eigenes DNS für die angegebene Unterdomäne verwalten.  Da Sie den Namen der Unterdomäne festlegen, müssen Sie auch das für den HTTPS-Zugriff verwendete Zertifikat verwalten.  Nach der ASE-Erstellung werden Sie aufgefordert, das Zertifikat anzugeben.  Weitere Informationen zur Erstellung und Verwendung einer ILB-ASE finden Sie unter [Verwenden einen internen Lastenausgleichs mit einer App Service-Umgebung][ILBASE]. 

## <a name="portal"></a>Portal
Sie können die App Service-Umgebung mit der Benutzeroberfläche im Azure-Portal verwalten und überwachen. Wenn für Sie eine App Service-Umgebung vorhanden ist, sehen Sie normalerweise in der Randleiste das App Service-Symbol. Im Azure-Portal werden App Service-Umgebungen mit folgendem Symbol dargestellt:

![Symbol „App Service-Umgebung“][1]

Um die Benutzeroberfläche zu öffnen, in der Ihre App Service-Umgebungen aufgelistet sind, können Sie das Symbol verwenden oder das Chevron („>“) unten in der Randleiste wählen, um die App Service-Umgebungen auszuwählen. Indem Sie eine der aufgeführten ASEs auswählen, wird die Benutzeroberfläche für die Überwachung und Verwaltung geöffnet.

![Benutzeroberfläche für Überwachung und Verwaltung der App Service-Umgebung][2]

Das erste Blatt zeigt einige Eigenschaften der App Service-Umgebung zusammen mit einem Metrikdiagramm pro Ressourcenpool. Einige Eigenschaften im Block **Zusammenfassung** sind auch Links, mit denen Sie das entsprechende Blatt öffnen können. Sie können z.B. auf den Namen des **virtuellen Netzwerks** klicken und dadurch die Benutzeroberfläche für das virtuelle Netzwerk öffnen, in dem Ihre App Service-Umgebung ausgeführt wird. Mit **App Service-Pläne** und **Apps** öffnen Sie jeweils Blätter mit Auflistungen dieser in Ihrer App Service-Umgebung enthaltenen Elemente.  

### <a name="monitoring"></a>Überwachung
Mit den Diagrammen können Sie eine Reihe von Leistungsmetriken im jeweiligen Ressourcenpool verfolgen. Für den Front-End-Pool können Sie die durchschnittliche CPU- und Speicherauslastung überwachen. Für Workerpools können Sie die verwendete Menge und die verfügbare Menge überwachen.

Die Worker in einem Workerpool können von mehreren App Service-Plänen genutzt werden. Die Workload wird nicht auf dieselbe Weise verteilt wie bei Front-End-Servern. Daher liefern die CPU- und die Arbeitsspeicherauslastung keine vergleichbaren nützlichen Informationen. Es ist wichtiger zu verfolgen, wie viele Worker verwendet wurden und wie viele verfügbar sind. Dies gilt besonders, wenn Sie das System für die Nutzung durch andere Personen verwalten.  

Sie können alle Metriken, die sich in den Diagrammen verfolgen lassen, auch zur Einrichtung von Warnungen verwenden. Beim Einrichten von Warnungen gehen Sie hier genauso vor wie sonst auch im App Service. Sie können eine Warnung einrichten, indem Sie den für **Warnungen** vorgesehenen Teil der Benutzeroberfläche verwenden oder indem Sie auf einer beliebigen Benutzeroberfläche für Metriken die Details öffnen und **Warnung hinzufügen** wählen.

![Benutzeroberfläche für Metriken][3]

Bei den gerade diskutierten Metriken handelt es sich um Metriken für App Service-Umgebungen. Darüber hinaus stehen auch auf der Ebene der App Service-Pläne Metriken zur Verfügung. Dort ist das Überwachen von CPU- und Speicherauslastung besonders sinnvoll.

In einer ASE sind alle App Service-Pläne dedizierte App Service-Pläne. Die einzigen Apps, die auf den zugeordneten Hosts des App Service-Plans ausgeführt werden, sind also die Apps in diesem App Service-Plan. Um die Details zu Ihrem App Service-Plan anzuzeigen, rufen Sie den App Service-Plan aus einer beliebigen Liste in der Benutzeroberfläche der App Service-Umgebung heraus auf oder nutzen die Funktion zum **Durchsuchen von App Service-Plänen** (Auflistung aller Pläne).   

### <a name="settings"></a>Einstellungen
Das Blatt „App Service-Umgebung“ enthält einen Abschnitt **Einstellungen** , auf dem Optionen zu verschiedenen wichtigen Funktionen festgelegt werden können:

**Einstellungen** > **Eigenschaften**: Das Blatt **Einstellungen** wird automatisch angezeigt, wenn Sie das Blatt „App Service-Umgebung“ öffnen. Im oberen Bereich finden Sie **Eigenschaften**. Einige Elemente sind identisch mit dem, was Sie unter **Zusammenfassung** sehen. Sehr nützlich sind jedoch die Optionen **Virtuelle IP-Adresse** und **Ausgehende IP-Adressen**.

![Blatt „Einstellungen“ und Eigenschaften][4]

**Einstellungen** > **IP-Addressen**: Wenn Sie eine IP-SSL-App (Secure Sockets Layer) in Ihrer ASE erstellen, benötigen Sie eine IP-SSL-Adresse. Um diese Adresse zu beschaffen, muss die App Service-Umgebung über eigene IP-SSL-Adressen verfügen, die zugeordnet werden können. Beim Erstellen einer App Service-Umgebung verfügt diese über eine IP-SSL-Adresse für diesen Zweck. Sie können jedoch weitere Adressen hinzufügen. Für zusätzliche IP-SSL-Adressen fallen Gebühren an. Diese sind im Abschnitt zu SSL-Verbindungen unter [App Service-Preise][AppServicePricing] aufgeführt. Der IP-SSL-Preis ist der zusätzliche Preis.

**Einstellungen** > **Front-End-Pool** / **Workerpools**: Auf jedem dieser Ressourcenpoolblätter werden nur die Informationen für den jeweiligen Ressourcenpool angezeigt. Außerdem enthalten die Blätter Steuerelemente, mit denen der Ressourcenpool skaliert werden kann.  

Das Grundlagenblatt für jeden Ressourcenpool enthält ein Diagramm mit den Metriken zu diesem Ressourcenpool. Ähnlich wie bei den Diagrammen im App Service-Umgebungsblatt können Sie auch hier das Diagramm öffnen und wie gewünscht Warnungen einrichten. Ob Sie eine Warnung im App Service-Umgebungsblatt für einen bestimmten Ressourcenpool einrichten oder dafür das Blatt für den Ressourcenpool verwenden, spielt keine Rolle – das Ergebnis ist dasselbe. Im Blatt **Einstellungen** für den Workerpool haben Sie Zugriff auf alle Apps oder App Service-Pläne, die in diesem Workerpool ausgeführt werden.

![Benutzeroberfläche mit Einstellungen für Workerpool][5]

### <a name="portal-scale-capabilities"></a>Skalierungsfunktionen im Portal
Es gibt drei Skalierungsvorgänge:

* Ändern der Anzahl von IP-Adressen in der App Service-Umgebung, die für die IP-SSL-Nutzung zur Verfügung stehen
* Ändern der Größe der Computeressource, die in einem Ressourcenpool verwendet wird
* Ändern der Anzahl von Computeressourcen in einem Ressourcenpool, entweder manuell oder durch automatische Skalierung

Im Portal gibt es drei Möglichkeiten, mit denen Sie steuern können, wie viele Server sich in Ihren Ressourcenpools befinden:

* Mit dem Skalierungsvorgang oben auf dem Hauptblatt für die App Service-Umgebung. Sie können für die Front-End- und Workerpools mehrere Änderungen der Skalierungskonfiguration vornehmen. Sie werden als Einzelvorgang angewendet.
* Mit einem manuellem Skalierungsvorgang unter **Einstellungen** auf dem Blatt **Skalieren** für den jeweiligen Ressourcenpool.
* Mit der automatischen Skalierung, die Sie vom Blatt **Skalieren** für den jeweiligen Ressourcenpool einrichten.

Um den Skalierungsvorgang auf dem Blatt „App Service-Umgebung“ zu verwenden, ziehen Sie den betreffenden Schieberegler auf die gewünschte Menge und speichern die neue Einstellung. Mit dieser Benutzeroberfläche können Sie auch die Größe ändern.  

![Benutzeroberfläche „Skalieren“][6]

Wenn Sie die Funktionen für die manuelle oder die automatische Skalierung in einem bestimmten Ressourcenpool verwenden möchten, wechseln Sie zu **Einstellungen** > **Front-End-Pool** /  **Workerpools**. Öffnen Sie anschließend den Pool, den Sie ändern möchten. Wählen Sie **Einstellungen** > **Horizontal hochskalieren** bzw. **Einstellungen** > **Zentral hochskalieren**. Auf dem Blatt **Horizontal hochskalieren** können Sie die Anzahl von Instanzen steuern. **Zentral hochskalieren** ermöglicht die Festlegung der Ressourcengröße.  

![Benutzeroberfläche „Skalierungseinstellungen“][7]

## <a name="fault-tolerance-considerations"></a>Aspekte der Fehlertoleranz
Sie können eine App Service-Umgebung für die Verwendung von bis zu 55 Computeressourcen konfigurieren. Von diesen 55 Computeressourcen können nur 50 zum Hosten von Workloads eingesetzt werden. Dies hat zwei Gründe. Es sind mindestens zwei Front-End-Computeressourcen erforderlich.  So bleiben bis zu 53 zur Unterstützung der Workerpoolzuweisung übrig. Um Fehlertoleranz bereitzustellen, muss nach den folgenden Regeln eine zusätzliche Computeressource zugewiesen werden:

* Jeder Workerpool benötigt mindestens eine weitere Computeressource, der keine Workload zugewiesen werden kann.
* Wenn die Menge von Computeressourcen in einem Workerpool einen bestimmten Wert überschreitet, ist aus Gründen der Fehlertoleranz eine weitere Computeressource erforderlich. Im Front-End-Pool ist dies nicht der Fall.

Im Rahmen der Fehlertoleranzanforderungen gilt innerhalb eines einzelnen Workerpools Folgendes für einen bestimmten Wert von X Ressourcen, die einem Workerpool zugewiesen sind:

* Wenn X zwischen 2 und 20 liegt, beträgt die Anzahl von Computeressourcen, die für Workloads verwendet werden können, X-1.
* Wenn X zwischen 21 und 40 liegt, beträgt die Anzahl von Computeressourcen, die für Workloads verwendet werden können, X-2.
* Wenn X zwischen 41 und 53 liegt, beträgt die Anzahl von Computeressourcen, die für Workloads verwendet werden können, X-3.

Es sind mindestens zwei Front-End-Server und zwei Worker erforderlich.  Die obigen Aussagen lassen sich durch die folgenden Beispiele gut veranschaulichen:  

* Wenn sich 30 Worker in einem einzelnen Pool befinden, können 28 für Workloads verwendet werden.
* Wenn sich zwei Worker in einem einzelnen Pool befinden, kann einer für Workloads verwendet werden.
* Wenn sich 20 Worker in einem einzelnen Pool befinden, können 19 für Workloads verwendet werden.  
* Wenn sich 21 Worker in einem einzelnen Pool befinden, können trotzdem nur 19 für Workloads verwendet werden.  

Der Aspekt der Fehlertoleranz ist wichtig, und Sie müssen ihn im Auge behalten, wenn Sie bei der Skalierung bestimmte Schwellenwerte überschreiten. Wenn Sie ausgehend von 20 Instanzen mehr Kapazität hinzufügen möchten, sollten Sie 22 oder mehr Instanzen wählen, weil bei 21 die Kapazität nicht steigt. Dasselbe trifft zu, wenn Sie 40 Instanzen überschreiten. Die nächste Anzahl, bei der die Kapazität steigt, ist 42.  

## <a name="deleting-an-app-service-environment"></a>Löschen einer App Service-Umgebung
Wenn Sie eine App Service-Umgebung löschen möchten, verwenden Sie einfach die Aktion **Löschen** im oberen Bereich des Blatts „App Service-Umgebung“. Wenn Sie dies tun, werden Sie aufgefordert, den Namen Ihrer App Service-Umgebung einzugeben. Auf diese Weise bestätigen Sie, dass Sie diesen Schritt wirklich ausführen möchten. Beachten Sie Folgendes: Beim Löschen einer App Service-Umgebung wird auch ihr gesamter Inhalt gelöscht.  

![Benutzeroberfläche „App Service-Umgebung löschen“][9]  

## <a name="getting-started"></a>Erste Schritte
Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Erstellen einer App Service-Umgebung](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
