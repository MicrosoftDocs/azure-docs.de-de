---
title: 'Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschungen | Microsoft-Dokumentation'
description: In diesem Thema wird die Funktion zum Verhindern von versehentlichen Löschungen (Verhindern versehentlicher Löschvorgänge) in Azure AD Connect beschrieben.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1244dd460196e5882caab0d4b526850da48d084
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60383388"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect-Synchronisierung: Verhindern eines versehentlichen Löschvorgangs
In diesem Thema wird die Funktion zum Verhindern von versehentlichen Löschungen (Verhindern versehentlicher Löschvorgänge) in Azure AD Connect beschrieben.

Bei der Installation von Azure AD Connect wird die Funktion zum Schutz vor unbeabsichtigtem Löschen standardmäßig aktiviert und so konfiguriert, das Exporte mit mehr als 500 Löschungen unterbunden werden. Diese Funktion dient zum Schutz vor unbeabsichtigten Konfigurationsänderungen und Änderungen an Ihrem lokalen Verzeichnis, die sich auf viele Benutzer und andere Objekte auswirken würden.

## <a name="what-is-prevent-accidental-deletes"></a>Verhindern von versehentlichen Löschungen
Häufige Szenarios für Löschvorgänge sind beispielsweise:

* Änderungen an der [Filterung](how-to-connect-sync-configure-filtering.md), bei denen die Auswahl einer gesamten [Organisationseinheit](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) oder [Domäne](how-to-connect-sync-configure-filtering.md#domain-based-filtering) aufgehoben wird.
* Das Löschen aller Objekte in einer Organisationseinheit.
* Das Umbenennen einer Organisationseinheit, sodass alle darin enthaltenen Objekte als außerhalb des Synchronisierungsbereichs liegend betrachtet werden.

Der Standardwert von 500 Objekten kann mit PowerShell mit `Enable-ADSyncExportDeletionThreshold` geändert werden. Dies ist ein Bestandteil des AD-Synchronisierungsmoduls, das mit Azure Active Directory Connect installiert wird. Sie sollten diesen Wert entsprechend der Größe Ihres Unternehmens konfigurieren. Da der Synchronisierungsplaner alle 30 Minuten ausgeführt wird, entspricht der Wert der Anzahl von Löschvorgängen, die innerhalb von 30 Minuten erfolgen.

Wenn zu viele Löschungen in Azure AD exportiert werden sollen, wird der Export angehalten, und Sie erhalten eine E-Mail-Nachricht wie die folgende:

![E-Mail zum Verhindern von versehentlichen Löschungen](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hallo (Ansprechpartner für Technik), am (Datum und Uhrzeit) hat der Dienst für die Identitätssynchronisierung festgestellt, dass die Anzahl von Löschvorgängen den für (Name der Organisation) konfigurierten Schwellenwert überschritten hat. Es wurden insgesamt (Anzahl) Objekte bei dieser Ausführung der Identitätssynchronisierung zum Löschen gesendet. Dies entspricht dem konfigurierten Schwellenwert für Löschungen von (Anzahl) Objekten bzw. überschreitet ihn. Sie müssen vor dem Fortfahren bestätigen, dass diese Löschvorgänge durchgeführt werden sollen. Weitere Informationen zu dem in dieser E-Mail-Nachricht genannten Fehler finden Sie unter „Verhindern von zufälligem Löschen“.*
>
> 

Sie können den Status `stopped-deletion-threshold-exceeded` auch auf der **Synchronization Service Manager** -Benutzeroberfläche für das Exportprofil sehen.
![Synchronization Service Manager-Benutzeroberfläche zum Verhindern von versehentlichen Löschungen](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Wenn Sie diese Nachricht unerwartet erhalten haben, untersuchen Sie die Grunde dafür, und ergreifen Sie die nötigen Maßnahmen, um das Problem zu beheben. Führen Sie die folgenden Schritte aus, um zu ermitteln, welche Objekte gelöscht werden sollen:

1. Starten Sie den **Synchronisierungsdienst** über das Startmenü.
2. Gehen Sie zu **Connectors**.
3. Wählen Sie den Connector mit dem Typ **Azure Active Directory**.
4. Wählen Sie unter **Actions** (Aktionen) auf der rechten Seite **Search Connector Space** (Connectorbereich suchen).
5. Wählen Sie im Popupfenster unter **Scope** (Bereich) die Option **Disconnected Since** (Getrennt seit) aus, und wählen Sie einen Zeitpunkt in der Vergangenheit. Klicken Sie auf **Suchen**. Auf dieser Seite wird eine Übersicht über alle zu löschenden Objekte angezeigt. Klicken Sie auf die einzelnen Objekte, um zusätzliche Informationen dazu zu erhalten. Sie können auch auf **Spalteneinstellung** klicken, um zusätzliche Attribute hinzuzufügen, die im Raster angezeigt werden.

![Suche Connectorbereich](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

Wenn alle Löschvorgänge gewünscht sind, gehen Sie folgendermaßen vor:

1. Führen Sie zum Abrufen des aktuellen Schwellenwerts für Löschungen das PowerShell-Cmdlet `Get-ADSyncExportDeletionThreshold` aus. Geben Sie das Konto und das Kennwort eines globalen Azure AD-Administrators ein. Der Standardwert ist 500.
2. Um den Schutz vorübergehend zu deaktivieren und diese Löschvorgänge zuzulassen, führen Sie folgendes PowerShell-Cmdlet aus: `Disable-ADSyncExportDeletionThreshold`. Geben Sie das Konto und das Kennwort eines globalen Azure AD-Administrators ein.
   ![Anmeldeinformationen](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Lassen Sie Azure Active Directory Connector weiterhin ausgewählt, wählen Sie die Aktion **Run** (Ausführen) und anschließend **Export** (Exportieren) aus.
4. Führen Sie zum erneuten Aktivieren des Schutzes folgendes PowerShell-Cmdlet aus: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Ersetzen Sie „500“ durch den Wert, den Sie beim Abrufen des aktuellen Schwellenwerts für Löschungen notiert haben. Geben Sie das Konto und das Kennwort eines globalen Azure AD-Administrators ein.

## <a name="next-steps"></a>Nächste Schritte
**Übersichtsthemen**

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
