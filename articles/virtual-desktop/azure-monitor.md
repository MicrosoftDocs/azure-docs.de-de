---
title: Verwenden von Azure Monitor für Windows Virtual Desktop (Vorschau)
description: Erfahren Sie mehr über der Verwendung von Azure Monitor für Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e0be6decf28fcbb2edacd5019f567d26403b1f31
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465712"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Verwenden von Azure Monitor für Windows Virtual Desktop zum Überwachen der Bereitstellung (Vorschau)

>[!IMPORTANT]
>Azure Monitor für Windows Virtual Desktop befindet sich derzeit in der öffentlichen Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor für Windows Virtual Desktop (Vorschau) ist ein Dashboard, das auf Azure Monitor-Arbeitsmappen basiert und IT-Experten Informationen zu ihren Windows Virtual Desktop-Umgebungen bereitstellt. In diesem Thema wird erläutert, wie Sie Azure Monitor für Windows Virtual Desktop zur Überwachung Ihrer Windows Virtual Desktop-Umgebungen einrichten.

## <a name="requirements"></a>Anforderungen

Bevor Sie Azure Monitor für Windows Virtual Desktop verwenden, müssen folgende Einrichtungsaufgaben erfüllt sein:

- Alle überwachten Windows Virtual Desktop-Umgebungen müssen auf der neuesten Version von Windows Virtual Desktop basieren, die mit Azure Resource Manager kompatibel ist.

- Mindestens ein Log Analytics-Arbeitsbereich muss konfiguriert sein.

- Aktivieren Sie die Datensammlung für die folgenden Elemente in Ihrem Log Analytics-Arbeitsbereich:
    - Alle erforderlichen Leistungsindikatoren
    - Alle Leistungsindikatoren oder Ereignisse, die in Azure Monitor für Windows Virtual Desktop verwendet werden
    - Daten aus dem Diagnosetool für alle Objekte in der zu überwachenden Umgebung
    - Virtuelle Computer (VMs) in der zu überwachenden Umgebung

Jeder Benutzer, der Azure Monitor für Windows Virtual Desktop für Ihre Umgebung überwacht, benötigt außerdem die folgenden Lesezugriffsberechtigungen:

- Lesezugriff auf die Ressourcengruppe, in der sich die Ressourcen der Umgebung befinden

- Lesezugriff auf die Ressourcengruppen, in denen sich die Sitzungshosts der Umgebung befinden

>[!NOTE]
> Der Lesezugriff ermöglicht Administratoren nur das Anzeigen von Daten. Zum Verwalten von Ressourcen im Windows Virtual Desktop-Portal sind andere Berechtigungen erforderlich.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Öffnen von Azure Monitor für Windows Virtual Desktop

Sie können Azure Monitor für Windows Virtual Desktop mit einer der folgenden Methoden öffnen:

- Wechseln Sie zu [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).

- Suchen Sie im Azure-Portal nach **Windows Virtual Desktop**, und wählen Sie diese Option aus. Dann wählen Sie **Insights** aus.

- Suchen Sie im Azure-Portal nach **Azure Monitor**, und wählen Sie diese Option. Wählen Sie unter **Insights** die Option **Insights-Hub** und dann unter **Andere** die Option **Windows Virtual Desktop** aus, um das Dashboard auf der Azure Monitor-Seite zu öffnen.

Nachdem Sie Azure Monitor für Windows Virtual Desktop geöffnet haben, aktivieren Sie je nach zu überwachender Umgebung mindestens eines der Kontrollkästchen mit den Bezeichnungen **Abonnement**, **Ressourcengruppe**, **Hostpool** und **Zeitbereich**.

>[!NOTE]
>Windows Virtual Desktop unterstützt derzeit nur das Überwachen eines Abonnements, einer Ressourcengruppe und eines Hostpools. Wenn Sie die zu überwachende Umgebung nicht finden können, suchen Sie in der [Dokumentation zur Problembehandlung](troubleshoot-azure-monitor.md) nach bekannten Funktionsanforderungen und Problemen.

## <a name="set-up-with-the-configuration-workbook"></a>Einrichten mit der Konfigurationsarbeitsmappe

