---
title: Azure Security Center-Datensicherheit | Microsoft Docs
description: In diesem Dokument wird erläutert, wie Daten in Azure Security Center verwaltet und geschützt werden.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: cd91b83bc808d811fc50293fbf1726d609ad5b46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65234070"
---
# <a name="azure-security-center-data-security"></a>Azure Security Center-Datensicherheit
Azure Security Center erfasst und verarbeitet sicherheitsbezogene Daten (einschließlich Konfigurationsinformationen, Metadaten, Ereignisprotokolle, Absturzabbilddateien und Ähnliches), um Kunden bei der Vermeidung, Erkennung und Behandlung von Bedrohungen zu unterstützen. Microsoft hält strenge Compliance- und Sicherheitsrichtlinien ein – angefangen bei der Codierung bis hin zum Betreiben von Diensten.

In diesem Artikel wird erläutert, wie Daten in Azure Security Center verwaltet und geschützt werden.

## <a name="data-sources"></a>Datenquellen
Azure Security Center analysiert Daten aus den folgenden Quellen, um über den Sicherheitsstatus zu informieren, Sicherheitslücken zu identifizieren, Gegenmaßnahmen zu empfehlen und aktive Bedrohungen zu erkennen:

- Azure-Dienste: Verwendet Informationen zur Konfiguration von Azure-Diensten, die Sie bereitgestellt haben. Hierzu wird mit dem Ressourcenanbieter des Diensts kommuniziert.
- Netzwerkdatenverkehr: Verwendet Metadatenstichproben des Netzwerkdatenverkehrs aus der Infrastruktur von Microsoft wie etwa Quelle/Ziel, IP/Port, Paketgröße und Netzwerkprotokoll.
- Partnerlösungen: Verwendet Sicherheitswarnungen von integrierten Partnerlösungen (beispielsweise Firewalls und Antischadsoftwarelösungen).
- Ihre virtuellen Computer und Server: Verwendet Konfigurationsinformationen und Informationen zu Sicherheitsereignissen – beispielsweise Windows-Ereignis- und -Überwachungsprotokolle, IIS-Protokolle, Syslog-Nachrichten und Absturzabbilddateien von Ihren virtuellen Computern. Außerdem kann Azure Security Center bei Erstellung einer Warnung eine Momentaufnahme des betroffenen VM-Datenträgers generieren und zu Untersuchungszwecken warnungsbezogene Computerartefakte wie etwa eine Registrierungsdatei aus dem VM-Datenträger extrahieren.


## <a name="data-protection"></a>Datenschutz
**Trennung von Daten**: Daten werden für jede Komponente des Diensts logisch getrennt verwaltet. Sämtliche Daten werden nach Organisation gekennzeichnet. Dieser Kennzeichnung wird während des gesamten Datenlebenszyklus beibehalten und auf jeder Ebene des Diensts erzwungen.

