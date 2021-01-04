---
title: Überprüfen der Integrität von Azure Active Directory Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Integrität einer verwalteten Azure AD DS-Domäne (Azure Active Directory Domain Services) überprüfen und die Statusmeldungen mithilfe des Azure-Portals verstehen.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620323"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Überprüfen der Integrität einer verwalteten Azure Active Directory Domain Services-Domäne

Azure Active Directory Domain Services (Azure AD DS) führt einige Hintergrundaufgaben aus, um die verwaltete Domäne fehlerfrei und auf dem neuesten Stand zu halten. Zu diesen Aufgaben gehören das Ausführen von Sicherungen, das Anwenden von Sicherheitsupdates und das Synchronisieren von Daten aus Azure AD. Wenn Probleme mit der verwalteten Azure AD DS-Domäne auftreten, werden diese Aufgaben möglicherweise nicht erfolgreich abgeschlossen. Um Probleme zu überprüfen und zu beheben, können Sie den Integritätsstatus einer verwalteten Domäne im Azure-Portal überprüfen.

In diesem Artikel wird gezeigt, wie Sie den Integritätsstatus von Azure AD DS anzeigen und die angezeigten Informationen oder Warnungen verstehen.

## <a name="view-the-health-status"></a>Anzeigen des Integritätsstatus

Der Integritätsstatus einer verwalteten Domäne kann im Azure-Portal angezeigt werden. Neben Informationen zum Zeitpunkt der letzten Sicherung und Synchronisierung mit Azure AD finden Sie auch mögliche Warnungen, die auf ein Problem mit der Integrität der verwalteten Domäne hinweisen. Führen Sie die folgenden Schritte aus, um den Integritätsstatus einer verwalteten Domäne anzuzeigen:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus.
1. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie links im Azure AD DS-Ressourcenfenster die Option **Integrität** aus. Der folgende Beispielscreenshot zeigt eine fehlerfreie verwaltete Domäne sowie den Status der letzten Sicherung und Synchronisierung mit Azure AD:

    ![Übersicht über die Seite „Integrität“ im Azure-Portal mit dem Azure Active Directory Domain Services-Status](./media/check-health/health-page.png)

Auf der Seite „Integrität“ gibt der Zeitstempel *Letzte Auswertung* an, wann die verwaltete Domäne zuletzt überprüft wurde. Die Integrität einer verwalteten Domäne wird stündlich ausgewertet. Warten Sie nach Änderungen an einer verwalteten Domäne bis zum nächsten Auswertungszyklus, um den aktualisierten Integritätsstatus anzuzeigen.

Der Status oben rechts auf der Seite gibt die Gesamtintegrität der verwalteten Domäne an. Der Status berücksichtigt alle vorhandenen Warnungen für Ihre Domäne. Die folgende Tabelle enthält die verfügbaren Statusindikatoren:

| Status | Symbol | Erklärung |
| --- | :----: | --- |
| Wird ausgeführt | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Die verwaltete Domäne wird ordnungsgemäß ausgeführt, und es liegen keine kritischen Fehler oder Warnungen vor. Eventuell gibt es Informationsmeldungen für die Domäne. |
| Eingreifen erforderlich (Warnung) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Für die verwaltete Domäne liegen keine kritischen Warnungen vor, aber es ist mindestens eine Warnung vorhanden, die behandelt werden sollte. |
| Eingreifen erforderlich (Kritisch) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Es gibt mindestens eine kritische Warnung für die verwaltete Domäne, die behandelt werden muss. Außerdem sind eventuell Warnungen und/oder Informationsmeldungen vorhanden. |
| Wird bereitgestellt | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Die verwaltete Domäne wird bereitgestellt. |

## <a name="understand-monitors-and-alerts"></a>Grundlegendes zu Monitoren und Warnungen

Der Integritätsstatus einer verwalteten Domäne gibt zwei Arten von Informationen an: *Monitore* und *Warnungen*. Monitore zeigen den Zeitpunkt an, an dem grundlegende Hintergrundaufgaben abgeschlossen wurden. Warnungen stellen Informationen oder Vorschläge bereit, um die Stabilität der verwalteten Domäne zu verbessern.

### <a name="monitors"></a>Monitore

