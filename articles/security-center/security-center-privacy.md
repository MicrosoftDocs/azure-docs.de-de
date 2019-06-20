---
title: Verwalten von Benutzerdaten in Azure Security Center | Microsoft-Dokumentation
description: " Dieser Artikel enthält Informationen zur Verwaltung von Benutzerdaten in Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: rkarlin
ms.openlocfilehash: fcec410df631a58b76878a4cb327ca2fb04a2105
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60703471"
---
# <a name="manage-user-data-in-azure-security-center"></a>Verwalten von Benutzerdaten in Azure Security Center
Dieser Artikel enthält Informationen zur Verwaltung der Benutzerdaten in Azure Security Center. Bei der Verwaltung der Benutzerdaten haben Sie auch die Möglichkeit, auf Daten zuzugreifen, Daten zu löschen oder zu exportieren.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Ein Security Center-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen wird, kann mit dem Tool auf die Kundendaten zugreifen. Weitere Informationen zu den Rollen „Leser“, „Besitzer“ und „Mitwirkender“ finden Sie unter [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md). Weitere Informationen zur Rolle „Kontoadministrator“ finden Sie unter [Azure-Abonnementadministratoren](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Suchen nach und Identifizieren von personenbezogenen Daten
Ein Security Center-Benutzer kann seine personenbezogenen Daten über das Azure-Portal anzeigen. In Security Center werden nur Details für einen Sicherheitskontakt gespeichert, wie z.B. E-Mail-Adressen und Telefonnummern. Weitere Informationen finden Sie unter [Bereitstellen von Details für einen Sicherheitskontakt in Azure Security Center](security-center-provide-security-contact-details.md).

Im Azure-Portal kann ein Benutzer mithilfe des Features „Just-In-Time“ von Security Center für den Zugriff auf VMs zulässige IP-Konfigurationen anzeigen. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features](security-center-just-in-time.md).

Im Azure-Portal kann ein Benutzer von Security Center bereitgestellte Sicherheitswarnungen einschließlich IP-Adressen und Details zu Angreifern anzeigen. Weitere Informationen finden Sie unter [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Klassifizieren personenbezogener Daten
Im Feature „Sicherheitskontakt“ von Security Center gefundene personenbezogene Daten müssen nicht klassifiziert werden. Als Daten gespeichert sind eine E-Mail-Adresse (oder mehrere E-Mail-Adressen) und eine Telefonnummer. [Kontaktdaten](security-center-provide-security-contact-details.md) werden von Security Center überprüft.

Die mit dem Feature [Just-In-Time](security-center-just-in-time.md) von Security Center gespeicherten IP-Adressen und Portnummern müssen nicht klassifiziert werden.

Nur ein Benutzer, dem die Rolle „Administrator“ zugewiesen ist, kann personenbezogene Daten durch das [Anzeigen von Warnungen](security-center-managing-and-responding-alerts.md) in Security Center klassifizieren.

## <a name="securing-and-controlling-access-to-personal-data"></a>Sichern und Steuern des Zugriffs auf personenbezogene Daten
Ein Security Center-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann auf [Sicherheitskontaktdaten](security-center-provide-security-contact-details.md) zugreifen.

Ein Security Center-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann auf die [Just-In-Time-Richtlinien](security-center-just-in-time.md) zugreifen.

Ein Security Center-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann auf seine [Warnungen](security-center-managing-and-responding-alerts.md) zugreifen.

## <a name="updating-personal-data"></a>Aktualisieren personenbezogener Daten
Ein Security Center-Benutzer, dem die Rolle „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann [Sicherheitskontaktdaten](security-center-provide-security-contact-details.md) über das Azure-Portal aktualisieren.

Ein Security Center-Benutzer, dem die Rolle „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann auf die [Just-In-Time-Richtlinien](security-center-just-in-time.md) zugreifen.

Ein Kontoadministrator kann keine Warnungsvorfälle bearbeiten. [Warnungsvorfälle](security-center-managing-and-responding-alerts.md) gelten als Sicherheitsdaten und sind schreibgeschützt.

## <a name="deleting-personal-data"></a>Löschen personenbezogener Daten
Ein Security Center-Benutzer, dem die Rolle „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann [Sicherheitskontaktdaten](security-center-provide-security-contact-details.md) über das Azure-Portal löschen.

Ein Security Center-Benutzer, dem die Rolle „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann [Just-In-Time-Richtlinien](security-center-just-in-time.md) über das Azure-Portal löschen.

Ein Security Center-Benutzer kann keine Warnungsvorfälle löschen. Aufgrund der Sicherheitsanforderungen gelten [Warnungsvorfälle](security-center-managing-and-responding-alerts.md) als schreibgeschützte Daten.

## <a name="exporting-personal-data"></a>Exportieren personenbezogener Daten
Ein Security Center-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann wie folgt [Sicherheitskontaktdaten](security-center-provide-security-contact-details.md) exportieren:

- Durchführen eines Kopiervorgangs über das Azure-Portal
- Ausführen des Azure-REST-API-Aufrufs GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Ein Security Center-Benutzer, dem die Rolle „Kontoadministrator“ zugewiesen ist, kann die [Just-In-Time-Richtlinien](security-center-just-in-time.md) mit den IP-Adressen wie folgt exportieren:

- Durchführen eines Kopiervorgangs über das Azure-Portal
- Ausführen des Azure-REST-API-Aufrufs GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Ein Kontoadministrator kann die Warnungsdetails wie folgt exportieren:

- Durchführen eines Kopiervorgangs über das Azure-Portal
- Ausführen des Azure-REST-API-Aufrufs GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Weitere Informationen finden Sie unter [Abrufen von Sicherheitswarnungen (GET-Auflistung)](https://msdn.microsoft.com/library/mt704050.aspx).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Einschränken der Verwendung der personenbezogenen Daten für die Profilerstellung oder das Marketing ohne Zustimmung
Ein Security Center-Benutzer kann das Abonnement kündigen, indem er seine [Sicherheitskontaktdaten](security-center-provide-security-contact-details.md) löscht.

[Just-in-Time-Daten](security-center-just-in-time.md) gelten als nicht identifizierbare Daten und werden für einen Zeitraum von 30 Tagen beibehalten.

[Warnungsdaten](security-center-managing-and-responding-alerts.md) gelten als Sicherheitsdaten und werden für einen Zeitraum von zwei Jahren beibehalten.

## <a name="auditing-and-reporting"></a>Überwachung und Berichterstellung
Überwachungsprotokolle von Sicherheitskontakt-, Just-In-Time- und Warnungsaktualisierungen werden in [Azure-Aktivitätsprotokollen](../azure-monitor/platform/activity-logs-overview.md) beibehalten.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Verwalten von Benutzerdaten finden Sie unter [Verwalten von Benutzerdaten in Azure Security Center](security-center-investigation-user-data.md).
