---
title: Übersicht über die App Service-Umgebung
description: Übersicht über die Azure App Service-Umgebung
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fbc498fcd654d16936c2548528e2600be68a2ad9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663327"
---
# <a name="app-service-environment-overview"></a>Übersicht über die App Service-Umgebung 

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v3 (Vorschau) behandelt.
> 

Die Azure App Service-Umgebung ist ein Feature von Azure App Service, das eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung von App Service-Apps mit umfangreicher Skalierung bereitstellt. Über diese Funktion können folgende Elemente gehostet werden:

- Windows-Web-Apps
- Linux-Web-Apps
- Docker-Container
- Functions

App Service-Umgebungen (App Service Environments, ASEs) sind ideal geeignet für Anwendungsworkloads mit folgenden Anforderungen:

- Großes Ausmaß.
- Isolierung und sicherer Netzwerkzugriff
- Hohe Speicherauslastung
- Viele Anforderungen pro Sekunde (Requests Per Second, RPS). Sie können mehrere ASEs innerhalb einer einzelnen Azure-Region oder über mehrere Azure-Regionen verteilt einrichten. Durch diese Flexibilität eignen sich ASEs hervorragend für die horizontale Skalierung zustandsloser Anwendungen mit vielen Anforderungen pro Sekunde (RPS).

ASEs hosten Anwendungen von nur einem Kunden in einem seiner VNETs. Kunden haben präzise Kontrolle über den eingehenden und ausgehenden Netzwerkdatenverkehr der Anwendung. Anwendungen können schnelle, sichere Verbindungen über VPNs mit lokalen Unternehmensressourcen einrichten.

ASEv3 verfügt über einen eigenen Tarif, Isolated v2.
App Service-Umgebungen v3 bieten eine Umgebung zum Schutz Ihrer Apps in einem Subnetz Ihres Netzwerks sowie eine eigene private Azure App Service-Bereitstellung.
Für die horizontale Skalierung können mehrere ASEs verwendet werden. Der Zugriff von Apps in ASEs kann durch Upstreamgeräte wie z.B. Web Application Firewalls (WAFs) abgegrenzt werden. Weitere Informationen finden Sie unter Web Application Firewall (WAF).

## <a name="usage-scenarios"></a>Verwendungsszenarios

Die App Service-Umgebung umfasst viele Anwendungsfälle, einschließlich:

- Interne Branchenanwendungen
- Anwendungen, die mehr als 30 ASP-Instanzen benötigen
- Ein System mit einem Mandanten, um interne Compliance- oder Sicherheitsanforderungen zu erfüllen
- Netzwerkisoliertes Hosten von Anwendungen
- Multi-Tier-Anwendungen

Es gibt eine Reihe von Netzwerkfeatures, mit denen Apps im mehrinstanzenfähigen App Service netzwerkisolierte Ressourcen erreichen oder selbst netzwerkisoliert werden können. Diese Features werden auf Anwendungsebene aktiviert.  Bei einer ASE ist keine zusätzliche Konfiguration für die Apps erforderlich, damit sie im VNET vorhanden sein können. Die Apps werden in einer netzwerkisolierten Umgebung bereitgestellt, die sich bereits in einem VNET befindet. Die ASE hostet nicht nur netzwerkisolierte Apps, sie ist auch ein System mit nur einem Mandanten. Es gibt keine anderen Kunden, die die ASE verwenden. Wenn Sie wirklich eine vollständige Isolation benötigen, können Sie Ihre ASE auch auf dedizierter Hardware bereitstellen lassen. Zwischen netzwerkisoliertem Hosten von Anwendungen, Einzelmandant und der Fähigkeit 

## <a name="dedicated-environment"></a>Dedizierte Umgebung
Eine ASE wird dediziert für ein einzelnes Abonnement verwendet und kann 200 App Service-Planinstanzen hosten. Dabei kann es sich um 100 Instanzen in einem einzelnen App Service-Plan oder auch um 100 App Service-Einzelinstanzpläne und alles dazwischen handeln.

Eine ASE besteht aus Front-Ends und Worker. Front-Ends sind für die HTTP/HTTPS-Beendigung und den automatischen Lastenausgleich von App-Anforderungen in einer ASE zuständig. Front-Ends werden automatisch hinzugefügt, wenn die App Service-Pläne in der ASE horizontal hochskaliert werden.

Worker sind Rollen, die Kunden-Apps hosten. Worker sind in drei festen Größen verfügbar:

- Zwei vCPUs/8 GB RAM
- Vier vCPUs/16 GB RAM
- Acht vCPUs/32 GB RAM

