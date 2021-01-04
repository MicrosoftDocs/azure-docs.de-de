---
title: Verwalten von Azure Analysis Services | Microsoft Docs
description: In diesem Artikel werden die Tools beschrieben, die zur Verwaltung von Administrations- und Verwaltungsaufgaben für einen Azure Analysis Services-Server verwendet werden.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 91ffcac98b2b919a8fc131d235e699aad4fa215d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078899"
---
# <a name="manage-analysis-services"></a>Verwalten von Analysis Services
Nachdem Sie in Azure einen Analysis Services-Server erstellt haben, müssen Sie möglicherweise sofort oder zu einem späteren Zeitpunkt einige Verwaltungsaufgaben ausführen. Sie können z. B. Daten aktualisieren, steuern, wem Zugriff auf die Modelle auf dem Server gewährt wird, oder die Integrität des Servers überwachen. Einige Verwaltungsaufgaben können nur im Azure-Portal, andere in SQL Server Management Studio (SSMS) und bestimmte sowohl im Portal als auch in SSMS ausgeführt werden.

## <a name="azure-portal"></a>Azure-Portal
Im [Azure-Portal](https://portal.azure.com/) können Sie Server erstellen und löschen, Serverressourcen überwachen, die Größe ändern und steuern, wer Zugriff auf die Server hat.  Wenn Probleme auftreten, können Sie eine Supportanfrage senden.

![Screenshot: Zeigt, wo Sie im Azure-Portal Server erstellen und löschen, Serverressourcen überwachen, die Größe ändern und den Zugriff auf Ihre Server steuern können](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Das Herstellen einer Verbindung mit dem Server in Azure entspricht dem Herstellen einer Verbindung mit einer Serverinstanz in der eigenen Organisation. In SSMS können Sie viele der gleichen Aufgaben ausführen, z. B. Verarbeiten von Daten oder Erstellen eines Verarbeitungsskripts, Verwalten von Rollen und Verwenden von PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Herunterladen und Installieren von SSMS
Verwenden Sie die neueste Version von SSMS, um alle aktuellen Features zu erhalten und die Verbindung mit dem Azure Analysis Services-Server reibungslos herzustellen. 

[Laden Sie SQL Server Management Studio herunter](/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>So stellen Sie eine Verbindung mit SSMS her
 Wenn Sie SSMS verwenden, stellen Sie sicher, dass Ihr Benutzername in der Analysis Services-Administratoren-Gruppe enthalten ist, bevor Sie das erste Mal eine Verbindung mit dem Server herstellen. Weitere Informationen finden Sie im weiteren Verlauf dieses Artikels unter [Serveradministratoren und Datenbankbenutzer](#server-administrators-and-database-users).

1. Bevor Sie eine Verbindung herstellen, müssen Sie den Namen des Servers abrufen. Kopieren Sie den Servernamen über **Azure-Portal** > Server > **Übersicht** > **Servername**.
   
    ![Abrufen von Servernamen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Klicken Sie in SSMS im **Objekt-Explorer** auf **Verbinden** > **Analysis Services**.
3. Fügen Sie im Dialogfeld **Mit Server verbinden** den Servernamen ein, und wählen Sie dann in **Authentifizierung** einen der folgenden Authentifizierungstypen aus:   
    > [!NOTE]
    > Es empfiehlt sich der Authentifizierungstyp **Active Directory: universell mit MFA-Unterstützung**.

    > [!NOTE]
    > Wenn Sie sich mit einem Microsoft-Konto, Live ID, Yahoo, Gmail usw. anmelden, lassen Sie das Kennwortfeld leer. Nachdem Sie auf „Verbinden“ geklickt haben, werden Sie zur Eingabe des Kennworts aufgefordert.

    **Windows-Authentifizierung** , um Ihre Windows-Domäne\Ihren Benutzernamen und Ihr Kennwort als Anmeldeinformationen zu verwenden.

    **Active Directory-Kennwortauthentifizierung** , um ein Organisationskonto zu verwenden. Dies ist beispielsweise beim Herstellen einer Verbindung von einem nicht mit der Domäne verbundenen Computer sinnvoll.

    **Active Directory: universell mit MFA-Unterstützung** , wenn [nicht interaktive oder mehrstufige Authentifizierung](../azure-sql/database/authentication-mfa-ssms-overview.md) verwendet werden soll. 
   
    ![Herstellen der Verbindung in SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Serveradministratoren und Datenbankbenutzer
In Azure Analysis Services gibt es zwei Benutzertypen: Serveradministratoren und Datenbankbenutzer. Beide Benutzertypen müssen in Ihrer Azure Active Directory-Instanz vorhanden sein und durch eine Organisations-E-Mail-Adresse oder einen UPN angegeben werden. Weitere Informationen finden Sie unter [Authentifizierung und Benutzerberechtigungen](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Beheben von Verbindungsproblemen
Wenn beim Herstellen einer Verbindung mithilfe von SSMS Probleme auftreten, müssen Sie den Anmeldecache löschen. Auf dem Datenträger wird nichts zwischengespeichert. Beenden Sie den Verbindungsvorgang, und starten Sie ihn neu, um den Cache zu löschen. 

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie auf dem neuen Server noch kein tabellarisches Modell bereitgestellt haben, ist jetzt ein guter Zeitpunkt dafür. Weitere Informationen finden Sie unter [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Bereitstellen in Azure Analysis Services, in englischer Sprache).

Wenn Sie ein Modell auf dem Server bereitgestellt haben, können Sie mithilfe eines Clients oder Browsers eine Verbindung mit diesem herstellen. Weitere Informationen finden Sie unter [Get data from Azure Analysis Services](analysis-services-connect.md) (Abrufen von Daten aus Azure Analysis Services, in englischer Sprache).