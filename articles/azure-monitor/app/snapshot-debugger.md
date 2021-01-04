---
title: Azure Application Insights-Momentaufnahmedebugger für .NET-Apps
description: Debugmomentaufnahmen werden automatisch beim Auslösen von Ausnahmen in .NET-Produktions-Apps erfasst.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: cbbec71ff8ab762ea0da764a7db9459123850271
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95530232"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Debugmomentaufnahmen von Ausnahmen in .NET-Apps
Wenn eine Ausnahme auftritt, können Sie automatisch eine Debugmomentaufnahme von Ihrer aktiven Webanwendung erfassen. Die Momentaufnahme zeigt den Status des Quellcodes und der Variablen in dem Moment, in dem die Ausnahme ausgelöst wurde. Der Momentaufnahmedebugger in [Azure Application Insights](./app-insights-overview.md) überwacht Ausnahmetelemetriedaten aus Ihrer Web-App. Er erfasst Momentaufnahmen Ihrer am häufigsten ausgelösten Ausnahmen, damit Sie die erforderlichen Informationen zur Diagnose von Problemen in der Produktion erhalten. Binden Sie das [NuGet-Paket des Momentaufnahmesammlers](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in Ihre Anwendung ein, und konfigurieren Sie optional Parameter für die Datensammlung in [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Momentaufnahmen finden Sie im Application Insights-Portal unter [Ausnahmen](./asp-net-exceptions.md).

Sie können Debugmomentaufnahmen im Portal anzeigen, um die Aufrufliste anzuzeigen und die Variablen in jedem Aufruflistenrahmen zu überprüfen. Öffnen Sie zum Verbessern Ihrer Debugleistung mit Quellcode die Momentaufnahmen mit Visual Studio 2019 Enterprise. In Visual Studio können Sie auch [Andockpunkte festlegen, um interaktiv Momentaufnahmen zu erstellen](/visualstudio/debugger/debug-live-azure-applications), ohne auf eine Ausnahme zu warten.

Debugmomentaufnahmen werden 15 Tage lang gespeichert. Diese Aufbewahrungsrichtlinie wird für jede Anwendung separat festgelegt. Wenn Sie diesen Wert erhöhen möchten, können Sie eine Erhöhung anfordern, indem Sie einen Supportfall im Azure-Portal eröffnen.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Aktivieren des Application Insights-Momentaufnahmedebuggers für Ihre Anwendung
Die Momentaufnahmesammlung ist für folgende Anwendungen verfügbar:
* .NET Framework- und ASP.NET-Anwendungen, die mit .NET Framework 4.5 oder höher ausgeführt werden
* .NET Core 2.0- und ASP.NET Core 2.0-Anwendungen, die unter Windows ausgeführt werden

Die folgenden Umgebungen werden unterstützt:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) mit Betriebssystemfamilie 4 oder höher
* [Azure Service Fabric-Dienste](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) unter Windows Server 2012 R2 oder höher
* [Azure Virtual Machines und VM-Skalierungsgruppen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) unter Windows Server 2012 R2 oder höher
* [Lokale virtuelle oder physische Computer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) unter Windows Server 2012 R2 oder höher oder Windows 8.1 oder höher

> [!NOTE]
> Clientanwendungen (z.B. WPF, Windows Forms oder UWP) werden nicht unterstützt.

Wenn Sie den Momentaufnahmedebugger aktiviert haben, aber keine Momentaufnahmen angezeigt werden, lesen Sie unseren [Leitfaden zur Problembehandlung](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Der Zugriff auf Momentaufnahmen wird durch die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) geschützt. Um eine Momentaufnahme zu überprüfen, muss Ihnen zuerst die erforderliche Rolle durch einen Abonnementbesitzer zugewiesen werden.

> [!NOTE]
> Weder Besitzer noch Mitwirkende erhalten diese Rolle automatisch. Wenn sie Momentaufnahmen anzeigen möchten, müssen sie sich die Rolle selbst zuweisen.

Abonnementbesitzer sollten Benutzern, die Momentaufnahmen untersuchen, die Rolle `Application Insights Snapshot Debugger` zuweisen. Abonnementbesitzer können diese Rolle einzelnen Benutzern oder Gruppen für die Application Insights-Zielressource oder für die dazugehörige Ressourcengruppe oder das dazugehörige Abonnement zuweisen.

1. Navigieren Sie im Azure-Portal zu der Application Insights-Ressource.
1. Klicken Sie auf **Zugriffssteuerung (IAM)** .
1. Klicken Sie auf die Schaltfläche **Rollenzuweisung hinzufügen**.
1. Wählen Sie in der Dropdownliste **Rollen** die Option **Application Insights-Momentaufnahmedebugger** aus.
1. Suchen Sie nach dem hinzuzufügenden Benutzer, und geben Sie einen Namen für ihn ein.
1. Klicken Sie auf die Schaltfläche **Speichern**, um den Benutzer der Rolle hinzuzufügen.


> [!IMPORTANT]
> In Variablen- und Parameterwerten von Momentaufnahmen können persönliche und andere vertrauliche Informationen enthalten sein.

## <a name="view-snapshots-in-the-portal"></a>Anzeigen von Momentaufnahmen im Portal

Nachdem in Ihrer Anwendung eine Ausnahme aufgetreten ist und eine Momentaufnahme erstellt wurde, sollten Momentaufnahmen zum Anzeigen vorhanden sein. Nach einer Ausnahme kann es 5 bis 10 Minuten dauern, bis eine Momentaufnahme abgeschlossen ist und im Portal angezeigt werden kann. Wählen Sie zum Anzeigen von Momentaufnahmen im Bereich **Fehler** die Schaltfläche **Vorgänge** auf der Registerkarte **Vorgänge** bzw. die Schaltfläche **Ausnahmen** auf der Registerkarte **Ausnahmen** aus:

![Seite „Fehler“](./media/snapshot-debugger/failures-page.png)

Wählen Sie einen Vorgang oder eine Ausnahme im rechten Bereich aus, um den Bereich **End-to-End-Transaktionsdetails** zu öffnen, und wählen Sie dann das Ausnahmeereignis aus. Wenn eine Momentaufnahme für die entsprechende Ausnahme verfügbar ist, wird im rechten Bereich die Schaltfläche **Debugmomentaufnahme öffnen** mit Details für die [Ausnahme](./asp-net-exceptions.md) angezeigt.

![Schaltfläche zum Erstellen einer Debug-Momentaufnahme für eine Ausnahme](./media/snapshot-debugger/e2e-transaction-page.png)

In der Ansicht der Debug-Momentaufnahme sehen Sie eine Aufrufliste und einen Variablenbereich. Wenn Sie Frames der Aufrufliste im Aufruflistenbereich auswählen, können Sie lokale Variablen und Parameter für diesen Funktionsaufruf im Variablenbereich anzeigen.

![Anzeigen der Debug-Momentaufnahme im Portal](./media/snapshot-debugger/open-snapshot-portal.png)

Momentaufnahmen enthalten möglicherweise vertrauliche Informationen und können standardmäßig nicht angezeigt werden. Zum Anzeigen von Momentaufnahmen muss Ihnen die Rolle `Application Insights Snapshot Debugger` zugewiesen sein.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Anzeigen von Momentaufnahmen in Visual Studio 2017 Enterprise oder höher
1. Klicken Sie auf die Schaltfläche **Momentaufnahme herunterladen**, um eine Datei vom Typ `.diagsession` herunterzuladen, die von Visual Studio Enterprise geöffnet werden kann.

2. Um die Datei `.diagsession` öffnen zu können, muss der Visual Studio-Momentaufnahmedebugger installiert sein. Der Momentaufnahmedebugger ist eine erforderliche Komponente der ASP.net-Workload in Visual Studio und kann im Visual Studio-Installer in der Liste mit den Einzelkomponenten ausgewählt werden. Bei Visual Studio-Versionen vor Visual Studio 2017 Version 15.5 muss die Erweiterung über den [Visual Studio Marketplace](https://aka.ms/snapshotdebugger) installiert werden.

3. Nach dem Öffnen der Momentaufnahmedatei erscheint in Visual Studio die Minidump-Debugging-Seite. Klicken Sie auf **Debug Managed Code** (Verwalteten Code debuggen), um mit dem Debuggen der Momentaufnahme zu beginnen. Die Momentaufnahme wird bei der Codezeile geöffnet, in der die Ausnahme ausgelöst wurde, damit Sie den aktuellen Zustand des Prozesses debuggen können.

    ![Anzeigen der Debugmomentaufnahme in Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Die heruntergeladene Momentaufnahme enthält alle Symboldateien, die auf Ihrem Webanwendungsserver gefunden wurden. Diese Symboldateien sind zum Zuordnen von Quellcode und Momentaufnahmedaten erforderlich. Achten Sie bei App Service-Apps darauf, dass die Symbolbereitstellung aktiviert ist, wenn Sie Ihre Web-Apps veröffentlichen.

## <a name="how-snapshots-work"></a>Funktionsweise von Momentaufnahmen

Der Snapshot Collector wird als [Application Insights-Telemetrieprozessor](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet) implementiert. Wenn die Anwendung ausgeführt wird, wird der Snapshot Collector-Telemetrieprozessor der Telemetriepipeline Ihrer Anwendung hinzugefügt.
Bei jedem Aufruf von [TrackException](./asp-net-exceptions.md#exceptions) durch Ihre Anwendung berechnet der Snapshot Collector auf der Grundlage der Art der ausgelösten Ausnahme und der auslösenden Methode eine Problem-ID.
Bei jedem Aufruf von „TrackException“ durch Ihre Anwendung erhöht sich der Zähler für die entsprechende Problem-ID. Wenn der Zähler den Wert `ThresholdForSnapshotting` erreicht, wird die Problem-ID einem Sammlungsplan hinzugefügt.

Der Snapshot Collector abonniert auch das Ereignis [AppDomain.CurrentDomain.FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception), um ausgelöste Ausnahmen zu überwachen. Wenn dieses Ereignis ausgelöst wird, wird die Problem-ID der Ausnahme berechnet und mit den Problem-IDs im Sammlungsplan verglichen.
Ist eine Entsprechung vorhanden, wird eine Momentaufnahme des ausgeführten Prozesses erstellt. Der Momentaufnahme wird ein eindeutiger Bezeichner zugewiesen, und die Ausnahme wird mit diesem Bezeichner gekennzeichnet. Nach Ausführung des Handlers „FirstChanceException“ wird die ausgelöste Ausnahme ganz normal verarbeitet. Letztendlich erreicht die Ausnahme wieder die Methode „TrackException“ und wird zusammen mit dem Momentaufnahmebezeichner an Application Insights gemeldet.

Der Hauptprozess wird mit minimaler Unterbrechung weiter ausgeführt und stellt weiter Datenverkehr für Benutzer bereit. In der Zwischenzeit wird die Momentaufnahme an den Snapshot Uploader-Prozess übergeben. Der Snapshot Uploader erstellt einen Minidump mit allen relevanten Symboldateien (PDB-Dateien) und lädt ihn in Application Insights hoch.

> [!TIP]
> - Bei einer Prozessmomentaufnahme handelt es sich um einen angehaltenen Klon des ausgeführten Prozesses.
> - Die Erstellung der Momentaufnahme dauert 10 bis 20 Millisekunden.
> - Der Standardwert für `ThresholdForSnapshotting` ist „1“. Das ist gleichzeitig auch der Mindestwert. Ihre App muss die gleiche Ausnahme also **zweimal** auslösen, bevor eine Momentaufnahme erstellt wird.
> - Legen Sie `IsEnabledInDeveloperMode` auf „true“ fest, wenn Sie beim Debuggen in Visual Studio Momentaufnahmen generieren möchten.
> - Die Rate der Momentaufnahmenerstellung wird durch die Einstellung `SnapshotsPerTenMinutesLimit` begrenzt. Standardmäßig ist das Limit auf eine einzelne Momentaufnahme pro zehn Minuten festgelegt.
> - Pro Tag können maximal 50 Momentaufnahmen hochgeladen werden.

## <a name="limitations"></a>Einschränkungen

Die standardmäßige Datenaufbewahrungsdauer beträgt 15 Tage. Für jede Application Insights-Instanz ist eine maximale Anzahl von 50 Momentaufnahmen pro Tag zulässig.

### <a name="publish-symbols"></a>Veröffentlichen von Symbolen
Für den Momentaufnahmedebugger müssen Symboldateien auf dem Produktionsserver vorhanden sein, um Variablen zu decodieren und eine gute Debugleistung in Visual Studio zu erzielen.
Die Version 15.2 (oder höher) von Visual Studio 2017 veröffentlicht Symbole für Releasebuilds standardmäßig im Rahmen der Veröffentlichung für App Service. In Vorgängerversionen müssen Sie der Veröffentlichungsprofildatei vom Typ `.pubxml` die folgende Zeile hinzufügen, um Symbole im Releasemodus zu veröffentlichen:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Stellen Sie für Azure Compute und andere Typen sicher, dass die Symboldateien im selben Ordner wie die DLL-Datei der Hauptanwendung (in der Regel `wwwroot/bin`) liegen oder unter dem aktuellen Pfad verfügbar sind.

> [!NOTE]
> Weitere Informationen zu den verschiedenen verfügbaren Symboloptionen finden Sie in der [Visual Studio-Dokumentation](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Um optimale Ergebnisse zu erzielen, wird die Verwendung von „full“, „portable“ oder „embedded“ empfohlen.

### <a name="optimized-builds"></a>Optimierte Builds
In einigen Fällen können lokale Variablen aufgrund von Optimierungen, die durch den JIT-Compiler angewendet werden, nicht in Releasebuilds angezeigt werden.
In Azure App Services kann der Snapshot Collector allerdings Auslösemethoden deoptimieren, die Teil des entsprechenden Sammlungsplans sind.

> [!TIP]
> Installieren Sie in Ihrer App Service-Instanz die Application Insights-Websiteerweiterung, um Deoptimierungsunterstützung zu erhalten.

## <a name="next-steps"></a>Nächste Schritte
Aktivieren des Application Insights-Momentaufnahmedebuggers für Ihre Anwendung:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-Dienste](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines und VM-Skalierungsgruppen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokale virtuelle oder physische Computer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Über den Application Insights-Momentaufnahmedebugger hinaus:
 
* [Legen Sie Fangpunkte in Ihrem Code fest](/visualstudio/debugger/debug-live-azure-applications), um Momentaufnahmen abzurufen, ohne auf eine Ausnahme warten zu müssen.
* Unter [Diagnostizieren von Ausnahmen in Ihren Web-Apps](./asp-net-exceptions.md) erfahren Sie, wie Sie weitere Ausnahmen für Application Insights sichtbar machen.
* Die [intelligente Erkennung](./proactive-diagnostics.md) ermittelt automatisch Leistungsanomalien.