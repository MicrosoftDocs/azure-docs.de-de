---
title: Verschieben einer Azure AD Connect-Datenbank von SQL Server Express zu SQL Server | Microsoft-Dokumentation
description: In diesem Dokument wird beschrieben, wie Sie die Azure AD Connect-Datenbank vom lokalen SQL Server Express-Server auf einen SQL Server-Remoteserver verschieben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ae0e87fddabee9f42cbb5506dce4cd7a5f4f082
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64918855"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Verschieben einer Azure AD Connect-Datenbank von SQL Server Express zu SQL Server 

In diesem Dokument wird beschrieben, wie Sie die Azure AD Connect-Datenbank vom lokalen SQL Server Express-Server auf einen SQL Server-Remoteserver verschieben.  Sie können für diese Aufgabe die unten aufgeführten Verfahren verwenden.

## <a name="about-this-scenario"></a>Informationen zu diesem Szenario
Nachfolgend finden Sie einige kurze Informationen zu diesem Szenario.  In diesem Szenario wird die Azure AD Connect-Version (1.1.819.0) auf einem einzelnen Windows Server 2016-Domänencontroller installiert.  Er verwendet die integrierte Version „SQL Server 2012 Express Edition“ als Datenbank.  Die Datenbank wird auf einen Server mit SQL Server 2017 verschoben.

