---
title: Azure Cloud Services-Definitionsschema (CSCFG-Datei) | Microsoft-Dokumentation
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 424381e2c243420cc2a68dc776d249cb17574f98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130312"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services-Konfigurationsschema (CSCFG-Datei)
Die Dienstkonfigurationsdatei gibt die Anzahl der Rolleninstanzen, die für jede Rolle im Dienst bereitgestellt werden, die Werte aller Konfigurationseinstellungen und die Fingerabdrücke für alle einer Rolle zugeordneten Zertifikate an. Wenn der Dienst Teil eines virtuellen Netzwerks ist, müssen Konfigurationsinformationen für das Netzwerk in der Dienstkonfigurationsdatei sowie in der Konfigurationsdatei für virtuelle Netzwerke bereitgestellt werden. Die Standarderweiterung für die Dienstkonfigurationsdatei ist .cscfg.

Das Dienstmodell wird durch das [Cloud Service (classic) Definition Schema (Clouddienst (klassisch)-Definitionsschema)](schema-csdef-file.md) beschrieben.

Standardmäßig wird die Konfigurationsschemadatei der Azure-Diagnose im `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`-Verzeichnis installiert. Ersetzen Sie `<version>` durch die installierte Version des [Azure SDK](https://azure.microsoft.com/downloads/).

Weitere Informationen zum Konfigurieren von Rollen in einem Dienst finden Sie unter [What is the Cloud Service model (Was ist das Clouddienstmodell)](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Basic Service-Konfigurationsschema
Das grundlegende Format der Dienstkonfigurationsdatei sieht wie folgt aus.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Schemadefinitionen
Die folgenden Themen beschreiben das Schema für das Element `ServiceConfiguration`:

- [Rollenschema](schema-cscfg-role.md)
- [NetworkConfiguration-Schema](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Dienstkonfigurations-Namespace
Der XML-Namespace für die Dienstkonfigurationsdatei lautet: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> ServiceConfiguration-Element
Das Element `ServiceConfiguration` ist das Element der obersten Ebene der Dienstkonfigurationsdatei.

In der folgenden Tabelle werden die Attribute des Elements `ServiceConfiguration` beschrieben. Alle Attributwerte sind Zeichenfolgentypen.

| Attribut | BESCHREIBUNG |
| --------- | ----------- |
|serviceName|Erforderlich. Der Name des Clouddiensts. Der hier angegebene Name muss mit dem Namen in der Dienstdefinitionsdatei übereinstimmen.|
|osFamily|Optional. Gibt das Gastbetriebssystem an, das auf Rolleninstanzen im Clouddienst ausgeführt wird. Nähere Informationen zu unterstützten Gastbetriebssystem-Releases finden Sie unter [Azure Gastbetriebssystem-Releases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md).<br /><br /> Wenn Sie keinen `osFamily`-Wert einschließen, und wenn Sie das `osVersion`-Attribut nicht für eine bestimmte Gastbetriebssystem-Version festgelegt haben, wird ein Standardwert 1 verwendet.|
|osVersion|Optional. Gibt die Version des Gastbetriebssystems an, das auf Rolleninstanzen im Clouddienst ausgeführt wird. Nähere Informationen zu unterstützten Gastbetriebssystem-Versionen finden Sie unter [Azure Gastbetriebssystem-Releases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md).<br /><br /> Sie können angeben, dass das Gastbetriebssystem automatisch auf die neueste Version aktualisiert werden soll. Zu diesem Zweck legen Sie den Wert des `osVersion`-Attributs auf `*` fest. Bei Festlegung auf `*` werden die Rolleninstanzen mit der neuesten Version des Gastbetriebssystems für die angegebene Betriebssystemfamilie bereitgestellt und automatisch aktualisiert, wenn neue Versionen des Gastbetriebssystems freigegeben werden.<br /><br /> Wenn Sie eine bestimmte Version manuell angeben möchten, verwenden Sie die `Configuration String` aus der Tabelle im Abschnitt **Future, Current and Transitional Guest OS Versions (Zukünftige-, Aktuelle- und Übergangs- Gastversionen)** der [Azure Gastbetriebssystem-Releases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md).<br /><br /> Der Standardwert für die `osVersion`-Eigenschaft ist `*`.|
|Schemaversion|Optional. Gibt die Version des Dienstkonfigurationsschemas an. Die Schemaversion ermöglicht Visual Studio, die richtigen SDK-Tools für die Schemaüberprüfung auszuwählen, wenn mehrere Versionen des SDK nebeneinander installiert sind. Nähere Informationen zu Schema- und Versionskompatibilität finden Sie unter [Azure Gastbetriebssystem-Releases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md).|

Die Dienstkonfigurationsdatei muss ein `ServiceConfiguration`-Element enthalten. Das `ServiceConfiguration`-Element kann eine beliebige Anzahl von `Role`-Elementen und 0 (null) oder 1 `NetworkConfiguration`-Elemente enthalten.