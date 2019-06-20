---
title: Azure AD Domain Services – Überprüfen der Integrität Ihrer verwalteten Domäne | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Integrität Ihrer verwalteten Domäne im Azure-Portal über die Seite „Integrität“ überprüfen.
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: c3ff0b7daeaef077ea0d83782aeac6cda56fc659
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245375"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Überprüfen der Integrität einer verwalteten Azure AD Domain Services-Domäne

## <a name="overview-of-the-health-page"></a>Übersicht über die Seite „Integrität“
Über die Seite „Integrität“ im Azure-Portal können Sie darüber auf dem Laufenden bleiben, was in Ihrer verwalteten Domäne passiert. In diesem Artikel werden die Elemente der Seite „Integrität“ einzeln beschrieben.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Anzeigen der Integrität Ihrer verwalteten Domäne
1. Navigieren Sie im Azure-Portal zur [Seite „Azure AD Domain Services“](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices).
2. Klicken Sie auf die Domäne, für die Sie die Integrität anzeigen möchten.
3. Klicken Sie im linken Navigationsbereich auf **Integrität**.

Die folgende Abbildung zeigt eine beispielhafte Seite „Integrität“: ![Beispiel für die Seite „Integrität“](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> Die Integrität Ihrer verwalteten Domäne wird einmal pro Stunde ausgewertet. Warten Sie nach dem Vornehmen von Änderungen an Ihrer verwalteten Domäne bis zum nächsten Auswertungszyklus, um den aktualisierten Integritätsstatus für Ihre verwaltete Domäne anzuzeigen. Der Zeitstempel „Zuletzt ausgewertet“ oben rechts zeigt an, wann die Integrität Ihrer verwalteten Domäne zuletzt ausgewertet wurde.
>

### <a name="status-of-your-managed-domain"></a>Status Ihrer verwalteten Domäne
Der Status oben rechts auf der Seite „Integrität“ zeigt die Gesamtintegrität Ihrer verwalteten Domäne an. In den Status werden alle vorhandenen Warnungen Ihrer Domäne einbezogen. Sie können den Status Ihrer Domäne auch auf der Übersichtsseite von Azure AD Domain Services anzeigen.

| Status | Symbol | Erklärung |
| --- | :----: | --- |
| Wird ausgeführt | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15"> | Ihre verwaltete Domäne wird ohne Probleme ausgeführt, und es liegen keine kritischen Fehler oder Warnmeldungen vor. Für die Domäne können ggf. Warnungen vom Typ „Information“ bestehen. |
| Eingreifen erforderlich (Warnung) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15"> | Für Ihre verwaltete Domäne liegen keine kritischen Warnungen vor, aber es ist mindestens eine Warnmeldung vorhanden, die ein Eingreifen erforderlich macht. |
| Eingreifen erforderlich (Kritisch) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15"> | Für Ihre verwaltete Domäne liegt mindestens eine kritische Warnung vor. Außerdem sind ggf. Warnmeldungen oder Warnungen vom Typ „Information“ vorhanden. |
| Wird bereitgestellt | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15"> | Die Domäne wird gerade bereitgestellt. |

## <a name="monitors"></a>Monitore
Monitore sind Aspekte Ihrer verwalteten Domäne, die von Azure AD Domain Services regelmäßig überwacht werden. Die beste Möglichkeit, Ihre Monitore in einem fehlerfreien Zustand zu halten, ist das Beheben der Probleme aller aktiven Warnungen für Ihre verwaltete Domäne.

Mit Azure AD Domain Services wird derzeit Folgendes überwacht:
 - Backup
 - Synchronisierung mit Azure AD

### <a name="the-backup-monitor"></a>Monitor „Sicherung“
Hiermit wird überwacht, ob für Ihre verwaltete Domäne regelmäßige Sicherungen erstellt werden. In der folgenden Tabelle ist beschrieben, was im Monitor „Sicherung“ in der Spalte mit den Details enthalten ist:

| Detailwert | Erklärung |
| --- | --- |
|„Never backed up“ (Nie gesichert) | Dieser Status ist für eine neu erstellte verwaltete Domäne normal. Generell wird die erste Sicherung 24 Stunden nach der Bereitstellung Ihrer verwalteten Domäne erstellt. Wenn Ihre verwaltete Domäne nicht neu erstellt wurde oder dieser Status ungewöhnlich lange angezeigt wird, sollten Sie sich [an den Support wenden](contact-us.md). |
| Last backup was taken 1 to 14 days ago (Letzte Sicherung vor 1 bis 14 Tagen) | Dies ist der Wert, der für den Monitor „Sicherung“ erwartet wird. |
| Last backup was taken more than 14 days ago (Letzte Sicherung vor mehr als 14 Tagen) | Längere Zeiträume als zwei Wochen für die letzte Sicherung sind ungewöhnlich. Unter Umständen wird das Erstellen regelmäßiger Sicherungen für Ihre verwaltete Domäne durch aktive kritische Warnungen verhindert. Beheben Sie zuerst alle aktiven Warnungen für Ihre verwaltete Domäne, und [wenden Sie sich an den Support](contact-us.md), falls das Problem weiterhin besteht. |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitor „Synchronisierung mit Azure AD“
Microsoft überwacht, wie häufig Ihre verwaltete Domäne mit Azure Active Directory synchronisiert wird. Die Anzahl von Objekten (Benutzer und Gruppen) und die Anzahl von Änderungen, die seit der letzten Synchronisierung in Ihrer Azure AD-Instanz vorgenommen wurden, können sich darauf auswirken, wie lange eine Synchronisierung dauern kann. Falls Ihre verwaltete Domäne zuletzt vor mehr als drei Tagen synchronisiert wurde, sollten Sie sich [an den Support wenden](contact-us.md).

## <a name="alerts"></a>Alerts
Warnungen werden für Probleme in Ihrer verwalteten Domäne generiert, die behoben werden müssen, damit Azure AD Domain Services ausgeführt werden kann. Für jede Warnung wird das Problem beschrieben und eine URL zu einer Seite mit Lösungsansätzen angegeben, auf der bestimmte Schritte für die Behebung aufgeführt sind. Eine Übersicht über alle Warnungen und die dazugehörigen Lösungen finden Sie im Artikel zur [Problembehandlung von Warnungen](troubleshoot-alerts.md).

### <a name="alert-severity"></a>Schweregrad der Warnung
Warnungen sind in drei verschiedenen Sicherheitsebenen kategorisiert: Kritisch, Warnung und Information.

 * **Kritische** Warnungen sind Probleme, die eine erhebliche Auswirkung auf Ihre verwaltete Domäne haben. Diese Warnungen sollten sofort behoben werden, da Microsoft für Ihre verwaltete Domäne das Überwachen, Verwalten, Patchen und Synchronisieren nicht durchführen kann. 
 * Mit **Warnungen** werden Sie über Probleme benachrichtigt, die später eine Auswirkung auf Ihre verwaltete Domäne haben können. Diese Warnungen enthalten Empfehlungen zum Schützen Ihrer verwalteten Domäne.
 * Warnungen vom Typ **Information** sind Benachrichtigungen, die keine negative Auswirkung auf Ihre Domäne haben. Mit diesen Warnungen sollen Sie darüber informiert werden, was in Ihrer Domäne und in Azure AD Domain Services passiert.

## <a name="next-steps"></a>Nächste Schritte
- [Azure AD Domain Services – Problembehandlung von Warnungen](troubleshoot-alerts.md)
- [Weitere Informationen zu Azure AD Domain Services](overview.md)
- [Wenden Sie sich an das Produktteam](contact-us.md)
