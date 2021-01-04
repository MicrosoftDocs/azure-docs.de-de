---
title: Einrichten einer Instanz und der Authentifizierung (per Skript)
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie eine Instanz des Azure Digital Twins-Diensts einrichten, indem Sie ein automatisiertes Bereitstellungsskript ausführen.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5806ea094abd3431cd7e22064c6acd8ad150726a
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495012"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Einrichten einer Azure Digital Twins-Instanz und -Authentifizierung (per Skript)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Dieser Artikel beschreibt die Schritte zum **Einrichten einer neuen Azure Digital Twins-Instanz** , einschließlich des Erstellens der Instanz und des Einrichtens der Authentifizierung. Nachdem Sie die Schritte in diesem Artikel durchgeführt haben, verfügen Sie über eine Azure Digital Twins-Instanz, die zum Programmieren bereitsteht.

Bei dieser Version dieses Artikels werden die Schritte durchgeführt, indem ein [Beispiel für ein **automatisiertes Bereitstellungsskript**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) zum Optimieren des Prozesses ausgeführt wird. 
* Die manuellen CLI-Schritte, die das Skript im Hintergrund durchläuft, finden Sie in der CLI-Version dieses Artikels: [*Verwenden Einrichten einer Instanz und Authentifizierung (CLI)*](how-to-set-up-instance-cli.md).
* Eine Beschreibung der manuellen Schritte im Azure-Portal finden Sie in der Portalversion dieses Artikels: [*Verwenden Einrichten einer Instanz und Authentifizierung (Portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Voraussetzungen: Herunterladen des Skripts

Das Beispielskript ist in PowerShell geschrieben. Es ist Teil der [**End-to-End-Beispiele für Azure Digital Twins**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), die Sie auf Ihren Computer herunterladen können, indem Sie dem Beispiellink folgen und unterhalb des Titels auf die Schaltfläche *ZIP herunterladen* klicken.

Dadurch wird das Beispielprojekt als _**Azure_Digital_Twins_end_to_end_samples.zip**_ auf Ihren Computer heruntergeladen. Navigieren Sie zu dem Ordner auf Ihrem Computer, und entpacken Sie ihn, um die Dateien zu extrahieren.

Im entpackten Ordner befindet sich das Bereitstellungsskript unter _Azure_Digital_Twins_end_to_end_samples > scripts > **deploy.ps1**_.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Ausführen des Bereitstellungsskripts

In diesem Artikel wird ein Azure Digital Twins-Codebeispiel verwendet, um eine Azure Digital Twins-Instanz und die erforderliche Authentifizierung halbautomatisch bereitzustellen. Er kann zudem als Startpunkt zum Schreiben Ihrer eigenen Skriptinteraktionen dienen.

Im Folgenden sind die Schritte zum Ausführen des Bereitstellungsskripts in Cloud Shell beschrieben.
1. Öffnen Sie in Ihrem Browser ein [Azure Cloud Shell](https://shell.azure.com/)-Fenster. Melden Sie sich mithilfe des folgenden Befehls an:
    ```azurecli-interactive
    az login
    ```
    Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite. Öffnen Sie andernfalls die Browserseite *https://aka.ms/devicelogin* , und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein.
 
2. Stellen Sie in der Cloud Shell-Symbolleiste sicher, dass Ihre Cloud Shell für die Ausführung der PowerShell-Version festgelegt ist.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell-Fenster: Auswahl der PowerShell-Version":::

1. auf das Symbol „Dateien hochladen/herunterladen“ und dann auf „Hochladen“.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell-Fenster: Auswahl des Symbols „Hochladen“":::

    Navigieren Sie zur Datei _**deploy.ps1**_ auf Ihrem Computer (in _Azure_Digital_Twins_end_to_end_samples > scripts > **deploy.ps1**_ ), und klicken Sie auf „Öffnen“. Dadurch wird die Datei in Cloud Shell hochgeladen, sodass Sie diese im Cloud Shell-Fenster ausführen können.

4. Führen Sie das Skript aus, indem Sie im Cloud Shell-Fenster den Befehl `./deploy.ps1` absenden. Sie können den Befehl unten kopieren (denken Sie daran, dass Sie zum Einfügen in Cloud Shell **STRG+UMSCHALT+V** unter Windows und Linux oder **CMD+UMSCHALT+V** unter macOS verwenden können. Sie können auch das Kontextmenü verwenden).

    ```azurecli-interactive
    ./deploy.ps1
    ```

    Dieses Skript erstellt eine Azure Digital Twins-Instanz und weist Ihrem Azure-Benutzer die Rolle *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) für die Instanz zu.

    Während das Skript die automatisierten Einrichtungsschritte durchläuft, werden Sie dazu aufgefordert, die folgenden Werte zu übergeben:
    * Für die Instanz: die *Abonnement-ID* Ihres zu verwendenden Azure-Abonnements.
    * Für die Instanz: einen *Speicherort* , an dem Sie die Instanz bereitstellen möchten. Informationen zu Regionen mit Unterstützung von Azure Digital Twins finden Sie unter [*Verfügbare Produkte nach Region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Für die Instanz: einen Namen für die *Ressourcengruppe*. Sie können eine vorhandene Ressourcengruppe verwenden oder einen neuen Namen einer zu erstellenden Ressourcengruppe eingeben.
    * Für die Instanz: einen *Namen* für Ihre Azure Digital Twins-Instanz. Der Name der neuen Instanz muss innerhalb der Region für Ihr Abonnement eindeutig sein (d. h., wenn Ihr Abonnement eine weitere Azure Digital Twins-Instanz in der Region aufweist, die bereits den von Ihnen ausgewählten Namen verwendet, werden Sie aufgefordert, einen anderen Namen auszuwählen).

Im Folgenden finden Sie einen Auszug aus dem Ausgabeprotokoll des Skripts:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell-Fenster mit Eingabe- und Ausgabeprotokoll beim Ausführen des Bereitstellungsskripts" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Wird das Skript erfolgreich abgeschlossen, zeigt die Ausgabe am Ende `Deployment completed successfully` an. Andernfalls beheben Sie das in der Fehlermeldung erwähnte Problem, und führen Sie das Skript erneut aus. Es überspringt die bereits abgeschlossenen Schritte und führt das Anfordern von Eingaben ab dem Punkt fort, an dem die Ausführung zuvor unterbrochen wurde.

> [!NOTE]
> Das Skript weist die erforderliche Verwaltungsrolle innerhalb von Azure Digital Twins ( *Azure Digital Twins-Datenbesitzer* ) momentan demselben Benutzer zu, der das Skript in Cloud Shell ausführt. Falls erforderlich, können Sie diese Rolle nun über das Azure-Portal ([Anweisungen](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) oder die CLI ([Anweisungen](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) einem anderen Benutzer zuweisen, der für die Verwaltung der Instanz zuständig ist.

>[!NOTE]
>Zurzeit gibt es ein **bekanntes Problem** beim skriptgesteuerten Setup, bei dem einige Benutzer (insbesondere Benutzer mit persönlichen [Microsoft-Konten (MSAs)](https://account.microsoft.com/account)) möglicherweise feststellen, dass die **Rollenzuweisung für _Azure Digital Twins Data Owner_ (Azure Digital Twins-Datenbesitzer) nicht erstellt wurde**.
>
>Sie können die Rollenzuweisung über den Abschnitt [*Überprüfen der Benutzerrollenzuweisung*](#verify-user-role-assignment) weiter unten in diesem Artikel überprüfen und bei Bedarf die Rollenzuweisung manuell über das [Azure-Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) oder mithilfe der [Befehlszeilenschnittstelle](how-to-set-up-instance-cli.md#set-up-user-access-permissions) manuell einrichten.
>
>Weitere Informationen zu diesem Problem finden Sie unter [*Problembehandlung: Bekannte Probleme in Azure Digital Twins*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## <a name="verify-success-and-collect-important-values"></a>Überprüfen des Erfolgs und Erfassen wichtiger Werte

Die vom Skript erstellten Ressourcen und eingerichteten Berechtigungen können Sie über das [Azure-Portal](https://portal.azure.com) anhand der unten beschriebenen Anleitungen überprüfen. Wenn Sie den Erfolg eines beliebigen Schritts nicht überprüfen können, wiederholen Sie den Schritt. Sie können die Schritte einzeln mit dem [Azure-Portal](how-to-set-up-instance-portal.md) oder mit [CLI](how-to-set-up-instance-cli.md)-Anweisungen ausführen.

In diesem Abschnitt wird auch gezeigt, wie wichtige Werte der vom Skript eingerichteten Ressourcen, die für Ihre weitere Arbeit mit der Azure Digital Twins-Instanz erforderlich sein könnten, ermittelt werden. Sie sollten diese Werte an einem sicheren Ort speichern oder zu diesem Abschnitt zurückkehren, sobald Sie diese später benötigen. Wenn andere Benutzer in der Instanz programmieren, sollten Sie auch ihnen diese Werte mitteilen.

### <a name="verify-instance"></a>Überprüfen der Instanz

Über die [Azure Digital Twins-Seite](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) im Azure-Portal können Sie überprüfen, ob Ihre Instanz erstellt wurde. Sie können selbst auf diese Seite gelangen, indem Sie über die Portalsuchleiste nach *Azure Digital Twins* suchen.

Auf dieser Seite sind alle Ihre Azure Digital Twins-Instanzen aufgelistet. Suchen Sie in der Liste nach dem Namen Ihrer neu erstellten Instanz.

Wenn die Überprüfung nicht erfolgreich war, können Sie erneut versuchen, eine Instanz zu erstellen, indem Sie das [Portal](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) oder die [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance) verwenden.

### <a name="collect-instance-values"></a>Erfassen von Instanzwerten

Wählen Sie den Namen Ihrer Instanz auf der Seite [Azure Digital Twins](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) aus, um die Seite *Übersicht* der Instanz zu öffnen. Notieren Sie sich ihren *Namen* , die *Ressourcengruppe* und den *Hostnamen*. Diese benötigen Sie möglicherweise später zum Identifizieren der Instanz und zum Herstellen einer Verbindung mit dieser.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Hervorheben der wichtigen Werte auf der Übersichtsseite der Instanz":::

### <a name="verify-user-role-assignment"></a>Überprüfen der Benutzerrollenzuweisung

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Beachten Sie, dass das Skript diese erforderliche Rolle derzeit demselben Benutzer zuweist, der auch das Skript in Cloud Shell ausführt. Falls erforderlich, können Sie diese Rolle nun über das Azure-Portal ([Anweisungen](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) oder die CLI ([Anweisungen](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) einem anderen Benutzer zuweisen, der für die Verwaltung der Instanz zuständig ist.

Wenn die Überprüfung nicht erfolgreich war, können Sie Ihre eigene Rollenzuweisung auch über das [Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) oder die [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) wiederholen.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie einzelne REST-API-Aufrufe für Ihre Instanz mithilfe der Befehle der Azure Digital Twins-CLI: 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Gewusst wie: Verwenden der Azure Digital Twins-Befehlszeilenschnittstelle*](how-to-use-cli.md)

Sie können auch eine Verbindung zwischen Ihrer Clientanwendung und Ihrer Instanz herstellen, indem Sie Authentifizierungscode verwenden:
* [*Verwenden Schreiben von App-Authentifizierungscode*](how-to-authenticate-client.md)