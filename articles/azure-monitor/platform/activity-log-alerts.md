---
title: Aktivitätsprotokollwarnungen in Azure Monitor
description: Lassen Sie sich per SMS, Webhook und E-Mail usw. benachrichtigen, wenn bestimmte Ereignisse im Aktivitätsprotokoll auftreten.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 836fc20b6c3d46766801dce0e2aeda83a217d6a3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536080"
---
# <a name="alerts-on-activity-log"></a>Warnungen zum Aktivitätsprotokoll

## <a name="overview"></a>Übersicht

Aktivitätsprotokollwarnungen sind Warnungen, die aktiviert werden, wenn ein neues [Aktivitätsprotokollereignis](activity-log-schema.md) auftritt, das die in der Warnung angegebenen Bedingungen erfüllt. Die Warnungsregel wird basierend auf der Reihenfolge und der Menge der im [Azure-Aktivitätsprotokoll](platform-logs-overview.md) erfassten Ereignisse ausgelöst. Warnungsregeln des Aktivitätsprotokolls sind Azure-Ressourcen, sie können also mithilfe einer Azure Resource Manager-Vorlage erstellt werden. Sie können auch im Azure-Portal erstellt, aktualisiert oder gelöscht werden. In diesem Artikel werden die Konzepte erläutert, auf denen Aktivitätsprotokollwarnungen basieren. Weitere Informationen zur Erstellung oder Verwendung von Warnungsregeln des Aktivitätsprotokolls finden Sie unter [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](alerts-activity-log.md).

> [!NOTE]
> Warnungen können **nicht** für Ereignisse in der Kategorie „Warnung“ des Aktivitätsprotokolls erstellt werden.

Normalerweise erstellen Sie Aktivitätsprotokollwarnungen, um Benachrichtigungen zu empfangen, wenn Folgendes geschieht:

* Spezifische Vorgänge treten bei Ressourcen in Ihrem Azure-Abonnement auf, die häufig für bestimmte Ressourcengruppen oder Ressourcen gelten. Es kann beispielsweise sein, dass Sie benachrichtigt werden möchten, wenn ein beliebiger virtueller Computer in myProductionResourceGroup gelöscht wird. Unter Umständen möchten Sie auch benachrichtigt werden, wenn einem Benutzer unter Ihrem Abonnement neue Rollen zugewiesen werden.
* Ein Dienstintegritätsereignis tritt auf. Dienstintegritätsereignisse umfassen eine Benachrichtigung bei Vorfällen und Wartungsereignissen, die für Ressourcen Ihres Abonnements gelten.

Eine einfache Analogie zum Verständnis der Bedingungen, unter denen Warnungsregeln für das Aktivitätsprotokoll erstellt werden können, ist die Untersuchung oder Filterung von Ereignissen über das [Aktivitätsprotokoll im Azure-Portal](./activity-log.md#view-the-activity-log). In „Azure Monitor – Aktivitätsprotokoll“ können Sie erforderliche Ereignisse filtern oder finden und dann eine Warnung mit der Schaltfläche **Aktivitätsprotokollwarnung hinzufügen** erstellen.

In beiden Fällen wird mit einer Aktivitätsprotokollwarnung nur eine Überwachung auf Ereignisse in dem Abonnement durchgeführt, unter dem die Warnung erstellt wurde.

Sie können eine Aktivitätsprotokollwarnung basierend auf einer beliebigen Eigenschaft der obersten Ebene im JSON-Objekt für ein Aktivitätsprotokollereignis konfigurieren. Weitere Informationen finden Sie unter [Kategorien im Aktivitätsprotokoll](./activity-log.md#view-the-activity-log). Weitere Informationen zu Dienstintegritätsereignissen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen](../../service-health/alerts-activity-log-service-notifications-portal.md). 

Aktivitätsprotokollwarnungen verfügen über einige allgemeine Optionen:

- **Kategorie:** Verwaltung, Dienstintegrität, Autoskalierung, Sicherheit, Richtlinie und Empfehlung. 
- **Bereich:** Die einzelne Ressource oder Ressourcengruppe, für die die Warnung für das Aktivitätsprotokoll definiert ist. Der Bereich für eine Aktivitätsprotokollwarnung kann auf verschiedenen Ebenen definiert werden:
    - Ressourcenebene: Beispielsweise für einen bestimmten virtuellen Computer
    - Ressourcengruppenebene: Beispielsweise alle virtuellen Computer in einer bestimmten Ressourcengruppe
    - Abonnementebene: Beispielsweise alle virtuellen Computer in einem Abonnement oder alle Ressourcen in einem Abonnement
- **Ressourcengruppe**: Standardmäßig wird die Warnungsregel in der Ressourcengruppe des im Bereich definierten Ziels gespeichert. Der Benutzer kann auch die Ressourcengruppe definieren, in der die Warnungsregel gespeichert werden soll.
- **Ressourcentyp:** Der von Resource Manager definierte Namespace für das Ziel der Warnung.
- **Vorgangsname:** Der Name des [Azure-Ressourcenanbietervorgangs](../../role-based-access-control/resource-provider-operations.md), der für die rollenbasierte Zugriffssteuerung verwendet wird. Vorgänge, die nicht in Azure Resource Manager registriert sind, können nicht in Warnungsregeln des Aktivitätsprotokolls verwendet werden.
- **Ebene**: der Schweregrad des Ereignisses (Information, Warnung, Fehler oder Kritisch).
- **Status:** Der Status des Ereignisses (beispielsweise „Gestartet“, „Fehler“ oder „Erfolgreich“).
- **Ereignis initiiert von:** Wird auch als „Aufrufer“ bezeichnet. Die E-Mail-Adresse oder der Azure Active Directory-Bezeichner des Benutzers, der den Vorgang durchgeführt hat.

> [!NOTE]
> In einem Abonnement können bis zu 100 Warnungsregeln für die Aktivität im Bereich einer einzelnen Ressource, aller Ressourcen in der Ressourcengruppe oder der gesamten Abonnementebene erstellt werden.

Bei Aktivierung einer Aktivitätsprotokollwarnung wird eine Aktionsgruppe verwendet, um Aktionen oder Benachrichtigungen zu generieren. Eine Aktionsgruppe ist ein wiederverwendbarer Satz von Benachrichtigungsempfängern, z.B. E-Mail-Adressen, Webhook-URLs oder SMS-Telefonnummern. Mehrere Warnungen können auf die Empfänger verweisen, um Ihre Benachrichtigungskanäle zu zentralisieren und gruppieren. Wenn Sie Ihre Aktivitätsprotokollwarnung definieren, stehen Ihnen zwei Optionen zur Verfügung. Ihre Möglichkeiten:

* Verwenden Sie eine vorhandene Aktionsgruppe in Ihrer Aktivitätsprotokollwarnung.
* Erstellen Sie eine neue Aktionsgruppe.

Weitere Informationen zu Aktionsgruppen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](action-groups.md).


## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie eine [Übersicht über Warnungen](alerts-overview.md).
- Erfahren Sie mehr über das [Erstellen und Ändern von Aktivitätsprotokollwarnungen](alerts-activity-log.md).
- Weitere Informationen zum [Webhookschema für Aktivitätsprotokollwarnungen](activity-log-alerts-webhook.md).
- Weitere Informationen zu [Dienstintegritätsbenachrichtigungen](../../service-health/service-notifications.md).