Wenn Sie Azure Monitor für Windows Virtual Desktop zum ersten Mal öffnen, müssen Sie Azure Monitor für Ihre Windows Virtual Desktop-Ressourcen konfigurieren. So konfigurieren Sie die Ressourcen:

1. Öffnen Sie Ihre Arbeitsmappe im Azure-Portal.
2. Wählen Sie **Konfigurationsarbeitsmappe öffnen** aus.

Die Konfigurationsarbeitsmappe richtet Ihre Überwachungsumgebung ein und ermöglicht Ihnen eine Überprüfung der Konfiguration, nachdem Sie den Einrichtungsvorgang abgeschlossen haben. Es ist wichtig, die Konfiguration zu überprüfen, falls Elemente im Dashboard nicht ordnungsgemäß angezeigt werden oder wenn die Produktgruppe Updates veröffentlicht, die zusätzliche Datenpunkte erfordern.

## <a name="host-pool-diagnostic-settings"></a>Diagnoseeinstellungen für den Hostpool

Sie müssen Azure Monitor-Diagnoseeinstellungen für alle Objekte in der Windows Virtual Desktop-Umgebung aktivieren, die diese Funktion unterstützen.

1. Öffnen Sie Azure Monitor für Windows Virtual Desktop unter [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), und wählen Sie dann **Konfigurationsarbeitsmappe** aus.

2. Wählen Sie eine zu überwachende Umgebung unter **Abonnement**, **Ressourcengruppen** und **Hostpool** aus.

3. Prüfen Sie unter **Diagnoseeinstellungen für den Hostpool**, ob die Windows Virtual Desktop-Diagnose für den Hostpool aktiviert ist. Wenn dies nicht der Fall ist, wird eine Fehlermeldung angezeigt, die besagt, dass für den ausgewählten Hostpool keine vorhandene Diagnosekonfiguration gefunden wurde. 
   
   Die folgenden Tabellen sollten aktiviert werden:

    - Prüfpunkt
    - Fehler
    - Verwaltung
    - Verbindung
    - HostRegistration

    >[!NOTE]
    > Wenn die Fehlermeldung nicht angezeigt wird, müssen Sie Schritt 4 nicht ausführen.

4. Wählen Sie **Hostpool konfigurieren** aus.

5. Klicken Sie auf **Bereitstellen**.

6. Aktualisieren der Arbeitsmappe

Weitere Informationen zum Aktivieren der Diagnose für alle Objekte in der Windows Virtual Desktop-Umgebung oder zum Zugreifen auf den Log Analytics-Arbeitsbereich finden Sie unter [Senden der Windows Virtual Desktop-Diagnose an Log Analytics](diagnostics-log-analytics.md).

## <a name="configure-log-analytics"></a>Konfigurieren von Log Analytics