Kunden müssen keine Front-Ends und Worker verwalten. Die gesamte Infrastruktur ist automatisch. Wenn App Service-Pläne erstellt oder in einer ASE skaliert werden, wird die erforderliche Infrastruktur nach Bedarf hinzugefügt bzw. entfernt.

Es gibt eine Gebühr für Isolated V2-App Service-Plan-Instanzen. Wenn in Ihrer ASE keine App Service-Pläne vorliegen, werden Ihnen Gebühren wie für einen App Service-Plan mit einer Instanz der Zwei-Kern-Worker in Rechnung gestellt.

## <a name="virtual-network-support"></a>Unterstützung für virtuelle Netzwerke
Das ASE-Feature ist eine Bereitstellung von Azure App Service direkt im virtuellen Azure Resource Manager-Netzwerk eines Kunden. Eine ASE ist immer in einem Subnetz eines virtuellen Netzwerks vorhanden. Mithilfe der Sicherheitsfunktionen virtueller Netzwerke können Sie ein- und ausgehende Netzwerkkommunikation für Ihre Apps steuern.

Mithilfe von Netzwerksicherheitsgruppen können Sie die eingehende Netzwerkkommunikation mit dem Subnetz einschränken, das eine ASE enthält. Durch NSGs können Sie Apps hinter Upstreamgeräten und -diensten ausführen, wie WAFs und Netzwerk-SaaS-Anbietern.

Apps müssen häufig auch auf Unternehmensressourcen wie interne Datenbanken und Webdienste zugreifen. Wenn Sie die ASE in einem virtuellen Netzwerk bereitstellen, das über eine VPN-Verbindung mit dem lokalen Netzwerk verfügt, können die Apps in der ASE auf die lokalen Ressourcen zugreifen. Diese Funktion besteht unabhängig davon, ob es sich um ein Site-to-Site-VPN oder ein Azure ExpressRoute-VPN handelt.

## <a name="preview"></a>Vorschau
Die App Service-Umgebung v3 ist als öffentliche Vorschauversion verfügbar.  Einige Features werden im Verlauf der Vorschauphase hinzugefügt. Die aktuellen Einschränkungen von ASEv3 umfassen Folgendes:

- App Service-Pläne können nicht über fünf Instanzen hinaus skaliert werden.
- Es kann kein Container aus einer privaten Registrierung abgerufen werden.
- Derzeit nicht unterstützte App Service-Features können im Kunden-VNET nicht genutzt werden.
- Kein externes Bereitstellungsmodell mit einem Endpunkt, auf den über das Internet zugegriffen werden kann.
- Keine Befehlszeilenunterstützung (AZ CLI und PowerShell)
- Keine Funktion für das Upgrade von ASEv2 auf ASEv3
- Keine FTP-Unterstützung
- Einige App Service-Features können im Kunden-VNET nicht genutzt werden. Sicherung/Wiederherstellung, Key Vault-Verweise in App-Einstellungen, die Verwendung einer privaten Containerregistrierung und die Diagnoseprotokollierung im Speicher funktionieren nicht mit Dienstendpunkten oder privaten Endpunkten.
    
### <a name="asev3-preview-architecture"></a>Architektur der ASEv3-Vorschau
In der ASEv3-Vorschau verwendet die ASE private Endpunkte, um eingehenden Datenverkehr zu unterstützen. Der private Endpunkt wird in der allgemeinen Verfügbarkeit durch Lastenausgleichsmodule ersetzt. In der Vorschauversion verfügt die ASE nicht über integrierte Unterstützung für einen Endpunkt, auf den über das Internet zugegriffen werden kann. Sie könnten für diesen Zweck ein Application Gateway hinzufügen. Die ASE benötigt Ressourcen in zwei Subnetzen.  Eingehender Datenverkehr wird über einen privaten Endpunkt geleitet. Der private Endpunkt kann in einem beliebigen Subnetz platziert werden, sofern er eine verfügbare Adresse hat, die von privaten Endpunkten verwendet werden kann.  Das Ausgangssubnetz muss leer sein und an Microsoft.Web/hostingEnvironments delegiert werden. Während der Verwendung durch die ASE kann das ausgehende Subnetz für nichts anderes verwendet werden.

Bei ASEv3 gibt es keine Netzwerkanforderungen hinsichtlich Eingang oder Ausgang im ASE-Subnetz. Sie können den Datenverkehr mit Netzwerksicherheitsgruppen und Routingtabellen steuern, und dies wirkt sich nur auf den Anwendungsdatenverkehr aus. Löschen Sie nicht den privaten Endpunkt, der Ihrer ASE zugeordnet ist, da diese Aktion nicht rückgängig gemacht werden kann. Der für die ASE verwendete private Endpunkt wird für alle Apps in der ASE verwendet. 
