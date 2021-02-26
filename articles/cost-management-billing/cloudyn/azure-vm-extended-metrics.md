---
title: Hinzufügen erweiterter Metriken für virtuelle Azure-Computer
description: Dieser Artikel unterstützt Sie dabei, erweiterte Diagnosemetriken für Ihre Azure-VMs zu aktivieren und zu konfigurieren.
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: da2e20e333fe499998fff72b175442650f5f1e28
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595303"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Hinzufügen erweiterter Metriken für virtuelle Azure-Computer

Cloudyn verwendet Azure-Metrikdaten aus Ihren Azure-VMs, um ausführliche Informationen zu den zugehörigen Ressourcen anzuzeigen. Metrikdaten – auch Leistungsindikatoren genannt – werden in Cloudyn zum Generieren von Berichten verwendet. In Cloudyn werden jedoch nicht automatisch alle Azure-Metrikdaten von Gast-VMs erfasst – Sie müssen die Metrikerfassung aktivieren. Dieser Artikel unterstützt Sie dabei, zusätzliche Diagnosemetriken für Ihre Azure-VMs zu aktivieren und zu konfigurieren.

Das Aktivieren der Metrikerfassung ermöglicht Folgendes:

- Benachrichtigung über das Erreichen von Arbeitsspeicher-, Datenträger- und CPU-Limits für Ihre VMs
- Erkennen von Nutzungstrends und Anomalien
- Steuern Ihrer Kosten durch eine Dimensionierung gemäß Nutzung
- Erhalten von Optimierungsempfehlungen für eine kosteneffiziente Größenanpassung von Cloudyn.

Angenommen, Sie möchten die CPU-Auslastung in % und die Arbeitsspeicherauslastung in % für Ihre Azure-VMs überwachen. Die entsprechenden Azure-VM-Metriken lauten _CPU in Prozent_ und _\Memory\Zugesicherte verwendete Bytes (\%)_ .

> [!NOTE]
> Das Sammeln von erweiterten Metriken wird nur zusammen mit Azure-Überwachung auf Gastebene unterstützt. Cloudyn ist nicht mit dem [Log Analytics-Agent](../../azure-monitor/agents/agents-overview.md) kompatibel.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>Bestimmen, ob erweiterte Metriken aktiviert sind

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Wählen Sie unter **Virtuelle Computer** eine VM aus, und klicken Sie unter **Überwachung** auf **Metriken**. Eine Liste der verfügbaren Metriken wird angezeigt.
3. Wählen Sie einige Metriken aus, und die Daten für diese Metriken werden in einem Diagramm angezeigt.  
    ![Beispielmetrik: CPU-Auslastung in Prozent für den Host](./media/azure-vm-extended-metrics/metric01.png)

Im vorangehenden Beispiel steht ein eingeschränkter Satz mit Standardmetriken für Ihre Hosts bereit, es sind jedoch keine Metriken zum Arbeitsspeicher verfügbar. Arbeitsspeichermetriken gehören zu den erweiterten Metriken. In diesem Fall sind erweiterte Metriken nicht für den virtuellen Computer aktiviert. Sie müssen einige zusätzliche Schritte ausführen, um die erweiterten Metriken zu aktivieren. Nachfolgend wird die Vorgehensweise zum Aktivieren dieser erweiterten Metriken beschrieben.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Aktivieren erweiterter Metriken im Azure-Portal

Die Standardmetriken sind Metriken zum Hostcomputer. Ein Beispiel ist die Metrik _CPU in Prozent_. Darüber hinaus stehen grundlegende Metriken für Gast-VMs zur Verfügung, diese werden auch als erweiterte Metriken bezeichnet. Beispiele für erweiterte Metriken: _\Memory\Zugesicherte verwendete Bytes (\%)_ und _\Memory\Verfügbare Bytes_.

Das Aktivieren der erweiterten Metriken ist einfach. Aktivieren Sie für jede VM die Überwachung auf Gastebene. Wenn Sie die Überwachung auf Gastebene aktivieren, wird der Azure-Diagnose-Agent auf der VM installiert. Standardmäßig wird ein grundlegender Satz von erweiterten Metriken hinzugefügt. Das folgende Verfahren ist für klassische und reguläre VMs sowie für Windows- und Linux-VMs identisch.

