---
title: Erstellen und Verwalten von Aktionsgruppen im Azure-Portal
description: Erfahren Sie, wie Sie Aktionsgruppen im Azure-Portal erstellen und verwalten.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/30/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: f4f10191ea28f73732f81eb9dc25321f82683652
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416967"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Erstellen und Verwalten von Aktionsgruppen im Azure-Portal
## <a name="overview"></a>Übersicht ##
Eine Aktionsgruppe ist eine Sammlung von Benachrichtigungseinstellungen, die vom Besitzer eines Azure-Abonnements definiert wurden. Azure Monitor- und Service Health-Warnungen verwenden Aktionsgruppen, um Benutzer zu benachrichtigen, dass eine Warnung ausgelöst wurde. Verschiedene Warnungen können je nach den Bedürfnissen des Benutzers die gleiche Aktionsgruppe oder verschiedene Aktionsgruppen verwenden. Sie können in einem Abonnement bis zu 2.000 Aktionsgruppen konfigurieren.

Sie konfigurieren eine Aktion, um eine Person per E-Mail oder SMS zu benachrichtigen. Die Person erhält eine Bestätigung mit dem Hinweis, dass sie der Aktionsgruppe hinzugefügt wurde.

In diesem Artikel wird beschrieben, wie Sie Aktionsgruppen im Azure-Portal erstellen und verwalten.

Jede Aktion besteht aus den folgenden Eigenschaften:

* **Name:** Ein eindeutiger Bezeichner innerhalb der Aktionsgruppe.  
* **Aktionstyp:** Die ausgeführte Aktion. Beispiele sind das Senden eines Sprachanrufs, SMS, E-Mail oder das Auslösen verschiedener Arten von automatisierten Aktionen. Siehe „Typen“ weiter unten in diesem Artikel.
* **Details**: Die entsprechenden Details. Diese können je nach *Aktionstyp* variieren.

