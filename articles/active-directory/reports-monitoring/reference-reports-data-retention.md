---
title: Wie lange speichert Azure AD die Berichtsdaten? | Microsoft-Dokumentation
description: Erfahren Sie, wie lange Azure die verschiedenen Arten von Berichtsdaten speichert.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc5f902d75084d649f211d589e53041f1eb37f0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012713"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Wie lange speichert Azure AD die Berichtsdaten?


Dieser Artikel enthält Informationen zu den Datenaufbewahrungsrichtlinien für die unterschiedlichen Aktivitätsberichte in Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Wann beginnt Azure AD mit der Datensammlung?

| Azure AD-Edition | Start der Erfassung |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Beim Registrieren für ein Abonnement |
| Azure AD Free| Beim ersten Öffnen des Blatts [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) oder bei der ersten Verwendung der [Berichterstellungs-APIs](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Wann stehen die Aktivitätsdaten im Azure-Portal zur Verfügung?

- **Sofort**, wenn Sie bereits Berichte im Azure-Portal verwendet haben.
- **Innerhalb von 2 Stunden**, wenn Sie die Berichterstellung im Azure-Portal nicht aktiviert haben.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Wie schnell werden nach Erwerb einer Premium-Lizenz Daten zu Aktivitäten angezeigt?

Wenn Ihnen bereits mit einer kostenlosen Lizenz Daten zu Aktivitäten angezeigt werden, können Sie diese sofort nach dem Upgrade sehen. Wenn Sie noch keine Daten haben, dauert es ein bis zwei Tage, bis die Daten in den Berichten nach dem Upgrade auf eine Premium-Lizenz angezeigt werden.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Wann beginnt Azure AD mit der Sammlung von Sicherheitssignaldaten?  

Die Erfassung von Sicherheitssignalen beginnt, wenn Sie sich für die Verwendung von **Identity Protection Center** entscheiden. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Wie lange speichert Azure AD die Daten?

**Aktivitätsberichte**    

| Bericht                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Überwachungsprotokolle             | 7 Tage        | 30 Tage             | 30 Tage             |
| Anmeldungen               | 7 Tage        | 30 Tage             | 30 Tage             |
| Verwendung von Azure AD MFA        | 30 Tage       | 30 Tage             | 30 Tage             |

Sie können die Aktivitätsdaten zu Überwachung und Anmeldung länger als den oben beschriebenen Standardaufbewahrungszeitraum speichern, indem Sie sie mit Azure Monitor an ein Azure Storage-Konto weiterleiten. Weitere Informationen finden Sie unter [Archivieren von Azure AD-Protokollen in einem Azure Storage-Konto](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sicherheitssignale**

| Bericht         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Gefährdete Benutzer  | 7 Tage        | 30 Tage             | 90 Tage             |
| Riskante Anmeldungen | 7 Tage        | 30 Tage             | 90 Tage             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Werden nach Erwerb einer Azure AD Premium-Lizenz die Daten des letzten Monats angezeigt?

**Nein**, das ist nicht möglich. Aktivitätsdaten werden in einer kostenlosen Version von Azure bis zu sieben Tage gespeichert. Das heißt, beim Wechsel von einer kostenlosen zu einer Premium-Version können nur Daten von maximal 7 Tagen angezeigt werden.

---