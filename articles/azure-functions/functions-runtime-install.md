---
title: Azure Functions Runtime-Installation | Microsoft-Dokumentation
description: Installieren der Vorschauversion 2 der Azure Functions-Laufzeit
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: aae6bc41f3c2fc2c5f8cf63d07f6b4d79bb3564a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61022864"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Installieren der Vorschauversion 2 der Azure Functions-Laufzeit

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Wenn Sie die Vorschauversion 2 der Azure Functions-Laufzeit installieren möchten, führen Sie die folgenden Schritte aus:

1. Stellen Sie sicher, dass Ihr Computer die Mindestanforderungen erfüllt.
1. Laden Sie das [Installationsprogramm für Azure Functions Runtime Preview](https://aka.ms/azafrv2) herunter.
1. Deinstallieren Sie die Vorschauversion 1 der Azure Functions-Laufzeit.
1. Deinstallieren Sie die Vorschauversion 2 der Azure Functions-Laufzeit.
1. Schließen Sie die Konfiguration der Vorschauversion 2 der Azure Functions-Laufzeit ab.
1. Erstellen Ihrer ersten Funktion in der Vorschauversion der Azure Functions-Laufzeit

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Vorschauversion der Azure Functions-Laufzeit installieren, benötigen Sie die folgenden Ressourcen:

1. Einen Computer unter Microsoft Windows Server 2016 oder Microsoft Windows 10 Creators Update (Professional oder Enterprise Edition).
1. Eine SQL Server-Instanz, die in Ihrem Netzwerk ausgeführt wird.  Sie müssen mindestens SQL Server Express verwenden.

## <a name="uninstall-previous-version"></a>Deinstallieren der vorherigen Version

Wenn Sie zuvor die Vorschauversion der Azure Functions-Laufzeit installiert haben, müssen Sie diese deinstallieren, bevor Sie die neueste Version installieren können.  Deinstallieren Sie die Vorschauversion der Azure Functions-Laufzeit, indem Sie das Programm in Windows unter „Software“ entfernen.

## <a name="install-the-azure-functions-runtime-preview"></a>Installieren von Azure Functions Runtime Preview

Das Installationsprogramm für die Vorschauversion von Azure Functions Runtime führt Sie durch die Installation der Verwaltungs- und Workerrollen der Vorschauversion von Azure Functions Runtime.  Es ist möglich, die Verwaltungs- und Workerrolle auf einem Computer zu installieren.  Wenn Sie allerdings weitere Funktions-Apps hinzufügen, müssen Sie weitere Workerrollen auf zusätzlichen Computern bereitstellen, um die Funktionen auf mehrere Worker skalieren zu können.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installieren der Verwaltungs- und Workerrolle auf einem Computer

1. Führen Sie das Installationsprogramm für Azure Functions Runtime Preview aus.

    ![Installationsprogramm für die Vorschauversion der Azure Functions-Laufzeit][1]

1. Klicken Sie auf **Weiter**.
1. Nachdem Sie die **Lizenzbedingungen** gelesen haben, **aktivieren Sie das Kontrollkästchen** zum Akzeptieren der Lizenzbedingungen, und klicken Sie auf **Weiter**, um fortzufahren.
1. Wählen Sie die Rollen aus, die Sie auf diesem Computer installieren möchten: **Functions-Verwaltungsrolle** und/oder **Functions-Workerrolle**. Klicken Sie dann auf **Weiter**.

    ![Installationsprogramm für die Vorschauversion der Azure Functions-Laufzeit – Rollenauswahl][3]

    > [!NOTE]
    > Sie können die **Functions-Workerrolle** auf vielen anderen Computern installieren. Befolgen Sie dazu diese Anweisungen, und wählen Sie nur **Functions-Workerrolle**  im Installationsprogramm aus.

1. Klicken Sie auf **Weiter**, damit der **Setup-Assistent für Azure Functions Runtime** mit der Installation auf dem Computer beginnt.
1. Nach Abschluss startet der Setup-Assistent das Konfigurationstool für die **Azure Functions-Laufzeit**.

    ![Abschluss des Installationsprogramms für die Vorschauversion der Azure Functions-Laufzeit][6]

    > [!NOTE]
    > Bei einer Installation unter **Windows 10** und ohne vorherige Aktivierung des Features **Container** fordert das Setupprogramm für **Azure Functions Runtime** Sie auf, den Computer zum Abschließen der Installation neu starten.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurieren von Azure Functions Runtime

Zum Abschließen der Installation der Azure Functions-Laufzeit müssen Sie die Konfiguration abschließen.

1. Das Konfigurationstool für **Azure Functions Runtime** zeigt an, welche Rollen auf dem Computer installiert sind.

    ![Konfigurationstool für die Vorschauversion der Azure Functions-Laufzeit][7]

1. Klicken Sie auf die Registerkarte **Datenbank**, geben Sie die Verbindungsdetails für die SQL Server-Instanz ein, einschließlich der Angabe eines [Datenbankhauptschlüssels](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), und klicken Sie auf **Übernehmen**.  Die Verbindung mit einer SQL Server-Instanz ist erforderlich, damit die Azure Functions-Laufzeit eine Datenbank zur Unterstützung der Laufzeit erstellt.

    ![Datenbankkonfiguration für die Vorschauversion der Azure Functions-Laufzeit][8]

1. Klicken Sie auf die Registerkarte **Anmeldeinformationen**.  Hier müssen Sie zwei neue Anmeldeinformationen für die Verwendung mit einer Dateifreigabe erstellen, die alle Ihre Functions-Apps hostet.  Geben Sie Kombinationen aus **Benutzername** und **Kennwort** für den **Besitzer der Dateifreigabe** und für den **Benutzer der Dateifreigabe** an, und klicken Sie auf **Übernehmen**.

    ![Anmeldeinformationen für die Vorschauversion der Azure Functions-Laufzeit][9]

1. Klicken Sie auf die Registerkarte **Dateifreigabe**.  Hier geben Sie die Details für den Speicherort der Dateifreigabe an.  Diese Dateifreigabe kann für Sie erstellt werden, oder Sie können eine vorhandene Dateifreigabe verwenden. Klicken Sie dann auf **Übernehmen**.  Wenn Sie eine neue Dateifreigabe auswählen, müssen Sie ein Verzeichnis angeben, das von Azure Functions Runtime verwendet werden soll.

    ![Dateifreigabe für die Vorschauversion der Azure Functions-Laufzeit][10]

1. Klicken Sie auf die Registerkarte **IIS**.  Diese Registerkarte zeigt die Details der Websites in IIS an, die durch das Konfigurationstool der Azure Functions-Laufzeit erstellt werden.  Sie können hier einen benutzerdefinierten DNS-Namen für das Portal der Vorschauversion der Azure Functions-Laufzeit angeben.  Klicken Sie zum Abschließen auf **Übernehmen**.

    ![IIS für die Vorschauversion der Azure Functions-Laufzeit][11]

1. Klicken Sie auf die Registerkarte **Dienste**.  Diese Registerkarte zeigt den Status der Dienste im Azure Functions Runtime-Konfigurationstool an.  Wenn nach der Erstkonfiguration der **Hostaktivierungsdienst für Azure Functions** nicht ausgeführt wird, klicken Sie auf **Dienst starten**.

    ![Konfigurationstool für die Vorschauversion der Azure Functions-Laufzeit][12]

1. Navigieren Sie zum **Portal für die Azure Functions-Laufzeit** unter `https://<machinename>.<domain>/`.

    ![Portal für die Vorschauversion der Azure Functions-Laufzeit][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Erstellen Ihrer ersten Funktion in der Vorschauversion der Azure Functions-Laufzeit

So erstellen Sie Ihre erste Funktion in der Vorschauversion der Azure Functions-Laufzeit

1. Navigieren Sie zum **Portal für die Azure Functions-Runtime** unter „`https://<machinename>.<domain>`“, z. B. `https://mycomputer.mydomain.com`.

1. Sie werden zur **Anmeldung** aufgefordert. Wenn Sie in einer Domäne bereitgestellt werden, verwenden Sie Ihren Benutzernamen und das zugehörige Kennwort für das Domänenkonto. Andernfalls verwenden Sie Ihren Benutzernamen und das Kennwort für Ihr lokales Konto, um sich am Portal anzumelden.

    ![Anmeldung am Portal für die Vorschauversion der Azure Functions-Laufzeit][14]

1. Um Funktions-Apps zu erstellen, müssen Sie ein Abonnement erstellen.  Klicken Sie in der oberen linken Ecke des Portals auf die Option **+** neben den Abonnements.

    ![Portalabonnements für die Vorschauversion der Azure Functions-Laufzeit][15]

1. Wählen Sie **DefaultPlan** aus, geben Sie einen Namen für Ihr Abonnement ein, und klicken Sie auf **Erstellen**.

    ![Portalabonnementplan und -name für die Vorschauversion der Azure Functions-Laufzeit][16]

1. Ihre Funktions-Apps werden im linken Bereich des Portals aufgelistet.  Um eine neue Funktions-App zu erstellen, wählen Sie die Überschrift **Funktions-Apps** aus und klicken Sie auf die Option **+** .

1. Geben Sie einen Namen für Ihre Funktions-App ein und wählen Sie das richtige Abonnement aus. Wählen Sie dann die Version der Azure Functions-Laufzeit aus, für die Sie programmieren möchten, und klicken Sie auf **Erstellen**.

    ![Neue Funktions-App auf dem Portal für die Vorschauversion der Azure Functions-Laufzeit][17]

1. Ihre neue Funktions-App ist im linken Bereich des Portals aufgeführt.  Wählen Sie „Funktionen“ aus und klicken Sie dann am oberen Rand des mittleren Bereichs des Portals auf **Neue Funktion**.

    ![Vorlagen der Vorschauversion der Azure Functions-Laufzeit][18]

1. Wählen Sie die Funktion „Timer Trigger“ im rechten Flyout aus, benennen Sie Ihre Funktion und ändern Sie den Zeitplan in `*/5 * * * * *` (dieser CRON-Ausdruck bewirkt, dass die Timerfunktion alle fünf Sekunden ausgeführt wird). Klicken Sie dann auf **Erstellen**.

    ![Konfiguration der neuen Timerfunktion für die Vorschauversion der Azure Functions-Laufzeit][19]

1. Ihre Funktion wurde jetzt erstellt.  Sie können das Ausführungsprotokoll Ihrer Funktions-App anzeigen, indem Sie den Bereich **Protokoll** am unteren Rand des Portals erweitern.

    ![Funktionsausführung für die Vorschauversion der Azure Functions-Laufzeit][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
