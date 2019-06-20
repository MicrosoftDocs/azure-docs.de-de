---
title: Upgraden des Azure Application Insights-Momentaufnahmedebuggers für .NET-Apps | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Momentaufnahmedebugger in Azure App Service oder über NuGet-Pakete auf die neueste Version upgraden.
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60784192"
---
# <a name="upgrading-the-snapshot-debugger"></a>Upgraden des Momentaufnahmedebuggers

Um die bestmögliche Sicherheit für Ihre Daten zu gewährleisten, rückt Microsoft von TLS 1.0 und TLS 1.1 ab, da sich beide Versionen als anfällig für Angriffe erwiesen haben. Sollten Sie eine ältere Version der Websiteerweiterung verwenden, muss diese aktualisiert werden, damit sie weiter funktioniert. In diesem Dokument erfahren Sie, wie Sie den Momentaufnahmedebugger auf die neueste Version upgraden. Es gibt zwei primäre Upgradepfade. Diese sind abhängig davon, ob Sie den Momentaufnahmedebugger mithilfe einer Websiteerweiterung aktiviert oder Ihrer Anwendung ein SDK/NuGet-Paket hinzugefügt haben. Im Anschluss werden beide Upgradepfade beschrieben. 

## <a name="upgrading-the-site-extension"></a>Upgraden der Websiteerweiterung

Wenn Sie den Momentaufnahmedebugger mithilfe der Websiteerweiterung aktiviert haben, können Sie das Upgrade ganz einfach wie folgt durchführen:

1. Melden Sie sich beim Azure-Portal an.
2. Navigieren Sie zu Ihrer Ressource, für die Application Insights und der Momentaufnahmedebugger aktiviert sind. Navigieren Sie also beispielsweise bei einer Web-App zur App Service-Ressource:

   ![Screenshot: App Service-Ressource namens „DiagService01“](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Klicken Sie anschließend auf dem Übersichtsblatt auf „Application Insights“:

   ![Screenshot mit drei Schaltflächen. Mittlere Schaltfläche namens „Application Insights“ ausgewählt.](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Ein neues Blatt mit den aktuellen Einstellungen wird geöffnet. Hier können Sie bei Bedarf Ihre Einstellungen ändern. Die Schaltfläche **Übernehmen** am unteren Rand des Blatts wird erst aktiv, wenn Sie eine der Einstellungen bearbeitet haben. Sie müssen allerdings keine Einstellung ändern, sondern können die Änderung gleich wieder zurücksetzen. Wir empfehlen, die Profiler-Einstellung zu bearbeiten und anschließend **Übernehmen** auszuwählen.

   ![Screenshot: App Service-Konfigurationsseite von Application Insights mit rot hervorgehobener Schaltfläche „Übernehmen“](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Nach dem Klicken auf **Übernehmen** werden Sie aufgefordert, die Änderungen zu bestätigen.

    > [!NOTE]
    > Die Website wird im Rahmen des Upgradeprozesses neu gestartet.

   ![Screenshot: App Service-Aufforderung zur Bestätigung der Übernahme der Überwachungseinstellungen. Das Textfeld enthält folgende Meldung: „Wir werden jetzt Änderungen an Ihren App-Einstellungen vornehmen und unsere Tools installieren, um Ihre Application Insights-Ressource mit der Web-App zu verknüpfen. Dies führt zu einem Neustart der Website. Möchten Sie fortfahren?“](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Klicken Sie auf **Ja**, um die Änderungen zu übernehmen. Während des Prozesses erscheint eine Benachrichtigung, die zeigt, dass die Änderungen übernommen werden:

   ![Screenshot: Übernahme der Änderungen. Meldung „Erweiterungen werden aktualisiert...“ in der rechten oberen Ecke](./media/snapshot-debugger-upgrade/updating-extensions.png)

Nach Abschluss des Vorgangs wird eine Benachrichtigung mit dem Hinweis angezeigt, dass**die Änderungen übernommen wurden**.

   ![Screenshot: Meldung mit dem Hinweis, dass die Änderungen angewendet wurden](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Die Website ist nun aktualisiert und verwendungsbereit.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Upgraden des Momentaufnahmedebuggers per SDK/NuGet

Falls die Anwendung nicht mindestens die Version 1.3.1 von `Microsoft.ApplicationInsights.SnapshotCollector` verwendet, ist ein Upgrade auf eine [neuere Version](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) erforderlich, damit sie weiter funktioniert.
