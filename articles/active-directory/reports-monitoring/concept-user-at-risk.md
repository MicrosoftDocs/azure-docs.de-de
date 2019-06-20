---
title: Sicherheitsbericht „Benutzer mit Risikomarkierung“ im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über den Sicherheitsbericht „Benutzer mit Risikomarkierung“ im Azure Active Directory-Portal.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 463f5c2d03cd96089342aa9b22ef85ebc05aa909
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438128"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Bericht „Benutzer mit Risikomarkierung“ im Azure-Portal

Azure Active Directory (Azure AD) erkennt verdächtige Aktionen im Zusammenhang mit Ihren Benutzerkonten. Für jede erkannte Aktion wird ein Datensatz mit der Bezeichnung [Risikoereignis](concept-risk-events.md) erstellt.

Sie können über das [Azure-Portal](https://portal.azure.com) auf die Sicherheitsberichte zugreifen. Wählen Sie dazu das Blatt **Azure Active Directory** aus, und navigieren Sie dann zum Abschnitt **Sicherheit**. 

Die erkannten Risikoereignisse werden zum Berechnen folgender Werte verwendet:

- **Riskante Anmeldungen:** Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist. 

- **Benutzer mit Risikomarkierung:** Ein Benutzer mit Risikomarkierung ist ein Indikator für ein möglicherweise kompromittiertes Benutzerkonto. 

Weitere Informationen zum Konfigurieren der Richtlinien, die diese Risikoereignisse auslösen, finden Sie unter [Gewusst wie: Konfigurieren der Richtlinie zum Benutzerrisiko](../identity-protection/howto-user-risk-policy.md). 

![Riskante Anmeldungen](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Welche Azure AD-Lizenz benötigen Sie, um auf den Bericht „Gefährdete Benutzer“ zuzugreifen?  

In allen Editionen von Azure Active Directory stehen Sicherheitsberichte zu Benutzern mit Risikomarkierung zur Verfügung. Die Granularitätsebene von Berichten kann für die einzelnen Editionen aber variieren: 

- In den **Free- und Basic-Editionen von Azure Active Directory** erhalten Sie eine Liste mit Benutzern mit Risikomarkierung. 

- Darüber hinaus können Sie mit der Edition **Azure Active Directory Premium 1** einige zugrunde liegende Risikoereignisse untersuchen, die für die einzelnen Berichte erkannt wurden. 

- In der Edition **Azure Active Directory Premium 2** erhalten Sie die ausführlichsten Informationen zu allen zugrunde liegenden Risikoereignissen, und Sie können Sicherheitsrichtlinien konfigurieren, mit denen automatisch auf konfigurierte Risikostufen reagiert wird.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Bericht „Gefährdete Benutzer“ für die Azure AD-Editionen „Free“ und „Basic“

Der Bericht „Benutzer mit Risikomarkierung“ in den Editionen „Free“ und „Basic“ von Azure AD enthält eine Liste mit Benutzerkonten, die unter Umständen kompromittiert wurden. 

![Riskante Anmeldungen](./media/concept-user-at-risk/03.png)

Durch die Auswahl eines Benutzers werden Anmeldeinformationen bereitgestellt. Sie können für gefährdete Benutzer den Anmeldeverlauf des Benutzers prüfen und bei Bedarf das Kennwort zurücksetzen.

In diesem Dialogfenster steht Ihnen folgende Option zur Verfügung:

- Herunterladen des Berichts
- Suchen nach Benutzern

    ![Riskante Anmeldungen](./media/concept-user-at-risk/16.png)

Sie benötigen eine Premium-Lizenz, um ausführlichere Informationen zu erhalten.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Bericht „Gefährdete Benutzer“ für die Azure AD-Editionen vom Typ „Premium“

Der Bericht „Benutzer mit Risikomarkierung“ in den Premium-Editionen von Azure AD enthält Folgendes:

- Eine Liste mit Benutzerkonten, die unter Umständen kompromittiert wurden 

- Aggregierte Informationen zu den erkannten [Risikoereignistypen](concept-risk-events.md)

- Option zum Herunterladen des Berichts

- Option zum Konfigurieren einer [Richtlinie zum Beheben des Benutzerrisikos](../identity-protection/howto-user-risk-policy.md)  

![Riskante Anmeldungen](./media/concept-user-at-risk/71.png)

Wenn Sie einen Benutzer auswählen, erhalten Sie eine ausführliche Berichtsansicht für diesen Benutzer mit folgenden Optionen:

- Öffnen der Ansicht „Alle Anmeldungen“

- Das Kennwort des Benutzers zurücksetzen

- Verwerfen aller Ereignisse

- Untersuchen der gemeldeten Risikoereignisse für den Benutzer 

![Riskante Anmeldungen](./media/concept-user-at-risk/324.png)

Um ein Risikoereignis zu untersuchen, wählen Sie es in der Liste aus, um das Blatt **Details** für dieses Risikoereignis zu öffnen. Auf dem Blatt **Details** können Sie ein Risikoereignis manuell schließen oder ein manuell geschlossenes Risikoereignis wieder aktivieren. 

![Riskante Anmeldungen](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Nächste Schritte

- [Gewusst wie: Konfigurieren der Richtlinie zum Benutzerrisiko](../identity-protection/howto-user-risk-policy.md)
- [Gewusst wie: Konfigurieren der Richtlinie zum Beheben des Benutzerrisikos](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