**Datenzugriff**: Zur Bereitstellung von Sicherheitsempfehlungen sowie bei der Untersuchung potenzieller Sicherheitsrisiken greifen Mitarbeiter von Microsoft unter Umständen auf Informationen zu, die von Azure-Diensten erfasst oder analysiert wurden. Hierzu zählen etwa Absturzabbilddateien, Prozesserstellungsereignisse, Momentaufnahmen von VM-Datenträgern und Artefakte. Diese können ggf. Kundendaten oder persönliche Informationen von Ihren virtuellen Computern enthalten. Wir halten uns an die [Microsoft Online Services-Bedingungen und Datenschutzerklärung](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Darin ist festgelegt, dass Microsoft keine Kundendaten oder daraus abgeleiteten Informationen zu Werbezwecken oder anderen kommerziellen Zwecken verwendet. Wir verwenden Kundendaten nur, wenn dies für die Bereitstellung Ihrer Azure-Dienste erforderlich ist. Dies gilt auch für Zwecke, die mit der Bereitstellung dieser Dienste kompatibel sind. Alle Rechte an den Kundendaten verbleiben bei Ihnen.

**Datennutzung**: Microsoft nutzt mandantenübergreifende Muster und Informationen zu Bedrohungen (Threat Intelligence), um die Funktionen für Prävention und Erkennung zu verbessern. Dies erfolgt in Übereinstimmung mit den in unserer [Datenschutzerklärung](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) beschriebenen Datenschutzzusagen.

## <a name="data-location"></a>Speicherort der Daten

**Ihre Arbeitsbereiche**: Ein Arbeitsbereich wird für die folgenden geografischen Räume angegeben, und die von Ihren virtuellen Azure-Computern erfassten Daten, einschließlich von Absturzabbildern und einigen Typen von Warnungsdaten, werden im nächstgelegenen Arbeitsbereich gespeichert.

| Geografischer Raum VM                              | Geografischer Raum Arbeitsbereich |
|-------------------------------------|---------------|
| USA, Brasilien, Südafrika | USA |
| Kanada                              | Kanada        |
| Europa (außer Vereinigtes Königreich)   | Europa        |
| Vereinigtes Königreich                      | Vereinigtes Königreich |
| Asien (außer Indien, Japan, Korea, China)   | Asien-Pazifik  |
| Korea                              | Asien-Pazifik  |
| Indien                               | Indien         |
| Japan                               | Japan         |
| China                               | China         |
| Australien                           | Australien     |


Momentaufnahmen von VM-Datenträgern werden im gleichen Speicherkonto gespeichert wie der VM-Datenträger.

Bei virtuellen Computern und Servern, die in anderen Umgebungen ausgeführt werden, z.B. lokal, können Sie den Arbeitsbereich und die Region für die Speicherung der erfassten Daten angeben.

**Azure Security Center-Speicher**: Informationen zu Sicherheitswarnungen, einschließlich von Partnerwarnungen, werden dem Standort der verknüpften Azure-Ressource entsprechend gespeichert, während Informationen zum Sicherheitsintegritätsstatus sowie Empfehlungen dem Kundenstandort entsprechend in den USA oder in Europa zentral gespeichert werden.
Azure Security Center erfasst kurzlebige Kopien Ihrer Absturzabbilddateien und analysiert sie, um nach Spuren von Exploitversuchen und erfolgreichen Kompromittierungen zu suchen. Azure Security Center führt diese Analyse in dem geografischen Raum durch, in dem sich auch der Arbeitsbereich befindet, und löscht die kurzlebigen Kopien nach Abschluss der Analyse.

Computerartefakte werden zentral in der Region gespeichert, in der sich auch der virtuelle Computer befindet.


## <a name="managing-data-collection-from-virtual-machines"></a>Verwalten der Datensammlung von virtuellen Computern

Wenn Sie Security Center in Azure aktivieren, wird die Datensammlung für alle Ihre Azure-Abonnements aktiviert. Sie können die Datensammlung für Ihre Abonnements auch in Azure Security Center im Abschnitt „Sicherheitsrichtlinie“ aktivieren. Wenn die Datensammlung aktiviert ist, stellt Azure Security Center den Microsoft Monitoring Agent auf allen vorhandenen unterstützten virtuellen Azure-Computern sowie auf allen neuen virtuellen Computern bereit, die erstellt werden.
Der Microsoft Monitoring Agent sucht in ETW-Ablaufverfolgungen (Event Tracing for Windows, [Ereignisablaufverfolgung für Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)) nach verschiedenen sicherheitsbezogenen Konfigurationen und Ereignissen. Außerdem löst das Betriebssystem während der Ausführung des Computers Ereignisprotokollereignisse aus. Beispiele für Daten dieser Art: Betriebssystemtyp und -version, Betriebssystemprotokolle (Windows-Ereignisprotokolle), ausgeführte Prozesse, Computername, IP-Adressen, angemeldeter Benutzer und Mandanten-ID. Der Microsoft Monitoring Agent liest Ereignisprotokolleinträge und ETW-Ablaufverfolgungen und kopiert diese zur Analyse in Ihren Arbeitsbereich bzw. in Ihre Arbeitsbereiche. Darüber hinaus kopiert Microsoft Monitoring Agent Absturzabbilddateien in Ihren Arbeitsbereich und ermöglicht Prozesserstellungsereignisse und Befehlszeilenüberwachung.

Wenn Sie Azure Security Center Free verwenden, können Sie die Datensammlung von virtuellen Computern in der Sicherheitsrichtlinie deaktivieren. Die Datensammlung ist für Abonnements des Standard-Tarifs erforderlich. Momentaufnahmen von VM-Datenträgern sowie die Artefaktsammlung sind auch bei deaktivierter Datensammlung aktiviert.

## <a name="data-consumption"></a>Nutzung der Daten

Kunden können wie nachfolgend gezeigt Security Center-Daten aus verschiedenen Datenströmen nutzen:

* **Azure-Aktivität**: Alle Sicherheitswarnungen, von Security Center genehmigte [Just-In-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)-Anforderungen und alle von [adaptiven Anwendungssteuerungen](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) generierten Warnungen
* **Azure Monitor-Protokolle:** alle Sicherheitswarnungen.


> [!NOTE]
> Sicherheitsempfehlungen können auch über die REST-API genutzt werden. Weitere Informationen finden Sie unter [Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) (Referenz zur REST-API für Sicherheitsressourcenanbieter).

## <a name="see-also"></a>Weitere Informationen
In diesem Dokument haben Sie erfahren, wie Daten in Azure Security Center verwaltet und geschützt werden. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

* [Planungs- und Betriebshandbuch für Azure Security Center:](security-center-planning-and-operations-guide.md) Hier erfahren Sie, wie Sie die Einführung von Azure Security Center planen, und erhalten grundlegende Informationen zu Entwurfsaspekten.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) : Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) : Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md) : Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md) : Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Blogbeiträge zur Azure-Sicherheit und -Compliance.
