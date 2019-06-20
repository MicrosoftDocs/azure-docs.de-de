---
title: Azure Cloud Services-Definitionsschema (CSDEF-Datei) | Microsoft-Dokumentation
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: ea373c7b35ef82496690f213b92cc97f3536c57a
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356151"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services-Definitionsschema (.CSDEF-Datei)
Die Dienstdefinitionsdatei definiert das Dienstmodell für eine Anwendung. Die Datei enthält die Definitionen für die Rollen, die für einen Clouddienst verfügbar sind, gibt die Anbieterendpunkte an und legt Konfigurationseinstellungen für den Dienst fest. Konfigurationseinstellungswerte werden in der Dienstkonfigurationsdatei festgelegt, wie im [Clouddienst-Konfigurationsschema (klassisch)](/previous-versions/azure/reference/ee758710(v=azure.100)) beschrieben.

Standardmäßig wird die Konfigurationsschemadatei der Azure-Diagnose im `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`-Verzeichnis installiert. Ersetzen Sie `<version>` durch die installierte Version des [Azure SDK](https://www.windowsazure.com/develop/downloads/).

Die Standarderweiterung für die Dienstdefinitionsdatei lautet „.csdef“.

## <a name="basic-service-definition-schema"></a>Basisdienstdefinitionsschema
Die Dienstdefinitionsdatei muss ein `ServiceDefinition`-Element enthalten. Die Dienstdefinition muss mindestens ein Rollenelement enthalten (`WebRole` oder `WorkerRole`). Sie kann bis zu 25 Rollen enthalten, die in einer Definition definiert sind. Rollentypen können miteinander kombiniert werden. Zudem enthält die Dienstdefinition das optionale Element `NetworkTrafficRules`, mit dem eingeschränkt werden kann, welche Rollen mit angegebenen internen Endpunkten kommunizieren können. Die Dienstdefinition enthält auch das optionale Element `LoadBalancerProbes`, das vom Kunden definierte Integritätstests von Endpunkten enthält.

Das Standardformat der Dienstdefinitionsdatei sieht wie folgt aus.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Schemadefinitionen
In den folgenden Themen wird das Schema beschrieben:

- [LoadBalancerProbe-Schema](schema-csdef-loadbalancerprobe.md)
- [WebRole-Schema](schema-csdef-webrole.md)
- [WorkerRole-Schema](schema-csdef-workerrole.md)
- [NetworkTrafficRules-Schema](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> Element ServiceDefinition
Das Element `ServiceDefinition` ist das Element der obersten Ebene der Dienstdefinitionsdatei.

In der folgenden Tabelle werden die Attribute des Elements `ServiceDefinition` beschrieben.

| Attribut               | BESCHREIBUNG |
| ----------------------- | ----------- |
| name                    |Erforderlich. Der Name des Diensts. Der Name muss innerhalb des Dienstkontos eindeutig sein.|
| topologyChangeDiscovery | Optional. Gibt die Änderungsbenachrichtigung zur Art der Topologie an. Mögliche Werte:<br /><br /> -   `Blast`: Sendet das Update so schnell wie möglich an alle Rolleninstanzen. Wenn Sie die Option auswählen, sollte die Rolle das Topologieupdate verarbeiten können, ohne neu gestartet werden zu müssen.<br />-   `UpgradeDomainWalk`: Sendet das Update sequenziell an alle Rolleninstanzen, nachdem die vorherige Instanz das Update erfolgreich akzeptiert hat.|
| Schemaversion           | Optional. Gibt die Version des Dienstdefinitionsschemas an. Die Schemaversion ermöglicht Visual Studio, die richtigen SDK-Tools für die Schemaüberprüfung auszuwählen, wenn mehrere Versionen des SDK nebeneinander installiert sind.|
| upgradeDomainCount      | Optional. Gibt die Anzahl von Upgradedomänen an, über die die Rollen in diesem Dienst zugeordnet werden. Rolleninstanzen werden bei der Bereitstellung des Diensts einer Upgradedomäne zugeordnet. Weitere Informationen finden Sie unter [Aktualisieren einer Clouddienstrolle oder -bereitstellung](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) und [Was ist das Clouddienstmodell, und wie kann es gepackt werden?](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Sie können bis zu 20 Upgradedomänen angeben. Bei fehlender Angabe wird der Standardwert in Höhe von 5 Upgradedomänen verwendet.|