Beachten Sie, dass die Überwachung auf Gastebene sowohl bei Windows als auch bei Linux ein Speicherkonto erfordert. Wenn Sie Überwachung auf Gastebene aktivieren und kein vorhandenes Speicherkonto auswählen, wird eins für Sie erstellt.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Aktivieren der Überwachung auf Gastebene für vorhandene VMs

1. Zeigen Sie in **Virtuelle Computer** die Liste Ihrer VMs an, und wählen Sie eine VM aus.
2. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.
3. Klicken Sie auf der Seite „Diagnoseeinstellungen“ auf **Überwachung auf Gastebene aktivieren**.  
    ![Aktivieren der Überwachung auf Gastebene auf der Seite „Übersicht“](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Nach einigen Minuten wird der Azure-Diagnose-Agent auf der VM installiert. Ein grundlegender Satz von Metriken wird hinzugefügt. Aktualisieren Sie die Seite. Die hinzugefügten Leistungsindikatoren werden auf der Registerkarte „Übersicht“ angezeigt.
5. Wählen Sie unter „Überwachung“ **Metriken** aus.
6. Wählen Sie im Metrikdiagramm unter **Metrik-Namespace** die Option **Gast (klassisch)** aus.
7. In der Metrikliste können Sie alle verfügbaren Leistungsindikatoren für die Gast-VM anzeigen.  
    ![Liste mit Beispiel erweiterter Metriken](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Aktivieren der Überwachung auf Gastebene für neue VMs

Wenn Sie neue virtuelle Computer erstellen, wählen Sie auf der Registerkarte „Verwaltung“ **Ein** für **Diagnose des Gastbetriebssystems** aus.

![Festlegen der Diagnose des Gastbetriebssystems auf EIN](./media/azure-vm-extended-metrics/new-enable-diag.png)

Weitere Informationen zum Aktivieren erweiterter Metriken für virtuelle Azure-Computer finden Sie unter [Grundlegendes zum Azure Linux-Agent und seiner Verwendung](../../virtual-machines/extensions/agent-linux.md) und [Übersicht über den Agent für virtuelle Azure-Computer](../../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Resource Manager-Anmeldeinformationen

Nach dem Aktivieren der erweiterten Metriken müssen Sie sicherstellen, dass Cloudyn Zugriff auf Ihre [Resource Manager-Anmeldeinformationen](./activate-subs-accounts.md) hat. Ihre Anmeldeinformationen werden für Cloudyn benötigt, um Leistungsdaten für Ihre VMs zu erfassen und anzuzeigen. Außerdem werden die Daten verwendet, um Empfehlungen zur Kostenoptimierung zu erstellen. Cloudyn muss mindestens drei Tage lang Leistungsdaten von einer Instanz erhalten, um entscheiden zu können, ob es sich um einen Kandidaten für eine Verkleinerungsempfehlung handelt.

## <a name="enable-vm-metrics-with-a-script"></a>Aktivieren von VM-Metriken mit einem Skript

Sie können VM-Metriken mit Azure PowerShell-Skripts aktivieren. Wenn Sie viele VMs besitzen, für die Sie Metriken aktivieren möchten, können Sie den Vorgang mit einem Skript automatisieren. Beispielskripts finden Sie in GitHub im Abschnitt zur [Aktivierung der Azure-Diagnose](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Anzeigen von Azure-Leistungsmetriken

Um Leistungsmetriken für Ihre Azure-Instanzen im Cloudyn-Portal anzuzeigen, navigieren Sie zu **Assets** > **Compute** > **Instanz-Explorer**. Erweitern Sie in der Liste der VM-Instanzen eine Instanz, und erweiterten Sie dann eine Ressource, um Details anzuzeigen.

![Im Instanz-Explorer gezeigte Beispielinformationen](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie den Azure Resource Manager-API-Zugriff für Ihre Konten noch nicht aktiviert haben, fahren Sie mit [Aktivieren von Azure-Abonnements und -Konten](./activate-subs-accounts.md) fort.