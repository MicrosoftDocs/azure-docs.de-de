---
title: Erstellen einer neuen Azure Application Insights-Ressource | Microsoft Docs
description: Richten Sie manuell die Application Insights-Überwachung für eine neue Liveanwendung ein.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 712004a1ae8a2a72854b7b2332449a019c0820c3
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256246"
---
# <a name="create-an-application-insights-resource"></a>Erstellen einer Application Insights-Ressource
Azure Application Insights zeigt Daten über Ihre Anwendung in einer Microsoft Azure-*Ressource* an. Die Erstellung einer neuen Ressource gehört daher zur [Einrichtung von Application Insights für das Überwachen einer neuen Anwendung][start]. In vielen Fällen kann eine Ressource automatisch von der IDE erstellt werden. In einigen Fällen erstellen Sie eine Ressource jedoch manuell – z.B. um separate Ressourcen für die Entwicklungs- und Produktionsbuilds der Anwendung verwenden zu können.

Nach dem Erstellen der Ressource erhalten Sie den Instrumentationsschlüssel und verwenden ihn zum Konfigurieren des SDK in der Anwendung. Der Ressourcenschlüssel verknüpft die Telemetrie mit der Ressource.

## <a name="sign-up-to-microsoft-azure"></a>Anmelden bei Microsoft Azure
Wenn Sie noch kein [Microsoft-Konto besitzen, erstellen Sie jetzt eins](https://live.com). (Wenn Sie Dienste wie Outlook.com, OneDrive, Windows Phone oder XBox Live verwenden, besitzen Sie bereits ein Microsoft-Konto.)

Außerdem benötigen Sie ein Abonnement für [Microsoft Azure](https://azure.com). Wenn Ihr Team oder Ihre Organisation über ein Azure-Abonnement verfügt, kann der Besitzer Sie anhand Ihrer Windows Live ID hinzufügen. Ihnen wird nur die tatsächliche Verwendung in Rechnung gestellt. Darüber hinaus erlaubt der Basic-Standardplan die kostenlose Nutzung zu Testzwecken bis zu einem bestimmten Volumen.

Wenn Sie Zugriff auf ein Abonnement haben, melden Sie sich mit Ihrer Live ID unter [https://portal.azure.com](https://portal.azure.com) bei Application Insights an.

## <a name="create-an-application-insights-resource"></a>Erstellen einer Application Insights-Ressource
Fügen Sie unter [portal.azure.com](https://portal.azure.com)eine neue Application Insights-Ressource hinzu:

![Klicken Sie auf "Neu > Application Insights"](./media/create-new-resource/01-new.png)

* **Anwendungstyp** bestimmt den Inhalt des Blatts „Übersicht“ und die im [Metrik-Explorer][metrics] verfügbaren Eigenschaften. Wird Ihr App-Typ nicht angezeigt, wählen Sie „Allgemein“.
* **Abonnement** ist Ihr Zahlungskonto in Azure.
* **Ressourcengruppe** ist eine benutzerfreundliche Möglichkeit zum Verwalten von Eigenschaften wie der Zugriffssteuerung. Wenn Sie bereits andere Azure-Ressourcen erstellt haben, können Sie diese neue Ressource in derselben Gruppe platzieren.
* **Speicherort** ist der Ort, an dem Ihre Daten aufbewahrt werden.
* **An Dashboard anheften** legt eine Schnellzugriffskachel für die Ressource auf Ihrer Azure-Startseite ab. Empfohlen.

Wenn Ihre App erstellt wurde, wird ein neues Blatt geöffnet. Auf diesem Blatt werden die Leistungs- und Nutzungsdaten für Ihre App angezeigt. 

Um bei der nächsten Anmeldung bei Azure wieder dorthin zu gelangen, suchen Sie im Startmenü (bzw. auf der Startseite) nach der Schnellstartkachel für Ihre App. Klicken Sie alternativ dazu auf "Durchsuchen", um sie zu finden.

## <a name="copy-the-instrumentation-key"></a>Kopieren des Instrumentationsschlüssels
Der Instrumentierungsschlüssel identifiziert die Ressource, die Sie erstellt haben. Er muss an das SDK übergeben werden.

![Klicken Sie auf "Essentials", klicken Sie auf den Instrumentierungsschlüssel, STRG+C.](./media/create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Installieren des SDK in Ihrer App
Installieren Sie das Application Insights-SDK in Ihrer App. Dieser Schritt hängt stark von der Art der Anwendung ab. 

Konfigurieren Sie das [SDK, das Sie in Ihrer Anwendung installieren][start] mithilfe des Instrumentierungsschlüssels.

Das SDK enthält die Standardmodule, die Telemetriedaten senden, ohne dass Sie Code schreiben müssen. Um Benutzeraktionen nachzuverfolgen oder Probleme im Detail zu diagnostizieren, [verwenden Sie die API][api] zum Senden eigener Telemetriedaten.

## <a name="monitor"></a>Anzeigen von Telemetriedaten
Schließen Sie das Schnellstartblatt, um zum Blatt Ihrer Anwendung im Azure-Portal zurückzukehren.

Klicken Sie auf die Suchkachel, um die [Diagnosesuche][diagnostic] zu öffnen. Dort werden die ersten Ereignisse angezeigt. 

Klicken Sie nach einigen Sekunden auf **Aktualisieren**, falls Sie mehr Daten erwarten.

## <a name="creating-a-resource-automatically"></a>Automatisches Erstellen einer Ressource
Sie können ein [PowerShell-Skript](../../azure-monitor/app/powershell.md) schreiben, um eine Ressource automatisch zu erstellen.

## <a name="next-steps"></a>Nächste Schritte
* [Diagnosesuche](../../azure-monitor/app/diagnostic-search.md)
* [Untersuchen von Metriken](../../azure-monitor/app/metrics-explorer.md)
* [Schreiben von Analytics-Abfragen](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md

