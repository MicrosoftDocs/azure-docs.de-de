---
title: Continuous Deployment für Azure Functions | Microsoft Docs
description: Verwenden der Continuous Deployment-Funktionen von Azure App Service, um Ihre Funktionen zu veröffentlichen.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 8fed6925d8c9341612c1f02ed20d83c492a68140
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807242"
---
# <a name="continuous-deployment-for-azure-functions"></a>Continuous Deployment für Azure Functions
Mit Azure Functions können Sie Ihre Funktions-App unter Verwendung von Continuous Integration problemlos bereitstellen. Functions integriert sich in die wichtigsten Coderepositorys und Bereitstellungsquellen. Diese Integration ermöglicht einen Workflow, bei dem Funktionscodeaktualisierungen, die durch einen dieser Dienste erfolgen, die Bereitstellung in Azure auslösen. Sollten Sie noch nicht mit Azure Functions vertraut sein, sehen Sie sich zuerst die [Übersicht zu Azure Functions](functions-overview.md)an.

Continuous Deployment ist hervorragend für Projekte geeignet, bei denen Sie häufig zahlreiche Beiträge integrieren. Außerdem behalten Sie die Kontrolle über den Quellcode Ihrer Funktionen. Azure Functions unterstützt die folgenden Bereitstellungsquellen:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Externes Repository (Git oder Mercurial)
* [Lokales Git-Repository](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

Bereitstellungen werden pro Funktionen-App konfiguriert. Nach Aktivierung von Continuous Deployment wird der Zugriff auf den Code im Portal auf *schreibgeschützt*festgelegt.

## <a name="requirements-for-continuous-deployment"></a>Anforderungen für Continuous Deployment

Bevor Sie Continuous Deployment einrichten, müssen Sie Ihre Bereitstellungsquelle und den darin enthaltenen Funktionscode konfiguriert haben. In der Bereitstellung einer Funktions-App befindet sich jede Funktion in einem nach der Funktion benannten Unterverzeichnis.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Damit eine Bereitstellung über Azure DevOps möglich ist, müssen Sie zuerst Ihre Azure DevOps-Organisation mit Ihrem Azure-Abonnement verknüpfen. Weitere Informationen finden Sie unter [Einrichten der Abrechnung für Ihre Azure DevOps-Organisation](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs#set-up-billing).

## <a name="set-up-continuous-deployment"></a>Einrichten der fortlaufenden Bereitstellung
Gehen Sie wie folgt vor, um Continuous Deployment für eine vorhandene Funktionen-App zu konfigurieren. Die folgenden Schritte beziehen sich auf die Integration mit einem GitHub-Repository. Für Azure DevOps und andere Bereitstellungsdienste gelten jedoch ähnliche Schritte.

1. Wählen Sie in Ihrer Funktions-App im [Azure-Portal](https://portal.azure.com) **Plattformfeatures** > **Bereitstellungsoptionen** aus. 
   
    ![Auswahlmöglichkeiten für das Öffnen von Bereitstellungsoptionen](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Wählen Sie auf dem Blatt **Bereitstellungen** den Befehl **Setup** aus.
 
    ![Blatt „Bereitstellungen“](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Wählen Sie auf dem Blatt **Bereitstellungsquelle** die Option **Quelle auswählen** aus. Geben Sie die Informationen für Ihre ausgewählte Bereitstellungsquelle ein, und wählen Sie anschließend **OK** aus.
   
    ![Auswählen einer Bereitstellungsquelle](./media/functions-continuous-deployment/choose-deployment-source.png)

Nachdem Sie Continuous Deployment eingerichtet haben, werden alle Dateiänderungen in Ihrer Bereitstellungsquelle in die Funktions-App kopiert, und es wird eine vollständige Websitebereitstellung ausgelöst. Wenn Dateien in der Quelle aktualisiert werden, wird die Website neu bereitgestellt.

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

Typische Bereitstellungsszenarien umfassen das Erstellen einer Stagingbereitstellung sowie das Verschieben vorhandener Funktionen in Continuous Deployment.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Erstellen einer Stagingbereitstellung

Funktions-Apps unterstützen noch keine Bereitstellungsslots. Aber Sie können immer noch die gesonderte Staging- und Produktionsbereitstellungen verwalten, indem Sie Continuous Integration verwenden.

Der Prozess zum Konfigurieren und Verwenden einer Stagingbereitstellung sieht im Grunde wie folgt aus:

1. Erstellen Sie in Ihrem Abonnement zwei Funktions-Apps: eine für den Produktionscode und eine für das Staging. 

1. Erstellen Sie eine Bereitstellungsquelle, sofern noch nicht vorhanden. In diesem Beispiel wird [GitHub]verwendet.

1. Führen Sie für Ihre für die Produktionsumgebung vorgesehene Funktionen-App die vorherigen unter [Einrichten von Continuous Deployment](#set-up-continuous-deployment) angegebenen Schritte aus, und legen Sie die Bereitstellungsverzweigung auf die Hauptverzweigung Ihres GitHub-Repositorys fest.
   
    ![Möglichkeiten für die Auswahl einer Bereitstellungsverzweigung](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Wiederholen Sie Schritt 3 für das Staging der Funktions-App, aber wählen Sie stattdessen die Stagingverzweigung in Ihrem GitHub-Repository aus. Falls Ihre Bereitstellungsquelle keine Verzweigung unterstützt, verwenden Sie einen anderen Ordner.
    
1. Aktualisieren Sie Ihren Code in der Stagingverzweigung oder im Stagingordner, und vergewissern Sie sich anschließend, dass die Änderungen in der Stagingbereitstellung berücksichtigt wurden.

1. Führen Sie nach dem Testen die Änderungen aus der Stagingverzweigung in der Hauptverzweigung zusammen. Dadurch wird die Bereitstellung für die Funktionen-App in der Produktionsumgebung ausgelöst. Falls Ihre Bereitstellungsquelle keine Verzweigungen unterstützt, überschreiben Sie die Dateien im Produktionsordner mit den Dateien aus dem Stagingordner.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Verschieben vorhandener Funktionen in Continuous Deployment
Wenn Sie über Funktionen verfügen, die Sie im Portal erstellt und verwaltet haben, müssen Sie Ihre Funktionscodedateien per FTP oder aus dem lokalen Git-Repository herunterladen, um Continuous Deployment wie zuvor beschrieben einrichten zu können. Dies ist über die Azure App Service-Einstellungen für Ihre Funktions-App möglich. Die heruntergeladenen Dateien können Sie in Ihre gewünschte Continuous Deployment-Quelle hochladen.

> [!NOTE]
> Nach dem Konfigurieren von Continuous Integration können Sie Ihre Quelldateien im Functions-Portal nicht mehr bearbeiten.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>Konfigurieren von Anmeldeinformationen für die Bereitstellung
Bevor Sie über FTP oder ein lokales Git-Repository Dateien aus Ihrer Funktions-App herunterladen können, müssen Sie Ihre Anmeldeinformationen für den Websitezugriff angeben. Anmeldeinformationen werden auf Ebene der Funktions-App festgelegt. Legen Sie die Anmeldeinformationen für die Bereitstellung im Azure-Portal mit den folgenden Schritten fest:

1. Wählen Sie in Ihrer Funktions-App im [Azure-Portal](https://portal.azure.com) **Plattformfeatures** > **Anmeldeinformationen für Bereitstellung** aus.
   
1. Geben Sie einen Benutzernamen und ein Kennwort ein, und wählen Sie anschließend **Speichern** aus. 

   ![Auswahlmöglichkeiten für das Festlegen von Anmeldeinformationen für lokale Bereitstellungen](./media/functions-continuous-deployment/setup-deployment-credentials.png)

Nun können Sie unter Verwendung dieser Anmeldeinformationen per FTP oder über das integrierte Git-Repository auf Ihre Funktionen-App zugreifen.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Herunterladen von Dateien per FTP

1. Wählen Sie in Ihrer Funktions-App im [Azure-Portal](https://portal.azure.com) **Plattformfeatures** > **Eigenschaften** aus. Kopieren Sie dann die Werte für **FTP/Bereitstellungsbenutzer**, **FTP-Hostname** und **FTPS-Hostname**.  

   **FTP/Bereitstellungsbenutzer** muss wie im Portal angezeigt eingegeben werden (einschließlich App-Name), um den richtigen Kontext für den FTP-Server bereitzustellen.
   
   ![Auswahlmöglichkeiten für das Abrufen Ihrer Bereitstellungsinformationen](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. Verwenden Sie im FTP-Client die gesammelten Verbindungsinformationen, um eine Verbindung mit Ihrer App herzustellen und die Quelldateien für Ihre Funktionen herunterzuladen.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Herunterladen von Dateien mithilfe eines lokalen Git-Repositorys

1. Wählen Sie in Ihrer Funktions-App im [Azure-Portal](https://portal.azure.com) **Plattformfeatures** > **Bereitstellungsoptionen** aus. 
   
    ![Auswahlmöglichkeiten für das Öffnen von Bereitstellungsoptionen](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Wählen Sie dann auf dem Blatt **Bereitstellungen** den Befehl **Setup** aus.
 
    ![Blatt „Bereitstellungen“](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Wählen Sie auf dem Blatt **Bereitstellungsquelle** die Option **Lokales Git-Repository** > **OK** aus.

1. Wählen Sie in **Plattformfeatures** **Eigenschaften** aus, und notieren Sie sich den Wert der Git-URL. 
   
    ![Auswahlmöglichkeiten für das Abrufen der Git-URL](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Klonen Sie das Repository auf Ihrem lokalen Computer mithilfe einer Git-fähigen Befehlszeile oder mit Ihrem bevorzugten Git-Tool. Der Befehl zum Klonen von Git sieht wie folgt aus:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Rufen Sie Dateien aus Ihrer Funktionen-App in den Klon auf Ihrem lokalen Computer ab, wie im folgenden Beispiel zu sehen:
   
        git pull origin master
   
    Geben Sie bei eventueller Aufforderung Ihre [konfigurierten Anmeldeinformationen für die Bereitstellung](#credentials) an.  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bewährte Methoden für Azure Functions](functions-best-practices.md)
