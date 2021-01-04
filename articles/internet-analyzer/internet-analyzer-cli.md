---
title: Erstellen eines Internet Analyzer-Tests über die Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI Ihren ersten Internet Analyzer-Test erstellen.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 23a76e4007965c263671554bbcd937dbf3b4b0a6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676218"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Erstellen eines Internet Analyzer-Tests mithilfe der Befehlszeilenschnittstelle (Vorschau)

Es gibt zwei Möglichkeiten zum Erstellen einer Internet Analyzer-Ressource: über das Azure-Portal oder mithilfe der [CLI](internet-analyzer-create-test-portal.md). In diesem Abschnitt erfahren Sie, wie Sie eine neue Azure Internet Analyzer-Ressource mit der Befehlszeilenschnittstelle erstellen. 


> [!IMPORTANT]
> Diese öffentliche Vorschauversion wird ohne Servicelevelvereinbarung bereitgestellt und sollte nicht für Produktionsworkloads verwendet werden. Unter Umständen werden bestimmte Features nicht unterstützt, verfügen über eingeschränkte Funktionen und sind nicht an allen Azure-Standorten verfügbar. Weitere Informationen finden Sie unter [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voraussetzungen

Die öffentliche Vorschau ist für die globale Verwendung verfügbar. Allerdings ist der Datenspeicher in der Vorschauphase auf *USA, Westen 2* beschränkt.

## <a name="object-model"></a>Objektmodell
Die Internet Analyzer-Befehlszeilenschnittstelle macht die folgenden Ressourcentypen verfügbar:
* **Tests:** Ein Test vergleicht die Endbenutzerleistung von zwei Internetendpunkten (A und B) im Zeitverlauf.
* **Profile:** Tests werden unter einem Internet Analyzer-Profil erstellt. Profile ermöglichen das Gruppieren verwandter Tests. Ein einzelnes Profil kann einen Test oder mehrere Tests enthalten.
* **Vorkonfigurierte Endpunkte:** Wir haben Endpunkte mit verschiedenen Konfigurationen (Regionen, Beschleunigungstechnologien usw.) eingerichtet. Sie können in Ihren Tests einen dieser vorkonfigurierten Endpunkte verwenden.
* **Scorecards:**  Eine Scorecard bietet kurze und aussagekräftige Zusammenfassungen der Messergebnisse. Informationen finden Sie unter [Interpretieren Ihrer Scorecard](internet-analyzer-scorecard.md).
* **Zeitreihen:** Eine Zeitreihe zeigt, wie sich eine Metrik im Laufe der Zeit ändert.

## <a name="profile-and-test-creation"></a>Profil- und Testerstellung
1. Befolgen Sie die Anweisungen im Abschnitt **Wie nehme ich an der Vorschauversion teil?** der [häufig gestellten Fragen zu Azure Internet Analyzer](internet-analyzer-faq.md), um Zugriff auf die Vorschauversion von Internet Analyzer zu erhalten.
2. [Installieren Sie die Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Führen Sie den Befehl `login` aus, um eine CLI-Sitzung zu starten:
    ```azurecli-interactive
    az login
    ```

    Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.
    Öffnen Sie andernfalls die Browserseite https://aka.ms/devicelogin, und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein.

4. Melden Sie sich im Browser mit Ihren Anmeldeinformationen an.

5. Wählen Sie die Abonnement-ID aus, der Zugriff auf die öffentliche Vorschau von Internet Analyzer gewährt wurde.

    Nach der Anmeldung sehen Sie eine Liste der mit Ihrem Azure-Konto verknüpften Abonnements. Die Abonnementinformationen mit `isDefault: true` ist das momentan aktivierte Abonnement nach der Anmeldung. Zur Auswahl eines anderen Abonnements verwenden Sie den Befehl [az account set](/cli/azure/account#az-account-set) mit der ID des Abonnements, zu dem Sie wechseln möchten. Weitere Informationen zur Abonnementauswahl finden Sie unter [Verwenden mehrerer Azure-Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    Es gibt auch Wege, wie Sie sich nicht interaktiv anmelden können. Diese werden unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) ausführlich beschrieben.

6. **[Optional]** Erstellen Sie eine neue Azure-Ressourcengruppe:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Installieren Sie die Azure CLI-Erweiterung für Internet Analyzer:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Erstellen Sie ein neues Internet Analyzer-Profil:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Listen Sie alle vorkonfigurierten Endpunkte auf, die für das neu erstellte Profil verfügbar sind:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Erstellen Sie einen neuen Test unter dem neu erstellten Internet Analyzer-Profil:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Beim obigen Befehl wird angenommen, dass sowohl für `www.contoso.com` als auch für `www.microsoft.com` das 1 Pixel große Bild ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) unter benutzerdefinierten Pfaden gehostet wird. Wird ein Objektpfad nicht explizit angegeben, verwendet Internet Analyzer standardmäßig `/apc/trans.gif` als Objektpfad. Dort wird das 1 Pixel große Bild von den vorkonfigurierten Endpunkten gehostet. Beachten Sie auch, dass das Schema (HTTPS/HTTP) nicht angegeben werden muss. Internet Analyzer unterstützt nur HTTPS-Endpunkte, daher wird von HTTPS ausgegangen.

11. Der neue Test sollte im Internet Analyzer-Profil angezeigt werden:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Beispielausgabe:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Damit mit dem Generieren von Messungen begonnen werden kann, muss die JavaScript-Datei, auf die mit dem **scriptFileUri** -Element des Tests verwiesen wird, in Ihre Webanwendung eingebettet werden. Genaue Anweisungen finden Sie auf der Seite [Einbetten des Internet Analyzer-Clients](internet-analyzer-embed-client.md).

13. Der Fortschritt des Tests kann anhand seines Statuswerts nachverfolgt werden:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Sie können die gesammelten Ergebnisse des Tests überprüfen, indem Sie Zeitreihen oder Scorecards dafür erstellen:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Nächste Schritte

* Eine vollständige Liste der unterstützten Befehle sowie Anwendungsbeispiele finden Sie in der [Referenz zur Befehlszeilenschnittstelle für Internet Analyzer](/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest).
* Lesen Sie die [häufig gestellten Fragen zu Internet Analyzer](internet-analyzer-faq.md).
* Informieren Sie sich ausführlicher über das Einbetten des [Internet Analyzer-Clients](internet-analyzer-embed-client.md) und das Erstellen eines [benutzerdefinierten Endpunkts](internet-analyzer-custom-endpoint.md).