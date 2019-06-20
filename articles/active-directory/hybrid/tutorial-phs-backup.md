---
title: 'Tutorial:  Einrichten von PHS als Sicherung für AD FS in Azure AD Connect | Microsoft-Dokumentation'
description: In diesem Artikel wird veranschaulicht, wie Sie die Kennworthashsynchronisierung als Sicherung für AD FS aktivieren.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919007"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Tutorial:  Einrichten der Kennworthashsynchronisierung als Sicherung für AD FS in Azure AD Connect

Im folgenden Tutorial werden Sie durch die Einrichtung der Kennworthashsynchronisierung als Sicherung und Failover für AD FS geleitet.  In diesem Dokument wird außerdem gezeigt, wie Sie die Kennworthashsynchronisierung als primäre Authentifizierungsmethode aktivieren, falls AD FS ausgefallen oder nicht verfügbar ist.

>[!NOTE] 
>Obwohl diese Schritte in der Regel während einer Notfall- oder Ausfallsituation ausgeführt werden, empfiehlt es sich, dass Sie die Schritte testen und Ihre Verfahren überprüfen, bevor es zu einem Ausfall kommt.

>[!NOTE]
>Sollten Sie keinen Zugriff auf den Azure AD Connect-Server haben, oder sollte der Server keinen Zugriff auf das Internet haben, können Sie sich an [Microsoft-Support](https://support.microsoft.com/en-us/contactus/) wenden, um Unterstützung bei den Änderungen auf der Azure AD-Seite zu erhalten.

## <a name="prerequisites"></a>Voraussetzungen
Dieses Tutorial baut auf dem [Tutorial: Erstellen eines Verbunds einer Umgebung mit einer AD-Gesamtstruktur mit der Cloud](tutorial-federation.md) auf und ist Voraussetzung für die Bearbeitung des vorliegenden Tutorials.  Sofern noch nicht geschehen, bearbeiten Sie zunächst das genannte Tutorial, bevor Sie das vorliegende Dokument bearbeiten.

>[!IMPORTANT]
>Vor einem Wechsel zu PHS (Password Hash Sync, Kennworthashsynchronisierung) sollten Sie eine Sicherung Ihrer AD FS-Umgebung erstellen.  Dazu können Sie das [AD FS Rapid Restore-Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool) verwenden.

## <a name="enable-phs-in-azure-ad-connect"></a>Aktivieren der Kennworthashsynchronisierung in Azure AD Connect
Nachdem eine Azure AD Connect-Umgebung mit Verbund eingerichtet wurde, besteht der erste Schritt darin, die Kennworthashsynchronisierung zu aktivieren und Azure AD Connect das Synchronisieren der Hashes zu gestatten.

Gehen Sie wie folgt vor:

1.  Doppelklicken Sie auf das Azure AD Connect-Symbol, das auf dem Desktop erstellt wurde.
2.  Klicken Sie auf **Konfigurieren**.
3.  Wählen Sie auf der Seite „Weitere Aufgaben“ die Option **Synchronisierungsoptionen anpassen** aus, und klicken Sie auf **Weiter**.
4.  Geben Sie den Benutzernamen und das Kennwort für den globalen Administrator ein.  Sie haben dieses Konto [hier](tutorial-federation.md#create-a-global-administrator-in-azure-ad) im vorherigen Tutorial erstellt.
5.  Klicken Sie auf dem Bildschirm **Verzeichnisse verbinden** auf **Weiter**.
6.  Klicken Sie auf dem Bildschirm **Filtern von Domänen und Organisationseinheiten** auf **Weiter**.
7.  Wählen Sie auf dem Bildschirm **Optionale Features** die Option **Kennworthashsynchronisierung** aus, und klicken Sie auf **Weiter**.
![Auswählen](media/tutorial-phs-backup/backup1.png)</br>
8.  Klicken Sie auf dem Bildschirm **Bereit zur Konfiguration** auf **Konfigurieren**.
9.  Klicken Sie nach Abschluss der Konfiguration auf **Beenden**.
10. Das ist alles!  Fertig.  Die Kennworthashsynchronisierung wird jetzt durchgeführt und kann als Sicherung verwendet werden, falls AD FS nicht verfügbar ist.

## <a name="switch-to-password-hash-synchronization"></a>Umstellung auf die Kennworthashsynchronisierung
Nachfolgend wird veranschaulicht, wie Sie auf die Kennworthashsynchronisierung umstellen. Bevor Sie beginnen, überlegen Sie, unter welchen Bedingungen Sie die Umstellung vornehmen sollten. Nehmen Sie die Umstellung nicht aus vorübergehenden Gründen vor, wie einem Netzwerkausfall, einem kleinen Problem mit AD FS oder einem Problem, das eine Teilmenge Ihrer Benutzer betrifft. Wenn Sie sich für die Umstellung entscheiden, da das Beheben des Problems zu lange dauern würde, führen Sie folgende Schritte aus:

> [!IMPORTANT]
> Denken Sie daran, dass es einige Zeit dauert, bis die Kennworthashes mit Azure AD synchronisiert sind.  Es kann bis 3 Stunden dauern, bevor die Synchronisierung abgeschlossen ist und Sie mit Authentifizierung über die Kennworthashes beginnen können.

1. Doppelklicken Sie auf das Azure AD Connect-Symbol, das auf dem Desktop erstellt wurde.
2.  Klicken Sie auf **Konfigurieren**.
3.  Wählen Sie **Benutzeranmeldung ändern**, und klicken Sie auf **Weiter**.
![Änderung](media/tutorial-phs-backup/backup2.png)</br>
4.  Geben Sie den Benutzernamen und das Kennwort für den globalen Administrator ein.  Sie haben dieses Konto [hier](tutorial-federation.md#create-a-global-administrator-in-azure-ad) im vorherigen Tutorial erstellt.
5.  Wählen Sie auf dem Bildschirm **Benutzeranmeldung** die Option **Kennworthashsynchronisierung** aus, und setzen Sie ein Häkchen im Feld **Benutzerkonten nicht konvertieren**.  
6.  Lassen Sie die Einstellung **Einmaliges Anmelden aktivieren** aktiviert, und klicken Sie auf **Weiter**.
7.  Klicken Sie auf dem Bildschirm **Einmaliges Anmelden aktivieren** auf **Weiter**.
8.  Klicken Sie auf dem Bildschirm **Bereit zur Konfiguration** auf **Konfigurieren**.
9.  Klicken Sie nach Abschluss der Konfiguration auf **Beenden**.
10. Benutzer können jetzt ihre Kennwörter verwenden, um sich bei Azure und Azure-Diensten anzumelden.

## <a name="test-signing-in-with-one-of-our-users"></a>Testanmeldung mit einem unserer Benutzer

1. Rufen Sie [https://myapps.microsoft.com](https://myapps.microsoft.com) auf.
2. Melden Sie sich mit einem Benutzerkonto an, das in unserem neuen Mandanten erstellt wurde.  Sie müssen sich mit folgendem Format anmelden: (user@domain.onmicrosoft.com). Verwenden Sie dasselbe Kennwort, mit dem sich der Benutzer lokal anmeldet.</br>
   ![Überprüfen](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Rückkehr zum Verbund
Es wird nun gezeigt, wie eine Rückkehr zum Verbund erfolgt.  Führen Sie dazu folgende Schritte aus:

1.  Doppelklicken Sie auf das Azure AD Connect-Symbol, das auf dem Desktop erstellt wurde.
2.  Klicken Sie auf **Konfigurieren**.
3.  Wählen Sie **Benutzeranmeldung ändern**, und klicken Sie auf **Weiter**.
4.  Geben Sie den Benutzernamen und das Kennwort für den globalen Administrator ein.  Dies ist das Konto, das [hier](tutorial-federation.md#create-a-global-administrator-in-azure-ad) im vorherigen Tutorial erstellt wurde.
5.  Wählen Sie im Fenster **Benutzeranmeldung** die Option **Verbund mit AD FS** aus, und klicken Sie auf **Weiter**.  
6. Geben Sie auf der Seite mit den Domänenadministrator-Anmeldeinformationen den contoso\Administrator-Benutzernamen und das Kennwort ein, und klicken Sie auf **Weiter**.
7. Klicken Sie im Fenster „AD FS-Farm“ auf **Weiter**.
8. Wählen Sie im Fenster **Azure AD-Domäne** die Domäne in der Dropdownliste aus, und klicken Sie auf **Weiter**.
9. Klicken Sie auf dem Bildschirm **Bereit zur Konfiguration** auf **Konfigurieren**.
10. Klicken Sie nach Abschluss der Konfiguration auf **Weiter**.
![Konfigurieren](media/tutorial-phs-backup/backup4.png)</br>
11. Klicken Sie im Fenster **Verbundkonnektivität überprüfen** auf **Überprüfen**.  Sie müssen möglicherweise DNS-Einträge konfigurieren (A- und AAAA-Einträge hinzufügen), damit dieser Schritt erfolgreich ausgeführt werden kann.
![Überprüfen](media/tutorial-phs-backup/backup5.png)</br>
12. Klicken Sie auf **Beenden**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Zurücksetzen der Vertrauensstellung zwischen AD FS und Azure
Nun muss die Vertrauensstellung zwischen AD FS und Azure zurückgesetzt werden.

1.  Doppelklicken Sie auf das Azure AD Connect-Symbol, das auf dem Desktop erstellt wurde.
2.  Klicken Sie auf **Konfigurieren**.
3.  Wählen Sie **Verbund verwalten** aus, und klicken Sie auf **Weiter**.
4.  Wählen Sie **Azure AD-Vertrauensstellung zurücksetzen** aus, und klicken Sie auf **Weiter**.
![Zurücksetzen](media/tutorial-phs-backup/backup6.png)</br>
5.  Geben Sie im Fenster **Mit Azure AD verbinden** den Benutzernamen und das Kennwort für Ihren globalen Administrator ein.
6.  Geben Sie im Fenster **Mit Azure AD verbinden** den Benutzernamen und das Kennwort für „contoso\Administrator“ ein, und klicken Sie auf **Weiter**.
7.  Klicken Sie im Fenster **Zertifikate** auf **Weiter**.

## <a name="test-signing-in-with-one-of-our-users"></a>Testanmeldung mit einem unserer Benutzer

1.  Rufen Sie [https://myapps.microsoft.com](https://myapps.microsoft.com) auf.
2. Melden Sie sich mit einem Benutzerkonto an, das in unserem neuen Mandanten erstellt wurde.  Sie müssen sich mit folgendem Format anmelden: (user@domain.onmicrosoft.com). Verwenden Sie dasselbe Kennwort, mit dem sich der Benutzer lokal anmeldet.
![Überprüfen](media/tutorial-password-hash-sync/verify1.png)

Sie haben nun erfolgreich eine Hybrididentitätsumgebung eingerichtet, die Sie zum Testen verwenden können, und um sich mit den Möglichkeiten von Azure vertraut zu machen.

## <a name="next-steps"></a>Nächste Schritte


- [Voraussetzungen für Azure AD Connect](how-to-connect-install-prerequisites.md) 
- [Express-Einstellungen](how-to-connect-install-express.md)
- [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md)
