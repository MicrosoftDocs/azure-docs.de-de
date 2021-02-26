---
title: Häufig gestellte Fragen zu FHIR-Diensten in Azure – Azure API for FHIR
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure API for FHIR, wie z. B. Angaben zum Speicherort von Daten hinter FHIR-APIs und zur Versionsunterstützung.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: d83bc653ae8c3ff5a9553de568bc6f2355f18760
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104619"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Häufig gestellte Fragen zu Azure API for FHIR

## <a name="azure-api-for-fhir-the-basics"></a>Azure API for FHIR: Die Grundlagen

### <a name="what-is-fhir"></a>Was ist FHIR?
Fast Healthcare Interoperability Resources (FHIR, ausgesprochen wie das englische Wort „Fire“) ist ein Interoperabilitätsstandard, der den Austausch von Patientendaten zwischen verschiedenen Gesundheitssystemen ermöglicht. Dieser Standard wurde von der HL7-Organisation entwickelt und wird von Gesundheitsorganisationen auf der ganzen Welt übernommen. Die aktuelle Version von FHIR ist R4 (Release 4). Azure API for FHIR unterstützt R4 und auch die vorherige Version STU3 (Standard for Trial Use 3). Weitere Informationen zu FHIR finden Sie auf [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Werden die Daten hinter den FHIR-APIs in Azure gespeichert?

Ja. Die Daten werden in verwalteten Datenbanken in Azure gespeichert. Die Azure API for FHIR bietet keinen direkten Zugriff auf den zugrunde liegenden Datenspeicher.

### <a name="what-identity-provider-do-you-support"></a>Welcher Identitätsanbieter wird unterstützt?

Derzeit wird Microsoft Azure Active Directory als Identitätsanbieter unterstützt.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Wie lautet die RPO (Recovery Point Objective) für die Azure API for FHIR?
Die Azure API for FHIR wird durch Cosmos DB – unseren Persistenzanbieter – gesichert. Aus diesem Grund entspricht die RPO für den Dienst der für [Cosmos DB (Einzelregion)](../cosmos-db/consistency-levels.md) und liegt unter 240 Minuten.

### <a name="what-fhir-version-do-you-support"></a>Welche FHIR-Version wird unterstützt?

Die Versionen 4.0.0 und 3.0.1 werden sowohl in Azure API for FHIR (PaaS) als auch auf dem FHIR-Server für Azure (Open Source) unterstützt.

Ausführliche Informationen finden Sie unter [Unterstützte Features](fhir-features-supported.md). Informationen zu den Änderungen zwischen FHIR-Versionen (also zwischen STU3 und R4) finden Sie im [Versionsverlauf für HL7 FHIR](https://hl7.org/fhir/R4/history.html).

Von Azure IoT Connector for FHIR (Vorschau) wird aktuell nur die FHIR-Version R4 unterstützt, und der Connector ist nur in R4-Instanzen von Azure API for FHIR sichtbar.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Worin besteht der Unterschied zwischen dem Microsoft-FHIR-Server für Azure und Azure API for FHIR?

Azure API for FHIR ist eine gehostete und verwaltete Version des Microsoft Open-Source-FHIR-Servers für Azure. Im verwalteten Dienst stellt Microsoft die gesamte Wartung und alle Updates zur Verfügung. 

Bei Verwendung des FHIR-Servers für Azure haben Sie direkten Zugriff auf die zugrunde liegenden Dienste, sind aber auch für die Wartung und Aktualisierung des Servers sowie für alle erforderlichen Complianceaufgaben zuständig, wenn Sie PHI-Daten speichern.

Aus Entwicklungssicht wird jedes Feature, das nicht nur für den verwalteten Dienst gilt, zuerst für den Microsoft Open-Source-FHIR-Server für Azure bereitgestellt. Nachdem es in der Open-Source-Lösung überprüft wurde, wird es in der PaaS-Lösung von Azure API for FHIR veröffentlicht. Die Zeit zwischen der Veröffentlichung in der Open-Source- und der PaaS-Lösung hängt von der Komplexität des Features und anderen Roadmapprioritäten ab. Dieser Prozess ist für alle unsere Dienste gleich – beispielsweise für Azure IoT Connector for FHIR (Vorschau).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Wo kann ich sehen, was in Azure API for FHIR veröffentlicht wird?

Informationen zu den Funktionen, die in Azure API for FHIR veröffentlicht werden, finden Sie in dem [Release](https://github.com/microsoft/fhir-server/releases) des Open-Source-FHIR-Servers. Ab November 2020 werden Elemente mit „Azure-API-for-FHIR“ gekennzeichnet, wenn das Open-Source-Element für den verwalteten Dienst veröffentlicht wird. Diese Features werden in der Regel zwei Wochen nach Anzeige auf der Releaseseite in Open-Source verfügbar. Darüber hinaus haben wir [hier] (https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) eine Anleitung zum Testen des Builds bereitgestellt, wenn Sie Tests in Ihrer eigenen Umgebung durchführen möchten. Wir evaluieren, wie sich zusätzliche verwaltete Dienstupdates am besten teilen lassen.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>In welchen Regionen ist Azure API for FHIR verfügbar?

Aktuell besteht sowohl für den öffentlichen und behördlichen Bereich allgemeine Verfügbarkeit in [mehreren geografischen Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia). Weitere Informationen zu Government Cloud Services bei Microsoft finden Sie unter [Azure-Dienste nach FedRAMP- und DoD CC SRG-Prüfumfang](../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Wo kann ich sehen, was in Azure API for FHIR veröffentlicht wird?

Informationen zu den Funktionen, die in Azure API for FHIR veröffentlicht werden, finden Sie in dem [Release](https://github.com/microsoft/fhir-server/releases) des Open-Source-FHIR-Servers. Wir haben daran gearbeitet, Elemente mit Azure-API-for-FHIR zu markieren, wenn Sie im verwalteten Dienst freigegeben werden, und sie sind in der Regel zwei Wochen nach Anzeige auf der Releaseseite in Open-Source verfügbar. Wir haben außerdem Anweisungen zum Testen des Builds [hier](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) eingeschlossen, wenn Sie Tests in Ihrer eigenen Umgebung durchführen möchten. Wir evaluieren, wie sich zusätzliche verwaltete Dienstupdates am besten teilen lassen.

### <a name="what-is-smart-on-fhir"></a>Was ist SMART on FHIR?

Bei SMART (Substitutable Medical Applications and Reusable Technology) on FHIR handelt es sich um eine Reihe offener Spezifikationen für die Integration von Partneranwendungen in FHIR-Server und andere IT-Systeme im Gesundheitswesen, wie elektronische Patientenakten und den Austausch von Gesundheitsinformationen. Indem Sie eine SMART on FHIR-Anwendung erstellen, können Sie sicherstellen, dass auf Ihre Anwendung zugegriffen und diese von einer Vielzahl unterschiedlicher Systeme genutzt werden kann.
Authentifizierung und Azure API for FHIR. Weitere Informationen zu SMART finden Sie unter [SMART Health IT](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Wo sehe ich, welche Version von FHIR für meine Datenbank ausgeführt wird? 

Die genaue FHIR-Version finden Sie in der Funktionsbestätigung unter der Eigenschaft „fhirVersion“.

## <a name="fhir-implementations-and-specifications"></a>FHIR-Implementierungen and -Spezifikationen

### <a name="can-i-create-a-custom-fhir-resource"></a>Kann ich eine benutzerdefinierte FHIR-Ressource erstellen?

Wir lassen keine benutzerdefinierten FHIR-Ressourcen zu. Wenn Sie eine benutzerdefinierte FHIR-Ressource benötigen, können Sie eine benutzerdefinierte Ressource auf Grundlage der [Basisressource](http://www.hl7.org/fhir/basic.html) mit Erweiterungen erstellen. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Werden für Azure API for FHIR [Erweiterungen](https://www.hl7.org/fhir/extensibility.html) unterstützt?

Wir erlauben Ihnen, alle gültigen FHIR-JSON-Daten in den Server zu laden. Wenn Sie die Strukturdefinition, die die Erweiterung definiert, speichern möchten, können Sie diese als Strukturdefinitionsressource speichern. Derzeit ist es nicht möglich, nach Erweiterungen zu suchen.

### <a name="what-is-the-limit-on-_count"></a>Welcher Grenzwert gilt für _count?

Der aktuelle Grenzwert für „_count“ liegt bei 100. Wenn Sie „_count“ auf mehr als 100 festlegen, wird im Paket eine Warnung mit dem Hinweis angezeigt, dass nur 100 Datensätze angezeigt werden.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Gelten Einschränkungen für die Funktion „Gruppenexport“?

Für „Gruppenexport“ exportieren wir nur die eingeschlossenen Verweise aus der Gruppe, nicht alle Merkmale der [Gruppenressource](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Kann ich ein Bündel in Azure API for FHIR bereitstellen?

Zurzeit unterstützen wir das Bereitstellen von [Batchbündeln](https://www.hl7.org/fhir/valueset-bundle-type.html), wir unterstützen aber nicht das Bereitstellen von Transaktionsbündeln in Azure API for FHIR. Sie können den Open-Source-FHIR-Server verwenden, der von SQL unterstützt wird, um Transaktionsbündel bereitzustellen.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Wie kann ich alle Ressourcen für einen einzelnen Patienten in Azure API for FHIR abrufen?

Wir unterstützen die [Depotsuche](https://www.hl7.org/fhir/compartmentdefinition.html) in Azure API for FHIR. Auf diese Weise können Sie alle Ressourcen, die auf einen bestimmten Patienten bezogen sind, abrufen. Beachten Sie, dass ein Depot zurzeit alle Ressourcen enthält, die sich auf den Patienten beziehen, aber nicht den Patienten selbst, sodass Sie auch nach dem Patienten suchen müssen, wenn Sie die Patientenressource in Ihren Ergebnissen benötigen.

Einige Beispiele hierfür sehen Sie im Folgenden:

* GET Patient/<id>/*
* GET Patient/<id>/Observation
* GET Patient/<id>/Observation?code=8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Welche Sortierung wird bei der Suche nach Ressourcen in Azure API for FHIR standardmäßig verwendet?

Als Sortierkriterium wird das Datum der letzten Aktualisierung unterstützt: _sort=_lastUpdated. Weitere Informationen zu anderen unterstützten Suchparametern finden Sie auf der [Seite mit den unterstützten Features](./fhir-features-supported.md#search).

### <a name="does-the-azure-api-for-fhir-support-everything"></a>Unterstützt die Azure API for FHIR „$everything“? 

Nein. Derzeit wird „$everything“ nicht unterstützt. Dies kann jedoch mit zwei API-Aufrufen erreicht werden. Wenn Sie z. B. „Patient$everything“ abrufen möchten, können Sie zuerst den Patientendatensatz mithilfe von „/Patient/[ID]“ abrufen und dann mit einem zweiten Aufruf alle Patientendaten mit „/Patient/[ID]/*“ abrufen.

Weitere Details finden Sie in diesem [Communitybeitrag](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/.24everything.20with.20_type). 

### <a name="how-does-export-work"></a>Wie funktioniert „$export“?

„$export“ ist Teil der FHIR-Spezifikation: https://hl7.org/fhir/uv/bulkdata/export/index.html. Wenn der FHIR-Dienst mit einer verwalteten Identität und einem Speicherkonto konfiguriert ist und die verwaltete Identität Zugriff auf dieses Speicherkonto hat, können Sie einfach „$export“ über die FHIR-API aufrufen. Daraufhin werden alle FHIR-Ressourcen in das Speicherkonto exportiert. Weitere Informationen finden Sie im [Artikel zu „$export“](./export-data.md).

### <a name="is-de-identified-export-available-at-patient-and-group-level-as-well"></a>Ist der anonymisierte Export auch auf Patienten- und Gruppenebene verfügbar?
Der anonymisierte Export wird derzeit nur bei einem vollständigen Systemexport (/$export) und nicht für den Patientenexport (/Patient/$export) unterstützt. Wir arbeiten daran, diesen Export auch auf Patientenebene verfügbar zu machen.

## <a name="using-azure-api-for-fhir"></a>Verwenden von Azure API for FHIR

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Wie kann ich Log Analytics für Azure API for FHIR aktivieren?

Wir aktivieren die Diagnoseprotokollierung und ermöglichen die Überprüfung von Beispielabfragen für diese Protokolle. Ausführliche Informationen zum Aktivieren von Überwachungsprotokollen und Beispielabfragen finden Sie in [diesem Abschnitt](./enable-diagnostic-logging.md). Wenn Sie zusätzliche Informationen in die Protokolle aufnehmen möchten, lesen Sie [Hinzufügen von Daten zu Überwachungsprotokollen mithilfe benutzerdefinierter HTTP-Header](./use-custom-headers.md).

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Wo finde ich einige Beispiele für die Verwendung von Azure API for FHIR in einem Workflow?

Auf der [GitHub-Seite zur Gesundheitsarchitektur (Health Architecture)](https://github.com/microsoft/health-architectures) finden Sie eine Sammlung von Referenzarchitekturen.

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Wo finde ich ein Beispiel für die Verbindungsherstellung zwischen einer Webanwendung und Azure API for FHIR?

Die [GitHub-Seite zur Integritätsarchitektur](https://aka.ms/health-architectures) enthält Beispielanwendungen und -szenarien. Dort sehen Sie, wie Sie eine Verbindung zwischen einer Webanwendung und Azure API for FHIR herstellen.  

## <a name="azure-api-for-fhir-features-and-services"></a>Azure API for FHIR: Features und Dienste 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Gibt es eine Möglichkeit, meine Daten nicht mit einem Standardschlüssel, sondern mit meinem persönlichen Schlüssel zu verschlüsseln?

Ja. Azure API for FHIR ermöglicht das Konfigurieren kundenseitig verwalteter Schlüssel (unterstützt durch Cosmos DB). Weitere Informationen zum Verschlüsseln Ihrer Daten mit einem persönlichen Schlüssel finden Sie in [diesem Abschnitt](./customer-managed-key.md).

## <a name="azure-api-for-fhir-preview-features"></a>Azure API for FHIR: Previewfunktionen

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Kann ich die Skalierungskapazität für Azure IoT Connector for FHIR (Vorschau) konfigurieren?

Da Azure IoT Connector for FHIR während der öffentlichen Vorschau kostenlos ist, ist die Skalierungskapazität festgelegt und begrenzt. Die in der öffentlichen Vorschau verfügbare Azure IoT Connector für FHIR-Konfiguration ermöglicht voraussichtlich einen Durchsatz von etwa 200 Nachrichten pro Sekunde. Die Konfiguration der Ressourcenkapazität wird in irgendeiner Form in der allgemeinen Verfügbarkeit (General Availability, GA) vorhanden sein.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Warum kann ich Azure IoT Connector for FHIR (Vorschau) nicht installieren, wenn Private Link für Azure API for FHIR aktiviert ist?

Azure IoT Connector for FHIR unterstützt derzeit keine Private Link-Funktionalität. Wenn Sie also Private Link für Azure API for FHIR aktiviert haben, können Sie Azure IoT Connector for FHIR nicht installieren und umgekehrt. Diese Einschränkung wird voraussichtlich entfernt, wenn Azure IoT Connector for FHIR für die allgemeine Verfügbarkeit (GA) vorhanden ist.