Weitere Informationen zum Verwenden von Azure Resource Manager-Vorlagen zur Konfigurierung von Aktionsgruppen finden Sie unter [Aktionsgruppen-Resource Manager-Vorlagen](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Erstellen einer Aktionsgruppe mit dem Azure-Portal ##
1. Wählen Sie im [Portal](https://portal.azure.com) die Option **Überwachen**. Im Bereich **Überwachen** sind alle Ihre Überwachungseinstellungen und -daten an einem zentralen Ort zusammengefasst.

    ![Der Dienst „Überwachen“](./media/action-groups/home-monitor.png)
1. Wählen Sie **Warnungen** und dann **Aktionen verwalten** aus.

    ![Verwalten der Schaltfläche „Aktionen“](./media/action-groups/manage-action-groups.png)
1. Wählen Sie **Aktionsgruppe hinzufügen**, und füllen Sie die Felder aus.

    ![Der Befehl „Aktionsgruppe hinzufügen“](./media/action-groups/add-action-group.png)
1. Geben Sie jeweils einen Namen in die Felder **Aktionsgruppenname** und **Kurzname** ein. Der Kurzname wird anstelle eines vollständigen Aktionsgruppennamens verwendet, wenn Benachrichtigungen mithilfe dieser Gruppe gesendet werden.

      ![Das Dialogfeld „Aktionsgruppe hinzufügen“](./media/action-groups/action-group-define.png)

1. In das Feld **Abonnement** wird automatisch Ihr aktuelles Abonnement eingetragen. In diesem Abonnement wird die Aktionsgruppe gespeichert.

1. Wählen Sie die **Ressourcengruppe**, in der die Aktionsgruppe gespeichert wird.

1. Definieren Sie eine Liste mit Aktionen. Geben Sie für jede Aktion Folgendes an:

    a. **Name:** Geben Sie einen eindeutigen Bezeichner für diese Aktion ein.

    b. **Aktionstyp:** Wählen Sie E-Mail/SMS/Push/Sprachanruf, Logik-App, Webhook, ITSM oder Automation-Runbook aus.

    c. **Details:** Geben Sie je nach Aktionstyp eine Telefonnummer, eine E-Mail-Adresse, einen Webhook-URI, eine Azure-App, eine ITSM-Verbindung oder ein Automation-Runbook ein. Legen Sie für die ITSM-Aktion darüber hinaus **Arbeitselement** und andere Felder fest, die Ihr ITSM-Tool benötigt.

1. Wählen Sie **OK**, um die Aktionsgruppe zu erstellen.

## <a name="manage-your-action-groups"></a>Verwalten von Aktionsgruppen ##
Nachdem Sie eine Aktionsgruppe erstellt haben, wird diese im Abschnitt **Aktionsgruppen** des Bereichs **Überwachen** angezeigt. Wählen Sie die Aktionsgruppe, die Sie verwalten möchten, um Folgendes zu tun:

* Fügen Sie Aktionen hinzu, bearbeiten oder entfernen Sie diese.
* Löschen der Aktionsgruppe.

## <a name="action-specific-information"></a>Aktionsspezifische Informationen
> [!NOTE]
> Informationen zu den numerischen Grenzwerten für die unten angegebenen Elemente finden Sie unter [Grenzwerte für Monitor](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits).  

**Azure-App-Pushvorgang**: Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Azure-App-Aktionen verfügen.

**E-Mail**: E-Mails werden von den folgenden E-Mail-Adressen gesendet. Achten Sie darauf, dass Ihre E-Mail-Filterung ordnungsgemäß konfiguriert ist.
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von E-Mail-Aktionen verfügen. Weitere Informationen finden Sie im Artikel [Ratenlimits für Sprache, SMS-Nachrichten, E-Mail-Nachrichten, Azure App-Pushbenachrichtigungen und Webhookbeiträge](./../../azure-monitor/platform/alerts-rate-limiting.md).

**ITSM**: Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von ITSM-Aktionen verfügen. Für eine ITSM-Aktion muss eine ITSM-Verbindung hergestellt werden. Informieren Sie sich, wie Sie [eine ITSM-Verbindung erstellen](../../azure-monitor/platform/itsmc-overview.md).

**Logik-App**: Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Logik-App-Aktionen verfügen.

**Funktions-App**: Die für Funktions-Apps als Aktionen konfigurierten Funktionsschlüssel werden über die Functions-API gelesen, für die derzeit Funktions-Apps der Version 2 erforderlich sind, um die App-Einstellung „AzureWebJobsSecretStorageType“ auf „files“ festzulegen. Weitere Informationen finden Sie unter [Changes to Key Management in Functions V2]( https://aka.ms/funcsecrets) (Änderungen der Schlüsselverwaltung in Functions V2).

**Runbook**: Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Runbookaktionen verfügen. Informationen zu den Einschränkungen für Runbook-Nutzlasten finden Sie unter [Azure-Abonnementdienstgrenzen](../../azure-subscription-service-limits.md).

**SMS**: Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von SMS-Aktionen verfügen. Weitere wichtige Informationen finden Sie auch in den Artikeln zu [Ratenlimits](./../../azure-monitor/platform/alerts-rate-limiting.md) und zum [Verhalten von SMS-Benachrichtigungen](../../azure-monitor/platform/alerts-sms-behavior.md). 

**Sprachanruf**: Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Sprachanrufaktionen verfügen. Weitere Informationen finden Sie im Artikel [Ratenlimits für Sprache, SMS-Nachrichten, E-Mail-Nachrichten, Azure App-Pushbenachrichtigungen und Webhookbeiträge](./../../azure-monitor/platform/alerts-rate-limiting.md).

**Webhook**: Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Webhookaktionen verfügen. Webhooks werden mit den folgenden Regeln abgerufen. Es wird maximal zweimal versucht, den Webhookaufruf durchzuführen, wenn der HTTP-Statuscode 408, 429, 503 oder 504 zurückgegeben wird oder der HTTP-Endpunkt nicht reagiert. Der erste Wiederholungsversuch erfolgt nach 10 Sekunden. Der zweite Wiederholungsversuch erfolgt nach 100 Sekunden. Nach zwei Ausfällen wird der Endpunkt 30 Minuten lang von keiner Aktionsgruppe aufgerufen. 

Quell-IP-Adressbereiche
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Um Updates über Änderungen an diesen IP-Adressen zu erhalten, empfehlen wir Ihnen, eine Service Health-Warnung zu konfigurieren, die eine Überwachung auf Informationsbenachrichtigungen des Aktionsgruppendiensts ausführt.

## <a name="next-steps"></a>Nächste Schritte ##
* Erfahren Sie mehr über das [SMS-Warnungsverhalten in Aktionsgruppen](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Erweitern Sie Ihr [Verständnis des Webhookschemas für Aktivitätsprotokollwarnungen](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Erfahren Sie mehr über den [ITSM-Connector](../../azure-monitor/platform/itsmc-overview.md).
* Weitere Informationen zu [Ratenlimits](../../azure-monitor/platform/alerts-rate-limiting.md) für Warnungen.
* Verschaffen Sie sich eine [Übersicht über Aktivitätsprotokollwarnungen](../../azure-monitor/platform/alerts-overview.md), und erfahren Sie, wie Sie Warnungen empfangen können.  
* Erfahren Sie, wie Sie [Warnungen konfigurieren, wenn eine Dienstintegritätsbenachrichtigung gesendet wird](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
