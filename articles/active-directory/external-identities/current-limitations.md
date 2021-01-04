---
title: Einschränkungen der B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: Aktuelle Einschränkungen der Azure Active Directory B2B-Zusammenarbeit
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4f960819aa208dcc8d3e476fc45a766452b612c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168949"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Einschränkungen der Azure AD B2B-Zusammenarbeit
Azure Active Directory B2B-Zusammenarbeit (Azure AD) unterliegt derzeit den in diesem Artikel beschriebenen Einschränkungen.

## <a name="possible-double-multi-factor-authentication"></a>Mögliche doppelte Multi-Factor Authentication
Mit Azure AD B2B können Sie die mehrstufige Authentifizierung bei der Ressourcenorganisation (die einladende Organisation) erzwingen. Die Gründe für diesen Ansatz werden in [Multi-Factor Authentication für Benutzer von Azure Active Directory B2B-Zusammenarbeit](conditional-access.md) erläutert. Wenn ein Partner bereits die mehrstufige Authentifizierung eingerichtet hat und sie erzwingt, müssen die Benutzer die Authentifizierung möglicherweise einmal in ihrer eigenen Organisation ausführen und dann erneut in Ihrer.

## <a name="instant-on"></a>Instant-On
Im Workflow der B2B-Zusammenarbeit werden Benutzer zum Verzeichnis hinzugefügt und während der Einlösung der Einladung, der App-Zuweisung usw. dynamisch aktualisiert. Die Aktualisierungs- und Schreibvorgänge erfolgen im Allgemeinen in einer Verzeichnisinstanz und müssen in allen Instanzen repliziert werden. Die Replikation wird durchgeführt, nachdem alle Instanzen aktualisiert wurden. Wenn das Objekt in einer Instanz geschrieben oder aktualisiert wurde und der Aufruf zum Abrufen dieses Objekts in einer anderen Instanz erfolgt ist, können bei der Replikation Wartezeiten auftreten. Wenn dies der Fall ist, führen Sie eine Aktualisierung aus, oder wiederholen Sie den Vorgang. Wenn Sie mit unserer API eine App schreiben, ist die Wiederholung des Vorgangs mit einem Backoff-Intervall ein sinnvolles Verfahren, um dieses Problem zu verringern.

## <a name="azure-ad-directories"></a>Azure AD-Verzeichnisse
Azure AD B2B unterliegt den Einschränkungen des Azure AD-Dienstverzeichnisses. Ausführliche Informationen zur Anzahl von Verzeichnissen, die ein Benutzer erstellen kann, und der Anzahl von Verzeichnissen, zu denen ein Benutzer oder Gastbenutzer gehören kann, finden Sie unter [Dienst- und andere Einschränkungen für Azure AD](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="national-clouds"></a>Nationale Clouds
[Nationale Clouds](../develop/authentication-national-cloud.md) sind physisch isolierte Azure-Instanzen. Die B2B-Zusammenarbeit wird über die Grenzen nationaler Clouds hinweg nicht unterstützt. Beispiel: Wenn sich Ihr Azure-Mandant in der öffentlichen globalen Cloud befindet, können Sie keinen Benutzer einladen, dessen Konto sich in einer nationalen Cloud befindet. Damit Sie mit dem Benutzer zusammenarbeiten können, fragen Sie ihn nach einer anderen E-Mail-Adresse, oder erstellen Sie ein Mitgliedsbenutzerkonto in Ihrem Verzeichnis.

## <a name="azure-us-government-clouds"></a>Microsoft Azure Cloud for US Government (Azure-Cloud für US-Behörden)
Innerhalb der Azure-Cloud für US-Behörden wird die B2B-Zusammenarbeit zwischen Mandanten unterstützt, die sich beide in der Azure-Cloud für US-Behörden befinden und die B2B-Zusammenarbeit unterstützen. Azure-Mandanten für US-Behörden, die B2B-Zusammenarbeit unterstützen, können auch mit Benutzern von sozialen Netzwerken zusammenarbeiten, die Microsoft- oder Google-Konten verwenden. Wenn Sie einen Benutzer, der zu keiner dieser Gruppen gehört (z. B. wenn er sich in einem Mandanten befindet, der nicht zur Azure-Cloud für US-Behörden gehört oder die B2B-Zusammenarbeit noch nicht unterstützt), einladen, schlägt die Einladung fehl, oder der Benutzer kann die Einladung nicht einlösen. Ausführliche Informationen zu weiteren Einschränkungen finden Sie unter [Azure Active Directory Premium P1 und P2](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Wie kann ich erkennen, ob die B2B-Zusammenarbeit in meinem Mandanten der Azure-Cloud für US-Behörden verfügbar ist?
Gehen Sie wie folgt vor, um herauszufinden, ob Ihr Mandant der Azure-Cloud für US-Behörden die B2B-Zusammenarbeit unterstützt:

1. Navigieren Sie in einem Browser zur folgenden URL, und ersetzen Sie *&lt;tenantname&gt;* durch den Namen Ihres Mandanten:

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Suchen Sie in der JSON-Antwort nach `"tenant_region_scope"`:

   - Wenn der Eintrag `"tenant_region_scope":"USGOV”` lautet, wird B2B unterstützt.
   - Wenn der Eintrag `"tenant_region_scope":"USG"` lautet, wird B2B nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Delegieren von Einladungen zur B2B-Zusammenarbeit](delegate-invitations.md)