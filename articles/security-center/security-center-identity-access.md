---
title: Überwachen von Identität und Zugriff in Azure Security Center | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Identitäts- und Zugriffsfunktion in Azure Security Center die Zugriffsaktivitäten der Benutzer sowie identitätsbezogene Probleme überwachen können.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: monhaber
ms.openlocfilehash: 16548ae75567fa3ba6f8c9135d61945bd28d2db8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428423"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Überwachen der Identität und des Zugriffs im Azure Security Center (Vorschauversion)
In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure Security Center die Identität und Zugriffsaktivitäten von Benutzern überwachen.

> [!NOTE]
> Der Link „Identität und Zugriff (*klassisch*) anzeigen“ wird am 31. Juli 2019 eingestellt. Klicken Sie [hier](security-center-features-retirement-july2019.md#menu_classicidentity), um Informationen zu alternativen Diensten zu erhalten.

> [!NOTE]
> Die Überwachung der Identität und des Zugriffs ist als Vorschauversion und im Standard-Tarif vom Security Center verfügbar. Weitere Informationen zu den Tarifen von Security Center finden Sie unter [Preise](security-center-pricing.md).
>

Die Identität sollte die Kontrollebene für Ihr Unternehmen darstellen, und der Schutz der Identität sollte höchste Priorität haben. Der Fokus bei der Entwicklung des Sicherheitsbereichs wurde von der Netzwerkorientierung auf die Identitätsorientierung verlagert. Bei der Sicherheit geht es immer weniger um die Verteidigung Ihres Netzwerks und immer mehr um die Verteidigung Ihrer Daten sowie um die Verwaltung der Sicherheit Ihrer Apps und Benutzer. Da heutzutage jedoch immer mehr Daten und Apps in die Cloud verlagert werden, ist die Identität zur neuen Grenze geworden.

Durch die Überwachung von Identitätsaktivitäten können Sie proaktive Maßnahmen ergreifen, bevor es zu einem Vorfall kommt, oder einen Angriffsversuch abwehren. Das Dashboard für Identität und Zugriff bietet Empfehlungen wie etwa zu Folgendem:

- MFA für privilegierte Konten in Ihrem Abonnement aktivieren
- Externe Konten mit Schreibberechtigungen aus Ihrem Abonnement entfernen
- Privilegierte externe Konten aus Ihrem Abonnement entfernen

> [!NOTE]
> Wenn Ihr Abonnement mehr als 600 Konten umfasst, kann das Security Center nicht die Identitätsempfehlungen für Ihr Abonnement ausführen. Empfehlungen, die nicht ausgeführt werden, werden wie nachfolgend gezeigt unter „Nicht verfügbare Bewertungen“ aufgeführt.
Im Security Center können keine Identitätsempfehlungen für Administrator-Agents eines Cloud Solution Provider-Partners (CSP) ausgeführt werden.
>

Unter [Empfehlungen](security-center-identity-access.md#recommendations) finden Sie eine Liste der Identitäts- und Zugriffsempfehlungen, die vom Security Center bereitgestellt werden.

## <a name="monitoring-security-health"></a>Überwachung der Sicherheitsintegrität
Auf dem Dashboard **Security Center – Übersicht** können Sie den Sicherheitsstatus Ihrer Ressourcen überwachen. Der Abschnitt **Ressourcen** ist kein Integritätsindikator, der die Schweregrade für die einzelnen Ressourcentypen anzeigt.

Sie können eine Liste aller Probleme anzeigen, indem Sie auf **Empfehlungen** klicken. Unter **Ressourcen** können Sie eine Liste der Probleme einsehen, die speziell für Compute und Apps, die Datensicherheit, Netzwerke oder Identität und Zugriff gelten. Weitere Informationen zur Anwendung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).

Eine vollständige Liste der Identitäts- und Zugriffsempfehlungen finden Sie unter [Empfehlungen](security-center-identity-access.md#recommendations).

Wählen Sie zum Fortsetzen des Vorgangs **Identität und Zugriff** unter **Ressourcen** oder im Security Center-Hauptmenü aus.

![Security Center-Dashboard][1]

## <a name="monitor-identity-and-access"></a>Überwachen der Identität und des Zugriffs
Unter **Identität und Zugriff** befinden sich zwei Registerkarten:

- **Übersicht**: Vom Security Center identifizierte Empfehlungen.
- **Abonnements**: Liste Ihrer Abonnements und der jeweils aktuelle Sicherheitsstatus.

![„Identity & Access“ (Identität und Zugriff)][2]

### <a name="overview-section"></a>Abschnitt „Übersicht“
Unter **Übersicht** finden Sie eine Liste von Empfehlungen. Die erste Spalte enthält die Empfehlung. Die zweite Spalte enthält die Gesamtanzahl der Abonnements, die diese Empfehlung betrifft. Die dritte Spalte gibt den Schweregrad des Problems an.

1. Wählen Sie eine Empfehlung aus. Das Fenster der Empfehlung wird geöffnet und zeigt Folgendes an:

   - Beschreibung der Empfehlung
   - Liste der fehlerhaften und fehlerfreien Abonnements
   - Liste der Ressourcen, die aufgrund einer fehlerhaften Bewertung nicht überprüft wurden oder aufgrund der Ausführung in einem Abonnement mit dem Free-Tarif nicht bewertet wurden

   ![Fenster der Empfehlung][3]

1. Um weitere Einzelheiten zu erhalten, wählen Sie ein Abonnement in der Liste aus.

### <a name="subscriptions-section"></a>Abonnementabschnitt
Unter **Abonnements** finden Sie eine Liste von Abonnements. Die erste Spalte enthält die Abonnements. Die zweite Spalte zeigt die Gesamtanzahl der Empfehlungen für die einzelnen Abonnements. Die dritte Spalte gibt die Schweregrade der Probleme an.

![Registerkarte „Abonnements“][4]

1. Wählen Sie ein Abonnement aus. Eine zusammenfassende Darstellung mit drei Registerkarten wird geöffnet:

   - **Empfehlungen**: Basiert auf vom Security Center ausgeführten Bewertungen, bei denen ein Fehler aufgetreten ist.
   - **Bestandene Bewertungen**: Liste der vom Security Center ausgeführten Bewertungen, die bestanden wurden.
   - **Nicht verfügbare Bewertungen**: Liste von Bewertungen, bei denen ein Fehler aufgetreten ist oder ein Abonnement mit mehr als 600 Konten vorkommt.

   Unter **Empfehlungen** finden Sie eine Liste der Empfehlungen für das ausgewählte Abonnement und den Schweregrad der einzelnen Empfehlungen.

   ![Empfehlungen für das ausgewählte Abonnement][5]

1. Wählen Sie eine Empfehlung aus, um eine Beschreibung der Empfehlung, eine Liste von fehlerhaften und fehlerfreien Abonnements und eine Liste von nicht überprüften Ressourcen zu erhalten.

   ![Beschreibung der Empfehlung][6]

   Unter **Bestandene Bewertungen** finden Sie eine Liste der bestandenen Bewertungen.  Der Schweregrad dieser Bewertungen wird immer in Grün angezeigt.

   ![Bestandene Bewertungen][7]

1. Wählen Sie eine bestandene Bewertung aus der Liste aus, um eine Beschreibung der Bewertung und eine Liste von fehlerfreien Abonnements zu erhalten. Auf einer Registerkarte für fehlerhafte Abonnements werden alle Abonnements aufgeführt, bei denen ein Fehler aufgetreten ist.

   ![Bestandene Bewertungen][8]

## <a name="recommendations"></a>Empfehlungen
Der folgenden Tabelle können Sie entnehmen, welche Identitäts- und Zugriffsempfehlungen verfügbar sind und welche Aktionen bei ihrer Anwendung jeweils ausgeführt werden.

|Ressourcentyp|Sicherheitsbewertung|Empfehlung|BESCHREIBUNG|
|----|----|----|----|
|Abonnement|50|MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.|Aktivieren Sie für alle Abonnementkonten mit Administratorrechten die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA), um eine Sicherheitsverletzung von Konten oder Ressourcen zu verhindern.|
|Abonnement|40|MFA sollte für Ihre Abonnementkonten mit Schreibberechtigungen aktiviert sein.|Aktivieren Sie für alle Abonnementkonten mit Schreibberechtigungen die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA), um eine Sicherheitsverletzung von Konten oder Ressourcen zu verhindern.|
|Abonnement|30|Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Entfernen Sie externe Konten mit Besitzerberechtigungen aus Ihrem Abonnement, um nicht überwachten Zugriff zu verhindern.|
|Abonnement|30|MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein.|Aktivieren Sie für alle Abonnementkonten mit Leseberechtigungen die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA), um eine Sicherheitsverletzung von Konten oder Ressourcen zu verhindern.|
|Abonnement|25|Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Entfernen Sie externe Konten mit Schreibberechtigungen aus Ihrem Abonnement, um nicht überwachten Zugriff zu verhindern. |
|Abonnement|20|Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Entfernen Sie veraltete Konten mit Besitzerberechtigungen aus Ihrem Abonnement.|
|Abonnement|5|Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.|Entfernen Sie veraltete Konten aus Ihren Abonnements, um nur den Zugriff auf aktuelle Benutzer zuzulassen. |
|Abonnement|5|Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.|Legen Sie mehrere Abonnementbesitzer fest, um Redundanz beim Administratorzugriff zu gewährleisten.|
|Abonnement|5|Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.|Legen Sie höchstens drei Abonnementbesitzer fest, um die Möglichkeit einer Sicherheitsverletzung durch einen kompromittierten Besitzer zu verringern.|
|Schlüsseltresor|5|Diagnoseprotokolle in Key Vault sollten aktiviert sein.|Aktivieren Sie Protokolle, und bewahren Sie sie bis zu ein Jahr lang auf. Auf diese Weise können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist. |
|Abonnement|15|Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Entfernen Sie externe Konten mit Leseberechtigungen aus Ihrem Abonnement, um nicht überwachten Zugriff zu verhindern.| 

> [!NOTE]
> Wenn Sie eine Richtlinie für bedingten Zugriff erstellt haben, die MFA erfordert, für die jedoch Ausschlüsse festgelegt sind, wird die Richtlinie beim Assessment der MFA-Empfehlung für Security Center als nicht kompatibel eingestuft, da sie zulässt, dass sich einige Benutzer in Azure ohne MFA anmelden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie in den folgenden Themen:

- [Schützen von Computern und Anwendungen im Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Schützen Ihres Netzwerks in Azure Security Center](security-center-network-recommendations.md)
- [Schützen des Azure SQL-Diensts und der Daten im Azure Security Center](security-center-sql-service-recommendations.md)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Hier erfahren Sie, wie Sie Warnungen verwalten und auf Sicherheitsvorfälle in Security Center reagieren.
* [Verstehen der Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Hier finden Sie Informationen zu den unterschiedlichen Arten von Sicherheitswarnungen.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md)festgelegt ist. Hier finden Sie häufig gestellte Fragen zur Verwendung von Security Center.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
