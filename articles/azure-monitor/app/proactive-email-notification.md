---
title: Intelligente Erkennung in Azure Application Insights – bevorstehende Änderung an standardmäßigen Benachrichtigungsempfängern | Microsoft-Dokumentation
description: Überwachen Sie Anwendungsablaufverfolgungen mit Azure Application Insights auf ungewöhnliche Muster in der Ablaufverfolgungstelemetrie.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/13/2019
ms.author: harelbr
ms.openlocfilehash: f984a34be1c5d5fdd18a00812107318df8f5d9bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61299000"
---
# <a name="smart-detection-e-mail-notification-change"></a>Änderung an E-Mail-Benachrichtigungen der intelligenten Erkennung

Basierend auf Kundenfeedback ändern wir am 1. April 2019 die Standardrollen, die E-Mail-Benachrichtigungen von der intelligenten Erkennung erhalten.

## <a name="what-is-changing"></a>Was ändert sich?

Derzeit werden E-Mail-Benachrichtigungen der intelligenten Erkennung standardmäßig an die Rollen _Abonnementbesitzer_, _Abonnementmitwirkender_ und _Abonnementleser_ gesendet. Zu diesen Rollen gehören häufig Benutzer, die nicht aktiv an der Überwachung beteiligt sind, daher erhalten viele dieser Benutzer unnötige Benachrichtigungen. Um diesen Umstand zu verbessern, nehmen wir eine Änderung vor, sodass E-Mail-Benachrichtigungen standardmäßig nur an die Rollen [Benutzer mit Leseberechtigung für Überwachungsdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) und [Mitwirkender an der Überwachung](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) gesendet werden.

## <a name="scope-of-this-change"></a>Geltungsbereich dieser Änderung

Diese Änderung wirkt sich mit folgenden Ausnahmen auf alle Regeln der intelligenten Erkennung aus:

* Regeln der intelligenten Erkennung, die als Vorschauversion gekennzeichnet sind. Diese Regeln der intelligenten Erkennung unterstützen gegenwärtig keine E-Mail-Benachrichtigungen.

* Regel für Fehleranomalien. Diese Regel gilt für die neuen Standardrollen, sobald sie von einer klassischen Warnung zur einheitlichen Warnungsplattform migriert wurde. (Weitere Informationen finden Sie [hier](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Vorbereitung auf diese Änderung

Um sicherzustellen, dass E-Mail-Benachrichtigungen von der intelligenten Erkennung an die relevanten Benutzer gesendet werden, müssen diese Benutzer den Rollen [Benutzer mit Leseberechtigung für Überwachungsdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) und [Mitwirkender an der Überwachung](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) des Abonnements zugewiesen werden.

Um Benutzer über das Azure-Portal der Rolle „Benutzer mit Leseberechtigung für Überwachungsdaten“ oder „Mitwirkender an der Überwachung“ zuzuweisen, befolgen Sie die im Artikel [Hinzufügen einer Rollenzuweisung](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) beschriebenen Schritte. Achten Sie darauf, _Benutzer mit Leseberechtigung für Überwachungsdaten_ oder _Mitwirkender an der Überwachung_ als die Rolle auszuwählen, der die Benutzer zugewiesen werden.

> [!NOTE]
> Spezifische Empfänger von Benachrichtigungen der intelligenten Erkennung, die in den Regeleinstellungen über die Option _Weitere E-Mail-Empfänger_ konfiguriert wurden, sind von dieser Änderung nicht betroffen. Diese Empfänger erhalten weiterhin die E-Mail-Benachrichtigungen.

Wenn Sie Fragen oder Bedenken zu dieser Änderung haben, treten Sie gerne mit uns in [Kontakt](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur intelligenten Erkennung:

- [Anomalien bei Fehlern](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Arbeitsspeicherverluste](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Leistungsanomalien](../../azure-monitor/app/proactive-performance-diagnostics.md)