Um mit der Verwendung von Azure Monitor für Windows Virtual Desktop zu beginnen, benötigen Sie außerdem mindestens einen Log Analytics-Arbeitsbereich, um Daten aus der zu überwachenden Umgebung zu sammeln und für die Arbeitsmappe bereitzustellen. Wenn bereits ein Arbeitsbereich eingerichtet ist, fahren Sie mit [Einrichten von Leistungsindikatoren](#set-up-performance-counters) fort. Informationen zum Einrichten eines neuen Log Analytics-Arbeitsbereichs für das Azure-Abonnement, das Ihre Windows Virtual Desktop-Umgebung enthält, finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md).

>[!NOTE]
>Es gelten die Standardgebühren für Datenspeicher für Log Analytics. Für den Einstieg empfiehlt es sich, das Modell mit nutzungsbasierter Bezahlung auszuwählen und dann beim Skalieren der Bereitstellung und der Aufnahme von mehr Daten anzupassen. Weitere Informationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="set-up-performance-counters"></a>Einrichten von Leistungsindikatoren

Sie müssen bestimmte Leistungsindikatoren für die Sammlung im entsprechenden Stichprobenintervall im Log Analytics-Arbeitsbereich aktivieren. Diese Leistungsindikatoren sind die einzigen Indikatoren, die Sie zum Überwachen von Windows Virtual Desktop benötigen. Sie können alle anderen deaktivieren, um Kosten zu sparen.

Wenn bereits Leistungsindikatoren aktiviert sind und Sie diese entfernen möchten, folgen Sie den Anweisungen unter [Konfigurieren von Leistungsindikatoren](../azure-monitor/platform/data-sources-performance-counters.md), um die Leistungsindikatoren neu zu konfigurieren. Im Artikel wird zwar beschrieben, wie Leistungsindikatoren hinzugefügt werden, doch können Sie diese am gleichen Ort auch entfernen.

Wenn Sie noch keine Leistungsindikatoren eingerichtet haben, ist nachfolgend angegeben, wie Sie diese für Azure Monitor für Windows Virtual Desktop konfigurieren:

1. Wechseln Sie zu [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), und wählen Sie dann die **Konfigurationsarbeitsmappe** am unteren Rand des Fensters aus.

2. Wählen Sie unter **Log Analytics-Konfiguration** den Arbeitsbereich aus, den Sie für Ihr Abonnement eingerichtet haben.

3. Unter **Leistungsindikatoren für den Arbeitsbereich** wird die Liste der für die Überwachung erforderlichen Indikatoren angezeigt. Überprüfen Sie rechts neben dieser Liste die Elemente in der Liste **Fehlende Leistungsindikatoren**, um die Indikatoren zu aktivieren, die Sie zum Überwachen Ihres Arbeitsbereichs benötigen.

4. Wählen Sie **Leistungsindikatoren konfigurieren** aus.

5. Wählen Sie **Konfiguration anwenden** aus.

6. Aktualisieren Sie die Konfigurationsarbeitsmappe, und fahren Sie mit dem Einrichten Ihrer Umgebung fort.

Sie können nach der Erstkonfiguration auch neue Leistungsindikatoren hinzufügen, wenn der Dienst aktualisiert wird und neue Überwachungstools erfordert. Sie können überprüfen, ob alle erforderlichen Indikatoren aktiviert sind, indem Sie diese in der Liste **Fehlende Leistungsindikatoren** auswählen.

>[!NOTE]
>Leistungsindikatoren für die Eingabeverzögerung sind nur mit Windows 10 RS5 und höher oder Windows Server 2019 und höher kompatibel.

Weitere Informationen zum manuellen Hinzufügen von Leistungsindikatoren, die noch nicht für die Sammlung aktiviert sind, finden Sie unter [Konfigurieren von Leistungsindikatoren](../azure-monitor/platform/data-sources-performance-counters.md).

### <a name="set-up-windows-events"></a>Einrichten von Windows-Ereignissen

Im nächsten Schritt müssen Sie bestimmte Windows-Ereignisse für die Sammlung im Log Analytics-Arbeitsbereich aktivieren. Die in diesem Abschnitt beschriebenen Ereignisse sind die einzigen, die für Azure Monitor für Windows Virtual Desktop benötigt werden. Sie können alle anderen deaktivieren, um Kosten zu sparen.

So richten Sie Windows-Ereignisse ein:

1. Wenn Sie Windows-Ereignisse bereits aktiviert haben und diese entfernen möchten, entfernen Sie die nicht gewünschten Ereignisse, bevor Sie die Konfigurationsarbeitsmappe zum Aktiveren des für die Überwachung erforderlichen Satzes verwenden.

2. Wechseln Sie zu Azure Monitor für Windows Virtual Desktop unter [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), und wählen Sie dann **Konfigurationsarbeitsmappe** am unteren Rand des Fensters aus.

3. Unter **Konfiguration von Windows-Ereignissen** ist eine Liste der Windows-Ereignisse angegeben, die für die Überwachung erforderlich sind. Rechts neben dieser Liste befindet sich die Liste **Fehlende Ereignisse** mit den erforderlichen Ereignisnamen und Ereignistypen, die für Ihren Arbeitsbereich derzeit nicht aktiviert sind. Notieren Sie sich diese Namen zur späteren Verwendung.

4. Wählen Sie **Arbeitsbereichskonfiguration öffnen** aus.

5. Wählen Sie **Daten** aus.

6. Wählen Sie **Windows-Ereignisprotokolle** aus.

7. Fügen Sie die fehlenden Ereignisnamen aus Schritt 3 und den jeweils erforderlichen Ereignistyp hinzu.

8. Aktualisieren Sie die Konfigurationsarbeitsmappe, und fahren Sie mit dem Einrichten Ihrer Umgebung fort.

Sie können nach der Erstkonfiguration neue Windows-Ereignisse hinzufügen, wenn Aktualisierungen des Überwachungstools das Aktivieren neuer Ereignisse erforderlich machen. Um sicherzustellen, dass alle erforderlichen Ereignisse aktiviert sind, kehren Sie zur Liste **Fehlende Ereignisse** zurück, und aktivieren Sie alle fehlenden Ereignisse, die dort angezeigt werden.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>Installieren des Log Analytics-Agents auf allen Hosts

Im letzten Schritt müssen Sie den Log Analytics-Agent auf allen Hosts im Hostpool installieren, um Daten von den Hosts an den ausgewählten Arbeitsbereich zu senden.

So installieren Sie den Log Analytics-Agent:

1. Wechseln Sie zu Azure Monitor für Windows Virtual Desktop unter [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), und wählen Sie dann **Konfigurationsarbeitsmappe** am unteren Rand des Fensters aus.

2. Wenn Log Analytics nicht für alle Hosts im Hostpool konfiguriert ist, wird am unteren Rand des Abschnitts zur Log Analytics-Konfiguration ein Fehler mit der Meldung angezeigt, dass einige Hosts im Hostpool keine Daten an den ausgewählten Log Analytics-Arbeitsbereich senden. Wählen Sie **Hosts dem Arbeitsbereich hinzufügen** aus, um die ausgewählten Hosts hinzuzufügen. Wenn die Fehlermeldung nicht angezeigt wird, beenden Sie den Vorgang hier.

3. Aktualisieren Sie die Konfigurationsarbeitsmappe.

>[!NOTE]
>Der Hostcomputer muss aktiv sein, um die Log Analytics-Erweiterung zu installieren. Wenn bei der automatischen Bereitstellung auf einem Host ein Fehler auftritt, können Sie die Erweiterung immer auch manuell auf einem Host installieren. Informationen zum manuellen Installieren der Erweiterung finden Sie unter [Log Analytics-VM-Erweiterung für Windows](../virtual-machines/extensions/oms-windows.md).

## <a name="optional-configure-alerts"></a>Optional: Konfigurieren von Warnungen

Sie können Azure Monitor für Windows Virtual Desktop so konfigurieren, dass Sie benachrichtigt werden, wenn in Ihrem ausgewählten Abonnement schwerwiegende Azure Monitor-Warnungen auftreten. Befolgen Sie hierzu die Anweisungen unter [Reagieren auf Ereignisse mit Azure Monitor-Warnungen](../azure-monitor/learn/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Diagnose- und Nutzungsdaten

Wenn Sie den Azure Monitor-Dienst verwenden, sammelt Microsoft automatisch Nutzungs- und Leistungsdaten. Microsoft verwendet diese Daten, um die Qualität, Sicherheit und Integrität des Diensts zu verbessern.

Um genaue und effiziente Funktionen zur Problembehandlung bereitzustellen, enthalten die gesammelten Daten die Portalsitzungs-ID, die Azure Active Directory-Benutzer-ID und den Namen der Portalregisterkarte, auf der das Ereignis aufgetreten ist. Microsoft erfasst weder Namen noch Adressen oder andere Kontaktinformationen.

Weitere Informationen zur Sammlung und Nutzung von Daten finden Sie in den [Datenschutzbestimmungen für Onlinedienste von Microsoft](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Informationen zum Anzeigen oder Löschen Ihrer personenbezogenen Daten, die vom Dienst gesammelt werden, finden Sie unter [Anträge betroffener Personen für Azure im Rahmen der DSGVO](/microsoft-365/compliance/gdpr-dsr-azure). Allgemeine Informationen zur DSGVO finden Sie im [Abschnitt über DSGVO für Service Trust Portal](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun Ihr Azure-Portal für Windows Virtual Desktop konfiguriert haben, sind hier einige Ressourcen angegeben, die Ihnen helfen können:

- Sehen Sie sich unser [Glossar](azure-monitor-glossary.md) an, um mehr über Begriffe und Konzepte im Zusammenhang mit Azure Monitor für Windows Virtual Desktop zu erfahren.
- Wenn ein Problem auftritt, finden Sie weitere Informationen im [Leitfaden zur Problembehandlung](troubleshoot-azure-monitor.md).