![Szenarioarchitektur](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Verschieben der Azure AD Connect-Datenbank
Führen Sie die folgenden Schritte aus, um die Azure AD Connect-Datenbank auf einen Remoteserver mit SQL Server zu verschieben.

1. Navigieren Sie auf dem Azure AD Connect-Server zu **Dienste**, und beenden Sie den Dienst **Microsoft Azure AD Sync**.
2. Navigieren Sie zum Ordner **%Program Files%\Microsoft Azure AD Sync/Data/** , und kopieren Sie die Dateien **ADSync.mdf** und **ADSync_log.ldf** auf den SQL Server-Remoteserver.
3. Starten Sie den Dienst **Microsoft Azure AD Sync** auf dem Azure AD Connect-Server neu.
4. Deinstallieren Sie Azure AD Connect über die Systemsteuerung: „Programme“ > „Programme und Features“.  Wählen Sie Microsoft Azure AD Connect aus, und klicken Sie oben auf „Deinstallieren“.
5. Öffnen Sie auf dem SQL Server-Remoteserver SQL Server Management Studio.
6. Klicken Sie mit der rechten Maustaste auf „Datenbanken“, und wählen Sie „Anfügen“.
7. Klicken Sie im Bildschirm **Datenbanken anfügen** auf **Hinzufügen**, und navigieren Sie zur Datei „ADSync.mdf“.  Klicken Sie auf **OK**.
   ![Anfügen einer Datenbank](media/how-to-connect-install-move-db/move2.png)

8. Wenn die Datenbank angefügt wurde, wechseln Sie zurück zum Azure AD Connect-Server, und installieren Sie Azure AD Connect.
9. Sobald die MSI-Installation abgeschlossen ist, wird der Azure AD Connect-Assistent mit dem Setup im Express-Modus gestartet. Schließen Sie den Bildschirm durch Klicken auf das Symbol „Beenden“.
   ![Willkommen](./media/how-to-connect-install-move-db/db1.png)
10. Rufen Sie eine neue Eingabeaufforderung oder PowerShell-Sitzung auf. Navigieren Sie zum Ordner „\<Laufwerk>\Programme\Microsoft Azure AD Connect“. Führen Sie den Befehl „.\AzureADConnect.exe /useexistingdatabase“ aus, um den Azure AD Connect-Assistenten im Setupmodus „Vorhandene Datenbank verwenden“ auszuführen.
    ![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. Sie werden mit dem Bildschirm „Willkommen bei Azure AD Connect“ begrüßt. Wenn Sie den Lizenzbedingungen und dem Datenschutzhinweis zugestimmt haben, klicken Sie auf **Fortfahren**.
    ![Willkommen](./media/how-to-connect-install-move-db/db3.png)
12. Auf dem Bildschirm **Erforderliche Komponenten installieren** ist die Option **Vorhandenen SQL Server-Computer verwenden** aktiviert. Geben Sie den Namen der SQL-Server-Instanz an, auf der die ADSync-Datenbank gehostet wird. Wenn die zum Hosten der ADSync-Datenbank verwendete SQL Engine-Instanz nicht die Standardinstanz auf dem SQL Server-Computer ist, müssen Sie den Namen der SQL Engine-Instanz angeben. Darüber hinaus müssen Sie, wenn das SQL-Durchsuchen nicht aktiviert ist, auch die Portnummer der SQL Engine-Instanz angeben. Beispiel:         
    ![Willkommen](./media/how-to-connect-install-move-db/db4.png)           

13. Auf dem Bildschirm **Mit Azure AD verbinden** müssen Sie die Anmeldeinformationen als globaler Administrator Ihres Azure AD-Verzeichnisses angeben. Sie sollten ein Konto in der standardmäßigen Domäne „onmicrosoft.com“ verwenden. Dieses Konto dient ausschließlich der Erstellung eines Dienstkontos in Azure AD und wird nach Abschluss des Assistenten nicht mehr verwendet.
    ![Herstellen einer Verbindung](./media/how-to-connect-install-move-db/db5.png)
 
14. Auf dem Bildschirm **Verzeichnisse verbinden** wird die für die Verzeichnissynchronisierung konfigurierte vorhandene AD-Gesamtstruktur mit einem roten Kreuzsymbol aufgelistet. Zum Synchronisieren der Änderungen von einer lokalen AD-Gesamtstruktur ist ein AD DS-Konto erforderlich. Der Azure AD Connect-Assistent kann nicht die Anmeldeinformationen des in der ADSync-Datenbank gespeicherten AD DS-Kontos abrufen, da die Anmeldeinformationen verschlüsselt sind und nur von der vorherigen Azure AD Connect-Serverinstanz entschlüsselt werden können. Klicken Sie auf **Anmeldeinformationen ändern**, um das AD DS-Konto für die AD-Gesamtstruktur anzugeben.
    ![Verzeichnisse](./media/how-to-connect-install-move-db/db6.png)
 
 
15. Im Popupdialogfeld können Sie entweder (i) eine Enterprise-Administratoranmeldeinfo eingeben und Azure AD Connect das AD DS-Konto für Sie erstellen lassen, oder (ii) das AD DS-Konto selbst erstellen und Azure AD Connect die Anmeldeinformationen angeben. Nachdem Sie eine Option ausgewählt und die erforderlichen Anmeldeinformationen angeben haben, klicken Sie auf **OK**, um das Popupdialogfeld zu schließen.
    ![Willkommen](./media/how-to-connect-install-move-db/db7.png)
 
 
16. Sobald die Anmeldeinformationen bereitgestellt sind, wird das rote Kreuzsymbol durch ein grünes Häkchensymbol ersetzt. Klicken Sie auf **Weiter**.
    ![Willkommen](./media/how-to-connect-install-move-db/db8.png)
 
 
17. Klicken Sie auf dem Bildschirm **Bereit zur Konfiguration** auf **Installieren**.
    ![Willkommen](./media/how-to-connect-install-move-db/db9.png)
 
 
18. Nachdem die Installation abgeschlossen ist, wird die Azure AD Connect-Serverinstanz automatisch für den Stagingmodus aktiviert. Sie sollten die Serverkonfiguration und ausstehenden Exporte vor dem Deaktivieren des Stagingmodus auf unerwartete Änderungen überprüfen. 

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
- [Installieren von Azure AD Connect mithilfe einer vorhandenen ADSync-Datenbank](how-to-connect-install-existing-database.md)
- [Installieren von Azure AD Connect mit delegierten SQL-Administratorberechtigungen](how-to-connect-install-sql-delegation.md)

