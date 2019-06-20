---
title: Azure Cloud Services-Definition LoadBalancerProbe-Schema | Microsoft-Dokumentation
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 14
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: de365de7bf93c0a612f102b3ec2b25c79d1c3d18
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60613862"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services-Definition LoadBalancerProbe-Schema
Der Lastenausgleichstest ist ein vom Kunden definierter Integritätstest von UDP-Endpunkten und Endpunkten in Rolleninstanzen. `LoadBalancerProbe` ist kein eigenständiges Element; es wird mit der Webrolle oder der Workerrolle in einer Dienstdefinitionsdatei kombiniert. Ein `LoadBalancerProbe` kann von mehreren Rollen verwendet werden.

Die Standarderweiterung für die Dienstdefinitionsdatei lautet „.csdef“.

## <a name="the-function-of-a-load-balancer-probe"></a>Die Funktion eines Lastenausgleichstests
Der Azure Load Balancer ist für das Routing von eingehendem Datenverkehr zu Ihren Rolleninstanzen verantwortlich. Der Lastenausgleich bestimmt, welche Instanzen Datenverkehr empfangen können, indem sie in regelmäßigen Zeitabständen getestet werden, um die Integrität dieser Instanz zu ermitteln. Bei dem Lastenausgleich wird jede Instanz mehrmals pro Minute getestet. Der Zustand der Instanz kann auf zwei Weisen für den Lastenausgleich bereitgestellt werden: mit dem standardmäßigen Lastenausgleichstest oder mit einem benutzerdefinierten Lastenausgleichstest, der durch die Definition des LoadBalancerProbe in der CSDEF-Datei implementiert wird.

Beim standardmäßigen Lastenausgleichstest wird der Gast-Agent auf dem virtuellen Computer genutzt. Dieser lauscht und antwortet nur dann mit einer HTTP 200 OK-Antwort, wenn die Instanz den Zustand „Bereit“ aufweist (also nicht den Zustand „Ausgelastet“, „Wiederverwendung“ oder „Wird angehalten“). Wenn der Gast-Agent nicht mit HTTP 200 OK antwortet, kennzeichnet der Azure Load Balancer die Instanz als nicht reagierend und sendet keinen Datenverkehr mehr an diese Instanz. Der Azure Load Balancer pingt die Instanz weiterhin, und wenn der Gast-Agent mit einer HTTP 200-Antwort reagiert, sendet der Azure Load Balancer erneut Datenverkehr an diese Instanz. Wenn Sie eine Webrolle verwenden, wird Ihr Websitecode in der Regel in „w3wp.exe“ ausgeführt. Dieses Programm wird nicht von dem Azure-Fabric oder dem Gast-Agent überwacht. Das bedeutet, dass Fehler in „w3wp.exe“ (z.B. HTTP 500-Antworten) dem Gast-Agent nicht gemeldet werden und der Lastenausgleich nicht weiß, ob diese Instanz aus der Rotation entfernt werden soll.

Der benutzerdefinierte Lastenausgleichstest erhält Vorrang vor dem Standard-Gast-Agent-Test und ermöglicht es Ihnen, Ihre eigene benutzerdefinierte Logik zum Bestimmen der Integrität der Rolleninstanz zu erstellen. Beim Lastenausgleich wird in regelmäßigen Zeitabständen Ihr Endpunkt getestet (standardmäßig alle 15 Sekunden), und die Instanz gilt als rotierend, wenn sie innerhalb des Zeitraums (standardmäßig 31 Sekunden) mit einer TCP-Bestätigung oder HTTP 200 antwortet. Dies kann für die Implementierung Ihrer eigenen Logik hilfreich sein, um Instanzen aus der Lastenausgleichsrotation zu entfernen, z.B. bei der Rückgabe eines von 200 abweichenden Status, wenn die Instanz mehr als 90 % CPU-Auslastung erreicht. Für Webrollen, die „w3wp.exe“ verwenden, bedeutet dies auch, dass Ihre Website automatisch überwacht wird, da Fehler im Websitecode einen Nicht-200-Status an den Lastenausgleichstest zurückgibt. Wenn Sie in der .csdef-Datei keinen LoadBalancerProbe definieren, wird das standardmäßige Lastenausgleichsverhalten (wie zuvor beschrieben) verwendet.

