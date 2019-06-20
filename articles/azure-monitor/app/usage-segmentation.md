---
title: Benutzer-, Sitzungs- und Ereignisanalyse in Azure Application Insights | Microsoft-Dokumentation
description: Demografische Analyse der Benutzer Ihrer Web-App.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 7d378c2f72035c3584e1f5cd3c1f0fb9a5d5c2ed
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372283"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Benutzer-, Sitzungs- und Ereignisanalyse in Azure Application Insights

Ermitteln Sie, wann Personen Ihre Web-App verwenden, für welche Seiten sie sich am meisten interessieren, wo sich die Benutzer befinden, und welche Browser und Betriebssysteme sie verwenden. Analysieren Sie Geschäfts- und Nutzungstelemetriedaten mithilfe von [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Screenshot der Application Insights-Benutzer](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Erste Schritte

Wenn Sie im Application Insights-Portal auf den Blättern „Benutzer“, „Sitzungen“ und „Ereignissen“ noch keine Daten sehen, [erfahren Sie hier mehr zum Einstieg in die Nutzungstools](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Das Segmentierungstool für Benutzer, Sitzungen und Ereignisse

Drei der Nutzungsblätter verwenden dasselbe Tool zum Aufteilen von Telemetriedaten aus Ihrer Web-App aus drei Perspektiven. Durch Filtern und Aufteilen der Daten gewinnen Sie Einblicke in die relative Nutzung verschiedener Seiten und Funktionen.

* **Benutzertool:** Wie viele Personen haben Ihre App und zugehörige Funktionen verwendet?  Benutzer werden mithilfe von anonymen, in Browsercookies gespeicherten IDs gezählt. Eine einzelne Person, die verschiedene Browser oder Computer benutzt, wird als mehrere Benutzer gezählt.
* **Sitzungstool:** Bei wie vielen Benutzeraktivitätssitzungen wurden bestimmte Seiten und Funktionen der App verwendet? Eine Sitzung wird nach einer halben Stunde der Benutzerinaktivität oder nach 24 Stunden ununterbrochener Nutzung gezählt.
* **Ereignistool:** Wie oft werden bestimmte Seiten und Funktionen der App verwendet? Eine Seitenansicht wird gezählt, wenn eine Seite Ihrer App in einem Browser geladen wird, vorausgesetzt, Sie haben sie [instrumentiert](../../azure-monitor/app/javascript.md). 

    Ein benutzerdefiniertes Ereignis stellt eine Aktion in Ihrer App dar, häufig eine Benutzerinteraktion wie das Klicken auf eine Schaltfläche oder den Abschluss einer Aufgabe. Sie fügen Code in Ihre App ein, um [benutzerdefinierte Ereignisse zu generieren](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Abfragen für bestimmte Benutzer

Untersuchen Sie verschiedene Benutzergruppen, indem Sie die Abfrageoptionen oben im Benutzertools anpassen:

* Anzeigen: Wählen Sie eine Kohorte von Benutzern für die Analyse aus.
* die Folgendes verwendet haben: Wählen Sie benutzerdefinierte Ereignisse und Seitenansichten aus.
* Während: Wählen Sie einen Zeitbereich aus.
* Von: Wählen Sie aus, wie Datenbuckets erstellt werden, entweder anhand einer Zeitspanne oder einer anderen Eigenschaft, z.B. Browser oder Ort.
* Aufteilen nach: Wählen Sie eine Eigenschaft aus, anhand derer die Daten aufgeteilt oder segmentiert werden. 
* Filter hinzufügen: Beschränken Sie die Abfrage auf bestimmte Benutzer, Sitzungen oder Ereignisse basierend auf deren Eigenschaften, z.B. Browser oder Ort. 
 
## <a name="saving-and-sharing-reports"></a>Speichern und Freigeben von Berichten 
Sie können Benutzerberichte entweder privat nur für Sie selbst im Abschnitt „Meine Berichte“ oder im Abschnitt „Freigegebene Berichte“ für alle Benutzer speichern, die Zugriff auf diese Application Insights-Ressource haben.

Wenn Sie einen Link zu einem Benutzer-, Sitzungs oder Ereignisbericht teilen möchten, klicken Sie auf der Symbolleiste auf **Teilen**, und kopieren Sie den Link.

Wenn Sie eine Kopie der Daten eines Benutzer-, Sitzungs- oder Ereignisberichts teilen möchten, klicken Sie auf der Symbolleiste auf **Teilen** und anschließend auf das **Word**-Symbol, um ein Word-Dokument mit den Daten zu erstellen. Oder klicken Sie über dem Hauptdiagramm auf das Symbol für **Word**.

## <a name="meet-your-users"></a>Informationen zu Benutzern

Der Abschnitt **Meet your users** (Informationen zu Benutzern) enthält Informationen zu fünf Beispielbenutzern, die der aktuellen Abfrage entsprechen. Das Berücksichtigen und Untersuchen des Verhaltens einzelner Personen neben Aggregaten kann Erkenntnisse über die tatsächliche Verwendung Ihrer App durch Benutzer bieten.

## <a name="next-steps"></a>Nächste Schritte

- Um mit der Nutzung zu beginnen, senden Sie [benutzerdefinierte Ereignisse](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) oder [Seitenansichten](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Wenn Sie bereits benutzerdefinierte Ereignisse oder Seitenansichten senden, finden Sie mithilfe der Nutzungstools heraus, wie Benutzer den Dienst verwenden.
    - [Trichter](usage-funnels.md)
    - [Aufbewahrung](usage-retention.md)
    - [Benutzerabläufe](usage-flows.md)
    - [Arbeitsmappen](../../azure-monitor/app/usage-workbooks.md)
    - [Hinzufügen von Benutzerkontext](usage-send-user-context.md)