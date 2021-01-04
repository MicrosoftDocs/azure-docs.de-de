---
title: Häufig gestellte Fragen (FAQ) zu Cognitive Services-Containern
titleSuffix: Azure Cognitive Services
description: Häufig gestellte Fragen und Antworten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 188a348b497a485be0a8091b50a3f2c11b6493c0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960749"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu Azure Cognitive Services-Containern

## <a name="general-questions"></a>Allgemeine Fragen

**F: Was ist verfügbar?**

**A:** Mithilfe von Azure Cognitive Services-Containern können Entwickler dieselben intelligenten APIs wie in Azure verwenden, die dabei aber über die [Vorteile](../cognitive-services-container-support.md#features-and-benefits) der Containerisierung verfügen. Einige Container sind als geschlossene Vorschau verfügbar, für die möglicherweise eine Bewerbung für den Zugriff erforderlich ist. Andere Container sind als nicht geschlossene Vorschauversion öffentlich oder allgemein verfügbar. Eine vollständige Liste der Container und ihrer Verfügbarkeit finden Sie im Artikel [Containerunterstützung in Azure Cognitive Services](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services). Sie können die Container auch im [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services) anzeigen.

**F: Gibt es einen Unterschied zwischen der Cognitive Services-Cloud und den Containern?**

**A:** Cognitive Services-Container sind eine Alternative zur Cognitive Services-Cloud. Container bieten dieselben Funktionen wie die entsprechenden Clouddienste. Kunden können die Container lokal oder in Azure bereitstellen. Die KI-Kerntechnologie, die Tarife, die API-Schlüssel und die API-Signatur sind zwischen dem Container und den entsprechenden Clouddiensten identisch. Hier sind die [Features und Vorteile](../cognitive-services-container-support.md#features-and-benefits) für die Auswahl von Containern gegenüber ihrer Entsprechung für Clouddienste aufgeführt.

**F: Wie kann ich auf einen Container der beschränkten Vorschauversion zugreifen und diesen verwenden?**

**A:** Bisher wurden Container der beschränkten Vorschau im Repository `containerpreview.azurecr.io` gehostet. Ab dem 22. September 2020 werden diese Container in Microsoft Container Registry gehostet. Für den Download muss nicht der Docker-Anmeldebefehl verwendet werden. Sie können einen Container der beschränkten Vorschau ausführen, wenn Ihre Azure-Ressource mit einer genehmigten Azure-Abonnement-ID erstellt wurde. Sie können den Container nicht ausführen, wenn Ihr Azure-Abonnement nach dem Ausfüllen des [Anforderungsformulars](https://aka.ms/csgate) nicht genehmigt wurde.


**F: Werden Container für alle Cognitive Services zur Verfügung gestellt und welchen Containersatz können wir als nächstes erwarten?**

**A:** Wir möchten weitere Cognitive Services als Containerangebote zur Verfügung stellen. Wenden Sie sich an Ihren örtlichen Microsoft Account Manager, um Updates zu neuen Containerversionen und andere Ankündigungen zu Cognitive Services zu erhalten.

**F: Wie sieht die Vereinbarung zum Servicelevel (SLA) für Cognitive Services-Container aus?**

**A:** Cognitive Services-Container besitzen keine Vereinbarung zum Servicelevel (SLA).

Cognitive Services-Containerkonfigurationen von Ressourcen werden von Kunden kontrolliert, sodass Microsoft keine SLAs für die allgemeine Verfügbarkeit (General Availability, GA) anbieten wird. Kunden können Container lokal bereitstellen und definieren somit die Hostumgebungen.

> [!IMPORTANT]
> Weitere Informationen zu Vereinbarungen zum Servicelevel (SLA) für Cognitive Services finden Sie auf unserer [Seite zur Vereinbarung zum Servicelevel](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**F: Sind diese Container in Sovereign Clouds verfügbar?**

**A:** Nicht jeder ist mit dem Begriff „Sovereign Cloud“ vertraut, daher beginnen wir mit der Definition:

> Die „Sovereign Cloud“ besteht aus den Clouds [Azure Government](../../azure-government/documentation-government-welcome.md), [Azure Deutschland](../../germany/germany-welcome.md) und [Azure China 21Vianet](/azure/china/overview-operations).

Leider werden die Cognitive Services-Container *nicht* nativ in den Sovereign Clouds unterstützt. Die Container können in diesen Clouds ausgeführt werden, aber sie werden aus der Public Cloud abgerufen und müssen Nutzungsdaten an den öffentlichen Endpunkt senden.

### <a name="versioning"></a>Versionsverwaltung

**F: Wie werden Container auf die neueste Version aktualisiert?**

**A:** Kunden können wählen, wann sie die von ihnen bereitgestellten Container aktualisieren möchten. Container werden mit standardmäßigen [Docker-Tags](https://docs.docker.com/engine/reference/commandline/tag/) wie `latest` gekennzeichnet, um die neueste Version anzuzeigen. Wir empfehlen unseren Kunden, die neueste Version der Container zu installieren, sobald sie freigegeben werden. Weitere Informationen zur Benachrichtigung bei aktualisierten Images finden Sie unter [Azure Container Registry-Webhooks](../../container-registry/container-registry-webhook.md).
 
**F: Welche Versionen werden unterstützt?**

**A:** Die aktuelle und letzte Hauptversion des Containers wird unterstützt. Wir empfehlen unseren Kunden jedoch, auf dem Laufenden zu bleiben, um die neueste Technologie zu erhalten.
 
**F: Wie werden Updates versioniert?**

**A:** Änderungen an der Hauptversion deuten darauf hin, dass es ein Breaking Change an der API-Signatur gibt. Wir gehen davon aus, dass dies in der Regel mit wesentlichen Versionsänderungen des entsprechenden Cognitive Service-Cloudangebots einhergehen wird. Geringfügige Versionsänderungen deuten auf Fehlerbehebungen, Modellupdates oder neue Features hin, die kein Breaking Change für die API-Signatur bewirken.

## <a name="technical-questions"></a>Technische Fragen

**F: Wie kann ich die Cognitive Services-Container auf IoT-Geräten ausführen?**

**A:** Unabhängig davon, ob Sie keine zuverlässige Internetverbindung besitzen oder bei den Bandbreitenkosten sparen möchten. Oder wenn Sie Anforderungen mit niedriger Latenz haben oder es sich um vertrauliche Daten handelt, die lokal analysiert werden müssen, bietet Ihnen [Azure IoT Edge mit den Cognitive Services-Containern](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) Konsistenz mit der Cloud.

**F: Sind diese Container mit OpenShift kompatibel?** 

Wir testen keine Container mit OpenShift, aber im Allgemeinen sollten Cognitive Services-Container auf jeder Plattform ausgeführt werden können, die Docker-Images unterstützt. Wenn Sie OpenShift verwenden, empfehlen wir, die Container als `root-user` auszuführen.

**F: Wie stelle ich Produktfeedback und Featureempfehlungen bereit?**

**A:** Kunden werden aufgefordert, [ihre Bedenken öffentlich zu äußern](https://cognitive.uservoice.com/) und andere, die ebenso vorgegangen sind, an den Stellen, an denen sich potenzielle Probleme überschneiden, zu unterstützen. Das User Voice Tool kann sowohl für Produktfeedback als auch für Featureempfehlungen verwendet werden.

**F: Welche Statusmeldungen und Fehler werden von Cognitive Services-Containern zurückgegeben?**

**A:** In der folgenden Tabelle finden Sie eine Liste von Statusmeldungen und Fehlern.

|Status  | Beschreibung  |
|---------|---------|
| `Valid` | Ihr API-Schlüssel ist gültig, es ist keine Aktion erforderlich. |
| `Invalid` |   Ihr API-Schlüssel ist ungültig. Sie müssen einen gültigen API-Schlüssel angeben, um den Container auszuführen. Sie finden den API-Schlüssel und die Dienstregion im Azure-Portal im Abschnitt **Schlüssel und Endpunkt** Ihrer Azure Cognitive Services-Ressource. |
| `Mismatch` | Sie haben einen API-Schlüssel oder -Endpunkt für einen anderen Typ von Cognitive Services-Ressource angegeben. Sie finden den API-Schlüssel und die Dienstregion im Abschnitt **Schlüssel und Endpunkt** Ihrer Azure Cognitive Services-Ressource. |
| `CouldNotConnect` | Der Container konnte keine Verbindung mit dem Abrechnungsendpunkt herstellen. Überprüfen Sie den Wert von `Retry-After`, und warten Sie, bis dieser Zeitraum abgelaufen ist, bevor Sie zusätzliche Anforderungen übermitteln. |
| `OutOfQuota` | Der API-Schlüssel hat das Kontingent überschritten. Sie können entweder ein Upgrade Ihres Tarifs durchführen oder warten, bis zusätzliches Kontingent verfügbar gemacht wird. Sie finden Ihren Tarif im Azure-Portal im Abschnitt **Tarif** Ihrer Azure Cognitive Service-Ressource. |
| `BillingEndpointBusy` | Der Abrechnungsendpunkt ist zurzeit ausgelastet. Überprüfen Sie den Wert von `Retry-After`, und warten Sie, bis dieser Zeitraum abgelaufen ist, bevor Sie zusätzliche Anforderungen übermitteln. |
| `ContainerUseUnauthorized` | Der angegebene API-Schlüssel ist nicht für die Verwendung mit diesem Container autorisiert. Sie verwenden wahrscheinlich einen Container der eingeschränkten Vorschauversion. Stellen Sie daher sicher, dass Ihre Azure-Abonnement-ID genehmigt wurde, indem Sie eine [Onlineanforderung](https://aka.ms/csgate) übermitteln. |
| `Unknown` | Der Server kann derzeit keine Abrechnungsanforderungen verarbeiten. |


**F: An wen kann ich mich wenden, um Support zu erhalten?**

**A:** Die Kundensupportkanäle sind dieselben wie beim Cognitive Services-Cloudangebot. Alle Cognitive Services-Container enthalten Protokollierungsfeatures, die uns und den Kunden des Communitysupports helfen. Weitere Unterstützung finden Sie unter den folgenden Optionen.

### <a name="customer-support-plan"></a>Kundensupportplan

Kunden sollten sich auf ihren [Azure-Supportplan](https://azure.microsoft.com/support/plans/) beziehen, um zu prüfen, an wen sie sich hinsichtlich des Supports wenden können.

### <a name="azure-knowledge-center"></a>Azure Knowledge Center

Kunden können das [Azure Knowledge Center](https://azure.microsoft.com/resources/knowledge-center/) erkunden, um Fragen und Supportthemen zu beantworten.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) ist eine Frage- und Antwortseite für professionelle und begeisterte Programmierer.

Untersuchen Sie die folgenden Tags auf mögliche Fragen und Antworten, die Ihren Anforderungen entsprechen.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitive](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**F: Wie funktioniert die Abrechnung?**

**A:** Die Abrechnung erfolgt verbrauchsorientiert, ähnlich wie in der Cognitive Services-Cloud. Die Container müssen konfiguriert werden, um Messdaten an Azure zu senden, damit die Transaktionen entsprechend abgerechnet werden können. Die Ressourcen, die für die gehosteten und lokalen Dienste verwendet werden, werden zu einem einzelnen Kontingent mit gestaffelten Preisen hinzugefügt, wobei beide Verwendungen gegeneinander abgewogen werden. Weitere Informationen finden Sie auf der Seite mit der Preisübersicht des entsprechenden Angebots.

* [Anomalieerkennung][ad-containers-billing]
* [Maschinelles Sehen][cv-containers-billing]
* [Gesichtserkennung][fa-containers-billing]
* [Formularerkennung][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Spracherkennungsdienst-API][sp-containers-billing]
* [Textanalyse][ta-containers-billing]

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten an Microsoft.
 
**F: Wie lautet die aktuelle Supportgarantie für Container?**

**A:** Es gibt keine Garantie für Vorschauversionen. Die Standardgarantie von Microsoft für Unternehmenssoftware gilt, wenn Container offiziell als allgemeine Verfügbarkeit (GA) angekündigt werden.
 
**F: Was geschieht mit Cognitive Services-Containern, wenn die Internetverbindung unterbrochen wird?**

**A:** Für die Ausführung von Cognitive Services-Containern besteht *keine Lizenz*, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit mit dem Messungsdienst kommunizieren können.

**F: Wie lange kann der Container ohne Verbindung mit Azure betrieben werden?**

**A:** Für die Ausführung von Cognitive Services-Containern besteht *keine Lizenz*, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit mit dem Messungsdienst kommunizieren können.
 
**F: Welche aktuelle Hardware wird für den Betrieb dieser Container benötigt?**

**A:** Cognitive Services-Container sind x64-basierte Container, die alle kompatiblen Linux-Knoten, VMs und Edge-Geräte ausführen können, die x64-Linux-Docker-Container unterstützen. Sie alle erfordern CPU-Prozessoren. Die minimalen und empfohlenen Konfigurationen für die einzelnen Containerangebote sind unten aufgeführt:

* [Anomalieerkennung][ad-containers-recommendations]
* [Maschinelles Sehen][cv-containers-recommendations]
* [Gesichtserkennung][fa-containers-recommendations]
* [Formularerkennung][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Spracherkennungsdienst-API][sp-containers-recommendations]
* [Textanalyse][ta-containers-recommendations]
 
**F: Werden diese Container derzeit unter Windows unterstützt?**

**A:** Die Cognitive Services-Container sind Linux-Container, es gibt jedoch eine gewisse Unterstützung für Linux-Container unter Windows. Weitere Informationen zu Linux-Containern unter Windows finden Sie in der [Docker-Dokumentation](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**F: Wie kann ich die Container ermitteln?**

**A:** Cognitive Services-Container sind an verschiedenen Orten verfügbar, z. B. im Azure-Portal, im Docker-Hub und in Azure-Containerregistrierungen. Die neuesten Containerstandorte finden Sie unter [Containerrepositorys und -images](../cognitive-services-container-support.md#container-repositories-and-images).

**F: Wie schneiden Cognitive -Container im Vergleich zu AWS- und Google-Angeboten ab?**

**A:** Microsoft ist der erste Cloudanbieter, der seine vorab trainierten KI-Modelle in Container mit einfacher Abrechnung pro Transaktion verschiebt, als ob Kunden einen Clouddienst nutzen würden. Microsoft ist überzeugt, dass eine Hybrid Cloud den Kunden mehr Auswahl bietet.

**F: Welche Compliancezertifizierungen gibt es für Container?**

**A:** Cognitive Services-Container verfügen über keine Compliancezertifizierungen.

**F: In welchen Regionen sind Cognitive Services-Container verfügbar?**

**A:** Container können in jeder Region betrieben werden, benötigen aber einen Schlüssel und müssen zur Messung auf Azure zurückgreifen. Alle unterstützten Regionen für den Clouddienst werden für den Messaufruf des Containers unterstützt.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations