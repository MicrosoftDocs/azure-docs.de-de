---
title: Konfigurieren der Azure Active Directory-Richtlinie für Azure Data Catalog
description: Es kann vorkommen, dass Sie sich beim Azure Data Catalog-Portal anmelden können, jedoch eine Fehlermeldung erhalten, wenn Sie versuchen, sich beim Tool zum Registrieren von Datenquellen anzumelden.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: e69a7e3bd104d0fb82b248b6560d4fd082c88426
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116600"
---
# <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory-Richtlinienkonfiguration

Es kann vorkommen, dass Sie sich beim Azure Data Catalog-Portal anmelden können, jedoch eine Fehlermeldung erhalten, wenn Sie versuchen, sich beim Tool zum Registrieren von Datenquellen anzumelden. Dieser Fehler kann auftreten, wenn Sie sich im Unternehmensnetzwerk befinden oder von außerhalb des Unternehmensnetzwerks eine Verbindung herstellen.

## <a name="registration-tool"></a>Registrierungstool

Das Registrierungstool verwendet die *Formularauthentifizierung* , um Benutzeranmeldungen mit Azure Active Directory zu überprüfen. Um die Anmeldung zu ermöglichen, muss der Azure Active Directory-Administrator die Formularauthentifizierung in der *globalen Authentifizierungsrichtlinie*aktivieren.

Wie im folgenden Screenshot gezeigt, kann die Authentifizierung in der globalen Authentifizierungsrichtlinie separat für Intranet- und Extranetverbindungen aktiviert werden. Anmeldefehler können auftreten, wenn die Formularauthentifizierung für das Netzwerk, über das Sie eine Verbindung herstellen, nicht aktiviert ist.

 ![Globale Authentifizierungsrichtlinie für Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Weitere Informationen finden Sie unter [Konfigurieren von Authentifizierungsrichtlinien](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Azure Data Catalog-Instanz](data-catalog-get-started.md)