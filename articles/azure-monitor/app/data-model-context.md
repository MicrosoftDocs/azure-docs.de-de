---
title: Azure Application Insights-Telemetriedatenmodell – Telemetriekontext | Microsoft-Dokumentation
description: 'Application Insights-Datenmodell: Telemetriekontext'
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 7c1f47c9b88bd68b326b3c8923ba5b81d425c3e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900709"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetriekontext: Application Insights-Datenmodell

Jedem Telemetrieelement können stark typisierte Kontextfelder zugewiesen werden. Jedes Feld ermöglicht ein bestimmtes Überwachungsszenario. Verwenden Sie die Sammlung benutzerdefinierter Eigenschaften zum Speichern benutzerdefinierter oder anwendungsspezifischer Kontextinformationen.


## <a name="application-version"></a>Anwendungsversion

Informationen in den Anwendungskontextfeldern beziehen sich immer auf die Anwendung, die die Telemetriedaten sendet. „Anwendungsversion“ wird verwendet, um Trendänderungen im Anwendungsverhalten und die Korrelation mit den Bereitstellungen zu analysieren.

Max. Länge: 1024


## <a name="client-ip-address"></a>Client-IP-Adresse

Die IP-Adresse des Clientgeräts. IPv4 und IPv6 werden unterstützt. Wenn Telemetriedaten von einem Dienst gesendet werden, bezieht sich der Standortkontext auf den Benutzer, der den Vorgang im Dienst initiiert hat. Application Insights extrahiert die Geolocation-Informationen aus der Client-IP und kürzt sie dann. „Client-IP“ selbst kann daher nicht für identifizierbare Endbenutzerinformationen verwendet werden. 

Max. Länge: 46


## <a name="device-type"></a>Gerätetyp

Dieses Feld wurde ursprünglich verwendet, um den Typ des Geräts anzugeben, das der Endbenutzer der Anwendung nutzt. Nun wird es hauptsächlich verwendet, um zwischen JavaScript-Telemetrie mit dem Gerätetyp „Browser“ und serverseitiger Telemetrie mit dem Gerätetyp „PC“ zu unterscheiden.

Max. Länge: 64


## <a name="operation-id"></a>Vorgangs-ID

Ein eindeutiger Bezeichner des Stammvorgangs. Über diesen Bezeichner können Telemetriedaten für mehrere Komponenten gruppiert werden. Details finden Sie unter [Telemetriekorrelation](../../azure-monitor/app/correlation.md). Die Vorgangs-ID wird von einer Anforderung oder einer Seitenansicht erstellt. Alle anderen Telemetriedaten legen dieses Feld auf den Wert für die enthaltende Anforderung oder Seitenansicht fest. 

Max. Länge: 128


## <a name="parent-operation-id"></a>Übergeordnete Vorgangs-ID

Der eindeutige Bezeichner des direkt übergeordneten Elements des Telemetrieelements. Details finden Sie unter [Telemetriekorrelation](../../azure-monitor/app/correlation.md).

Max. Länge: 128


## <a name="operation-name"></a>Vorgangsname

Der Name (Gruppe) des Vorgangs. Der Vorgangsname wird von einer Anforderung oder einer Seitenansicht erstellt. Alle anderen Telemetrieelemente legen dieses Feld auf den Wert für die enthaltende Anforderung oder Seitenansicht fest. „Vorgangsname“ wird zum Suchen aller Telemetrieelemente für eine Gruppe von Vorgängen verwendet (z.B. „GET Home/Index“). Diese Kontexteigenschaft wird verwendet, um Fragen wie „Welche typischen Ausnahmen werden auf dieser Seite ausgelöst?“ zu beantworten.

Max. Länge: 1024


## <a name="synthetic-source-of-the-operation"></a>Synthetische Quelle des Vorgangs