Monitore sind Bereiche einer verwalteten Domäne, die regelmäßig überprüft werden. Wenn aktive Warnungen für die verwaltete Domäne vorhanden sind, kann dies dazu führen, dass einer der Monitore ein Problem meldet. Azure AD DS bietet derzeit Monitore für die folgenden Bereiche:

* Backup
* Synchronisierung mit Azure AD

#### <a name="backup-monitor"></a>Monitor „Sicherung“

Der Monitor „Sicherung“ überprüft, ob regelmäßige automatische Sicherungen der verwalteten Domäne erfolgreich ausgeführt wurden. Die folgende Tabelle enthält die verfügbaren Statuswerte des Monitors „Sicherung“:

| Detailwert | Erklärung |
| --- | --- |
| Nie gesichert | Bei neuen verwalteten Domänen ist dieser Status normal. Die erste Sicherung sollte 24 Stunden nach der Bereitstellung der verwalteten Domäne erstellt werden. Wenn dieser Status weiterhin auftritt, [erstellen Sie eine Azure-Supportanfrage][azure-support]. |
| Last backup was taken 1 to 14 days ago (Letzte Sicherung vor 1 bis 14 Tagen) | Dieser Zeitbereich ist der erwartete Status für den Monitor „Sicherung“. In diesem Zeitraum sollten regelmäßige automatische Sicherungen erfolgen. |
| Last backup was taken more than 14 days ago (Letzte Sicherung vor mehr als 14 Tagen) | Ein Zeitraum von mehr als zwei Wochen weist auf ein Problem mit den regelmäßigen automatischen Sicherungen hin. Möglicherweise wird das Erstellen von Sicherungen für die verwaltete Domäne durch aktive kritische Warnungen verhindert. Beheben Sie alle aktiven Warnungen für die verwaltete Domäne. Wenn der Monitor „Sicherung“ den Status nicht aktualisiert und keine aktuelle Sicherung meldet, [erstellen Sie eine Azure-Supportanfrage][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Monitor „Synchronisierung mit Azure AD“

Eine verwaltete Domäne wird regelmäßig mit Azure Active Directory synchronisiert. Die Anzahl der Benutzer- und Gruppenobjekte sowie die Anzahl der seit der letzten Synchronisierung im Azure AD Verzeichnis vorgenommenen Änderungen wirken sich auf die Dauer der Synchronisierung aus. Wenn die verwaltete Domäne zuletzt vor mehr als drei Tagen synchronisiert wurde, überprüfen Sie, ob aktive Warnungen vorhanden sind, und beheben Sie diese. Wenn der Monitor „Synchronisierung“ den Status nicht aktualisiert und nach der Behandlung aller aktiven Warnungen keine aktuelle Synchronisierung anzeigt, [erstellen Sie eine Azure-Supportanfrage][azure-support].

### <a name="alerts"></a>Alerts

Warnungen werden für Probleme in einer verwalteten Domäne generiert, die behoben werden müssen, damit der Dienst ordnungsgemäß ausgeführt werden kann. Jede Warnung enthält eine Beschreibung des Problems sowie eine URL, unter der Sie eine Erläuterung der Schritte zur Behebung des Problems finden. Weitere Informationen zu möglichen Warnungen und deren Behebung finden Sie unter [Problembehandlung von Warnungen](troubleshoot-alerts.md).

Warnungen bezüglich des Integritätsstatus werden in die folgenden Schweregrade eingeteilt:

 * **Kritische Warnungen** werden für Probleme generiert, die schwerwiegende Auswirkungen auf die verwaltete Domäne haben. Diese Warnungen sollten sofort behoben werden. Die Azure-Plattform kann die verwaltete Domäne erst wieder überwachen, verwalten, patchen und synchronisieren, wenn die Probleme behoben wurden.
 * **Warnungen** informieren Sie über Probleme, die sich auf Vorgänge in der verwalteten Domäne auswirken können, wenn das jeweilige Problem weiterhin besteht. Diese Warnungen enthalten außerdem Empfehlungen zum Schutz der verwalteten Domäne.
 * **Informationsmeldungen** sind Benachrichtigungen, die sich nicht negativ auf die verwaltete Domäne auswirken. Informationsmeldungen bieten gewisse Einblicke in die Vorgänge in der verwalteten Domäne.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Warnungen, die auf der Seite „Integritätsstatus“ angezeigt werden, finden Sie unter [Azure AD Domain Services – Problembehandlung von Warnungen][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
