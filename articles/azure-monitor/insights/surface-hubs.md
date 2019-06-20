---
title: Überwachen von Surface Hubs mit Azure Monitor | Microsoft-Dokumentation
description: Verwenden Sie die Surface Hub-Lösung, um die Integrität Ihrer Surface Hubs zu überwachen und um zu verstehen, wie sie verwendet werden.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 7e0dbb4c3cd8ae4bb552e7b7f0748f1bde2f51de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232787"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Überwachen von Surface Hubs mit Azure Monitor zum Verfolgen ihrer Integrität

![Surface Hub-Symbol](./media/surface-hubs/surface-hub-symbol.png)

Dieser Artikel beschreibt, wie Sie die Surface Hub-Lösung in Azure Monitor verwenden können, um Microsoft Surface Hub-Geräte zu überwachen. Mit dieser Lösung können Sie die Integrität Ihrer Surface Hubs überwachen und untersuchen, wie sie verwendet werden.

Auf jedem Surface Hub ist Microsoft Monitoring Agent installiert. Über den Agent können Sie Daten von Surface Hub an einen Log Analytics-Arbeitsbereich in Azure Monitor senden. Protokolldateien werden auf Surface Hubs gelesen und dann an Azure Monitor gesendet. Probleme wie Server, die offline sind, Kalender, die nicht synchronisiert werden, oder ein Gerätekonto, das sich nicht bei Skype anmelden kann, werden in Azure Monitor auf dem Surface Hub-Dashboard angezeigt. Mithilfe der Daten im Dashboard können Sie Geräte identifizieren, die nicht ausgeführt werden oder bei denen andere Probleme vorliegen, und möglicherweise Fehlerbehebungen für die erkannten Probleme anwenden.

## <a name="install-and-configure-the-solution"></a>Installieren und Konfigurieren der Lösung
Verwenden Sie die folgenden Informationen zum Installieren und Konfigurieren der Lösung. Für die Verwaltung von Surface Hubs in Azure Monitor benötigen Sie Folgendes:

* Eine [Log Analytics-Abonnementebene](https://azure.microsoft.com/pricing/details/log-analytics/), die die Anzahl der Geräte unterstützt, die Sie überwachen möchten. Log Analytics-Preise hängen davon ab, wie viele Geräte registriert sind und wie viele Daten verarbeitet werden. Sie sollten dies bei der Planung Ihrer Surface Hub-Implementierung berücksichtigen.

Wählen Sie als Nächstes einen vorhandenen Log Analytics-Arbeitsbereich aus, oder erstellen Sie einen neuen Arbeitsbereich. Detaillierte Anweisungen für die Verwendung beider Methoden finden Sie in [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../learn/quick-create-workspace.md). Wenn der Log Analytics-Arbeitsbereich konfiguriert wurde, haben Sie zwei Möglichkeiten zum Registrieren Ihrer Surface Hub-Geräte:

* Automatisch über Intune
* Manuell über **Einstellungen** auf Ihrem Surface Hub-Gerät

## <a name="set-up-monitoring"></a>Einrichten der Überwachung
Sie können die Integrität und Aktivität Ihres Surface Hub-Geräts mit Azure Monitor überwachen. Sie können Surface Hub mit Intune oder lokal über **Einstellungen** auf Surface Hub registrieren.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Verbinden von Surface Hubs mit Azure Monitor über Intune
Sie benötigen die Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel für den Log Analytics-Arbeitsbereich, der Ihre Surface Hubs verwalten soll. Sie können sie aus den Arbeitsbereichseinstellungen im Azure-Portal abrufen.

Intune ist ein Microsoft-Produkt, mit dem Sie die Konfigurationseinstellungen für einen Log Analytics-Arbeitsbereich zentral verwalten können, die auf ein Gerät oder mehrere Geräte angewendet werden. Gehen Sie wie folgt vor, um Ihre Geräte über Intune zu konfigurieren:

1. Melden Sie sich bei Intune an.
2. Navigieren Sie zu **Einstellungen** > **Verbundene Quellen**.
3. Erstellen oder bearbeiten Sie eine Richtlinie basierend auf der Surface Hub-Vorlage.
4. Navigieren Sie zum Abschnitt „Azure Operational Insights“ der Richtlinie, und fügen Sie der Richtlinie die *Arbeitsbereichs-ID* und den *Arbeitsbereichsschlüssel* von Log Analytics hinzu.
5. Speichern Sie die Richtlinie.
6. Verknüpfen Sie die Richtlinie mit der entsprechenden Gruppe von Geräten.

   ![Intune-Richtlinie](./media/surface-hubs/intune.png)

Intune synchronisiert dann die Log Analytics-Einstellungen mit den Geräten in der Zielgruppe und registriert diese in Ihrem Log Analytics-Arbeitsbereich.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Verbinden von Surface Hubs mit Azure Monitor über die App „Einstellungen“
Sie benötigen die Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel für den Log Analytics-Arbeitsbereich, der Ihre Surface Hubs verwalten soll. Sie können sie aus den Einstellungen des Log Analytics-Arbeitsbereichs im Azure-Portal abrufen.

Wenn Sie Intune verwenden, um Ihre Umgebung zu verwalten, können Sie Geräte manuell über **Einstellungen** auf jedem Surface Hub registrieren:

1. Öffnen Sie auf Ihrem Surface Hub **Einstellungen**.
2. Geben Sie die Anmeldeinformationen des Administrators für das Gerät auf Aufforderung ein.
3. Klicken Sie auf **Dieses Gerät** und dann unter **Überwachung** auf **Log Analytics-Einstellungen konfigurieren**.
4. Wählen Sie **Überwachung aktivieren** aus.
5. Geben Sie im Dialogfeld mit den Log Analytics-Einstellungen die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel** von Log Analytics ein.  
   ![settings](./media/surface-hubs/settings.png)
6. Klicken Sie auf **OK**, um die Konfiguration abzuschließen.

Eine Bestätigung informiert Sie darüber, ob die Konfiguration erfolgreich auf das Gerät angewendet wurde. Ist dies der Fall, wird eine Meldung angezeigt, die besagt, dass der Agent erfolgreich mit Azure Monitor verbunden wurde. Das Gerät beginnt dann mit dem Senden von Daten an Azure Monitor, wo Sie sie anzeigen und darauf reagieren können.

## <a name="monitor-surface-hubs"></a>Überwachen von Surface Hubs
Das Überwachen Ihrer Surface Hubs mit Azure Monitor gleicht dem Überwachen anderer registrierter Geräte.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Wenn Sie auf der Surface Hub-Kachel klicken, wird die Integrität des Geräts angezeigt.

   ![Surface Hub-Dashboard](./media/surface-hubs/surface-hub-dashboard.png)

Sie können [Warnungen](../platform/alerts-overview.md) auf der Grundlage von vorhandenen oder benutzerdefinierten Protokollsuchvorgängen erstellen. Mit den Daten, die Azure Monitor auf Surface Hubs sammelt, können Sie nach Problemen suchen und Warnungen für die Bedingungen ausgegeben, die Sie für Ihre Geräte definieren.

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie [Protokollabfragen in Azure Monitor](../log-query/log-query-overview.md), um ausführliche Surface Hub-Daten anzuzeigen.
* Erstellen Sie [Warnungen](../platform/alerts-overview.md), um benachrichtigt zu werden, wenn Probleme mit Surface Hubs auftreten.