Wenn Sie einen benutzerdefinierten Lastenausgleichstest verwenden, müssen Sie sicherstellen, dass Ihre Logik die Methode RoleEnvironment.OnStop berücksichtigt. Bei Verwendung des standardmäßigen Lastenausgleichstests wird die Instanz von der Rotation ausgenommen, bevor OnStop aufgerufen wird. Bei einem benutzerdefinierten Lastenausgleichstest kann während des OnStop-Ereignisses jedoch weiterhin eine 200 OK-Antwort zurückgegeben werden. Bei Verwendung des OnStop-Ereignisses zum Bereinigen des Cache, zum Beenden des Diensts oder zum Durchführen anderer Änderungen, die Auswirkungen auf das Laufzeitverhalten Ihres Diensts haben können, müssen Sie sicherstellen, dass die Logik Ihres benutzerdefinierten Lastenausgleichstests die Instanz aus der Rotation entfernt.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Grundlegendes Dienstdefinitionsschema für einen Lastenausgleichstest
 Das Standardformat einer Dienstdefinitionsdatei, die einen Lastenausgleichstest enthält, lautet wie folgt.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema-Elemente
Das Element `LoadBalancerProbes` der Dienstdefinitionsdatei umfasst folgende Elemente:

- [Element LoadBalancerProbes](#LoadBalancerProbes)
- [Element LoadBalancerProbe](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> Element LoadBalancerProbes
Das Element `LoadBalancerProbes` beschreibt die Sammlung von Lastenausgleichstests. Dieses Element ist dem [LoadBalancerProbe-Element](#LoadBalancerProbe) übergeordnet. 

##  <a name="LoadBalancerProbe"></a> Element LoadBalancerProbe
Das Element `LoadBalancerProbe` definiert den Integritätstest für ein Modell. Sie können mehrere Lastenausgleichstests definieren. 

In der folgenden Tabelle sind die Attribute des `LoadBalancerProbe`-Elements beschrieben:

|Attribut|Type|BESCHREIBUNG|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Erforderlich. Der Name des Lastenausgleichstests. Der Name muss eindeutig sein.|
| `protocol`          | `string` | Erforderlich. Gibt das Protokoll des Endpunkts an. Mögliche Werte sind `http` und `tcp`. Wenn `tcp` angegeben wird, ist der Empfang einer Bestätigung erforderlich, damit der Test erfolgreich ist. Wenn `http` angegeben wird, ist eine 200 OK-Antwort vom angegebenen URI erforderlich, damit der Test erfolgreich ist.|
| `path`              | `string` | Der URI, mit dem der Integritätsstatus über den virtuellen Computer angefordert wird. `path` ist erforderlich, wenn `protocol` auf `http` festgelegt ist. Andernfalls ist die Angabe nicht zulässig.<br /><br /> Es gibt keinen Standardwert.|
| `port`              | `integer` | Optional. Der Port für die Übertragung des Tests. Dies ist für jeden Endpunkt optional, da derselbe Port anschließend für den Test verwendet wird. Sie können einen anderen Port für die Ausführung des Tests konfigurieren. Mögliche Werte reichen von 1 bis einschließlich 65535.<br /><br /> Der Standardwert wird vom Endpunkt festgelegt.|
| `intervalInSeconds` | `integer` | Optional. Das Intervall in Sekunden, in dem der Endpunkt auf den Integritätsstatus getestet werden soll. In der Regel ist das Intervall etwas kleiner als die Hälfte des zugeordneten Zeitlimits (in Sekunden). Dies ermöglicht die Durchführung von zwei vollständigen Tests, bevor die Instanz von der Rotation ausgenommen wird.<br /><br /> Der Standardwert beträgt 15, der Mindestwert 5.|
| `timeoutInSeconds`  | `integer` | Optional. Das Zeitlimit in Sekunden für den Test, bei dem keine Antwort dazu führt, dass die Übermittlung von weiterem Datenverkehr an den Endpunkt beendet wird. Mit diesem Wert können Endpunkte schneller oder langsamer als bei den typischen, in Azure verwendeten Zeiten (Standardwerte) von der Rotation ausgenommen werden.<br /><br /> Der Standardwert beträgt 31, der Mindestwert 11.|

## <a name="see-also"></a>Siehe auch
[Clouddienst-Definitionsschema (klassisch)](schema-csdef-file.md)