Name der synthetischen Quelle. Einige Telemetriedaten aus der Anwendung stellen möglicherweise synthetischen Datenverkehr dar. Dabei kann es sich um Webcrawler, die die Website indizieren, Tests der Websiteverfügbarkeit oder Ablaufverfolgungen von Diagnosebibliotheken wie Application Insights SDK handeln.

Max. Länge: 1024


## <a name="session-id"></a>Sitzungs-ID

Die Instanz der Interaktion des Benutzers mit der App. Die Informationen in den Sitzungskontextfeldern beziehen sich immer auf den Endbenutzer. Wenn Telemetriedaten von einem Dienst gesendet werden, bezieht sich der Sitzungskontext auf den Benutzer, der den Vorgang im Dienst initiiert hat.

Max. Länge: 64


## <a name="anonymous-user-id"></a>Anonyme Benutzer-ID

Anonyme Benutzer-ID. Stellt den Endbenutzer der Anwendung dar. Wenn Telemetriedaten von einem Dienst gesendet werden, bezieht sich der Benutzerkontext auf den Benutzer, der den Vorgang im Dienst initiiert hat.

Die [Stichprobenentnahme](../../azure-monitor/app/sampling.md) ist eine der Methoden zum Minimieren der Menge der gesammelten Telemetriedaten. Der Stichprobenalgorithmus versucht, die Stichprobenerstellung innerhalb oder außerhalb aller korrelierten Telemetriedaten durchzuführen. „Anonyme Benutzer-ID“ wird zur Generierung der Stichprobenbewertung verwendet. Daher muss die anonyme Benutzer-ID einen ausreichend zufälligen Wert aufweisen. 

Die Verwendung von „Anonyme Benutzer-ID“ zum Speichern von Benutzernamen stellt eine unsachgemäße Verwendung des Felds dar. Verwenden Sie stattdessen „Authentifizierte Benutzer-ID“.

Max. Länge: 128


## <a name="authenticated-user-id"></a>Authentifizierte Benutzer-ID

Authentifizierte Benutzer-ID. Als Gegenstück von „Anonyme Benutzer-ID“ stellt dieses Feld den Benutzer mit einem Anzeigenamen dar. Da es sich um personenbezogene Informationen handelt, werden diese von den meisten SDKs standardmäßig nicht erfasst.

Max. Länge: 1024


## <a name="account-id"></a>Konto-ID

In mehrinstanzenfähigen Anwendungen ist dies die Konto-ID oder der Name, unter denen der Benutzer agiert. Beispiele sind die Abonnement-ID für das Azure-Portal oder der Blogname für eine Blogplattform.

Max. Länge: 1024


## <a name="cloud-role"></a>Cloudrolle

Name der Rolle, der die Anwendung zugewiesen ist. Dieser wird direkt dem Rollennamen in Azure zugeordnet. Er kann auch zur Unterscheidung von Microservices verwendet werden, die Teil einer einzelnen Anwendung sind.

Max. Länge: 256


## <a name="cloud-role-instance"></a>Cloudrolleninstanz

Name der Instanz, in der die Anwendung ausgeführt wird. Name des Computers für lokalen Instanzennamen für Azure.

Max. Länge: 256


## <a name="internal-sdk-version"></a>Intern: SDK-Version

SDK-Version. Weitere Informationen finden Sie unter https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification.

Max. Länge: 64


## <a name="internal-node-name"></a>Intern: Knotenname

Dieses Feld gibt den Namen des zu Abrechnungszwecken verwendeten Knotens an. Verwenden Sie es, um die Standarderkennung von Knoten zu überschreiben.

Max. Länge: 256


## <a name="next-steps"></a>Nächste Schritte

- Informationen zum [Erweitern und Filtern von Telemetriedaten](../../azure-monitor/app/api-filtering-sampling.md).
- Lesen Sie die Informationen zu den Application Insights-Typen und zum Datenmodell unter [Datenmodell](data-model.md).
- Informationen zur [Konfiguration](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) der Sammlung von Standardkontexteigenschaften.
