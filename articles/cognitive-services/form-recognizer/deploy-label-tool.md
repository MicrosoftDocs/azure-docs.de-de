---
title: Bereitstellen des Formularerkennungstools für die Bezeichnung von Beispielen
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die verschiedenen Möglichkeiten, wie Sie das Formularerkennungstool für die Bezeichnung von Beispielen für überwachte Lernen bereitstellen können.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 985f9e741a1491a812c1d2b20de96381f8af3fa4
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359845"
---
# <a name="deploy-the-sample-labeling-tool"></a>Bereitstellen des Samplebezeichnungstools

Das Formularerkennungstool für die Beschriftung von Beispielen ist eine Anwendung mit einer einfachen Benutzeroberfläche, mit der Sie Formulare (Dokumente) zum überwachten Lernen manuell beschriften können. In diesem Artikel finden Sie Links und Anweisungen zu folgenden Aufgaben:

* [Lokales Ausführen des Tools für die Beschriftung von Beispielen](#run-the-sample-labeling-tool-locally)
* [Bereitstellen des Tools für die Beschriftung von Beispielen in einer Azure-Containerinstanz](#deploy-with-azure-container-instances-aci)
* [Verwenden des Open-Source-OCR Form Labeling Tool und Mitwirken am Tool](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Lokales Ausführen des Tools für die Beschriftung von Beispielen

Die schnellste Möglichkeit, um mit dem Beschriften von Daten loszulegen, ist die lokale Ausführung des Tools für die Beschriftung von Beispielen. Im folgenden Schnellstart wird gezeigt, wie Sie die Formularerkennungs-REST-API und das Tool für die Beschriftung von Beispielen verwenden, um ein benutzerdefiniertes Modell mit manuell beschrifteten Daten zu trainieren. 

* [Schnellstart: Beschriften von Formularen, Trainieren eines Modells und Analysieren eines Formulars mithilfe des Tools für die Beschriftung von Beispielen](./quickstarts/label-tool.md)

## <a name="deploy-with-azure-container-instances-aci"></a>Bereitstellen mit Azure Container Instances (ACI)

Zum Bereitstellen des Tools für die Beschriftung von Beispielen in einer Azure-Containerinstanz gibt es zwei Möglichkeiten. Beide Optionen werden verwendet, um das Tool für die Beschriftung von Beispielen mit ACI auszuführen: 

* [Verwenden des Azure-Portals](#azure-portal)
* [Verwenden der Azure-Befehlszeilenschnittstelle](#azure-cli)

### <a name="azure-portal"></a>Azure-Portal

Führen Sie die folgenden Schritte aus, um eine neue Ressource im Azure-Portal zu erstellen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/signin/index/) an.
2. Wählen Sie **Ressource erstellen**. 
3. Wählen Sie anschließend **Web-App** aus. 

   > [!div class="mx-imgBorder"]
   > ![Auswählen von „Web-App“](./media/quickstarts/formre-create-web-app.png)
   
4. Vergewissern Sie sich zunächst, dass die Registerkarte **Grundlagen** ausgewählt ist. Jetzt müssen Sie einige Informationen angeben: 

   > [!div class="mx-imgBorder"]
   > ![Auswählen von „Grundlagen“](./media/quickstarts/formre-select-basics.png)
   * Abonnement: Wählen Sie ein vorhandenes Azure-Abonnement aus.
   * Ressourcengruppe: Sie können eine vorhandene Ressourcengruppe wiederverwenden oder eine neue Ressourcengruppe für das Projekt erstellen. Es wird empfohlen, eine neue Ressourcengruppe zu erstellen.
   * Name: Geben Sie einen Namen für Ihre Web-App ein. 
   * Veröffentlichen: Wählen Sie **Docker-Container** aus.
   * Betriebssystem: Wählen Sie **Linux** aus.
   * Region: Wählen Sie eine für Sie geeignete Region aus.
   * Linux-Plan: Wählen Sie einen Tarif/Plan für Ihre App Service-App aus. 

   > [!div class="mx-imgBorder"]
   > ![Konfigurieren der Web-App](./media/quickstarts/formre-select-docker-linux.png)

5. Wählen Sie als Nächstes die Registerkarte **Docker** aus. 

   > [!div class="mx-imgBorder"]
   > ![Auswählen von „Docker“](./media/quickstarts/formre-select-docker.png)

6. Konfigurieren Sie nun Ihren Docker-Container. Alle Felder sind erforderlich, sofern nicht anders angegeben:

    # <a name="v20"></a>[v2.0](#tab/v2-0)  
   * Optionen: Wählen Sie **Einzelner Container** aus.
   * Imagequelle: Wählen Sie **Private Registrierung** aus. 
   * Server-URL: Legen Sie die URL auf `https://mcr.microsoft.com` fest.
   * Benutzername (optional): Erstellen Sie einen Benutzernamen. 
   * Kennwort (optional): Erstellen Sie ein sicheres Kennwort, das Sie sich gut merken können.
   * Image und Tag: Legen Sie diese Einstellung auf `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest` fest.
   * Continuous Deployment: Legen Sie diesen Wert auf **On** fest, wenn Sie automatische Aktualisierungen erhalten möchten, sobald das Entwicklungsteam Änderungen am Beschriftungstool für Stichproben vornimmt.
   * Startbefehl: Legen Sie diese Einstellung auf `./run.sh eula=accept` fest.

    # <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1) 
   * Optionen: Wählen Sie **Einzelner Container** aus.
   * Imagequelle: Wählen Sie **Private Registrierung** aus. 
   * Server-URL: Legen Sie die URL auf `https://mcr.microsoft.com` fest.
   * Benutzername (optional): Erstellen Sie einen Benutzernamen. 
   * Kennwort (optional): Erstellen Sie ein sicheres Kennwort, das Sie sich gut merken können.
   * Image und Tag: Legen Sie diese Einstellung auf `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview` fest.
   * Continuous Deployment: Legen Sie diesen Wert auf **On** fest, wenn Sie automatische Aktualisierungen erhalten möchten, sobald das Entwicklungsteam Änderungen am Beschriftungstool für Stichproben vornimmt.
   * Startbefehl: Legen Sie diese Einstellung auf `./run.sh eula=accept` fest.
    
    ---

   > [!div class="mx-imgBorder"]
   > ![Konfigurieren von Docker](./media/quickstarts/formre-configure-docker.png)

7. Das ist alles. Wählen Sie als Nächstes **Überprüfen + Erstellen** und dann **Erstellen** aus, um die Web-App bereitzustellen. Nach der Bereitstellung können Sie unter der URL, die in der **Übersicht** für Ihre Ressource angegeben ist, auf die Web-App zugreifen.

> [!NOTE]
> Beim Erstellen Ihrer Web-App können Sie auch die Autorisierung/Authentifizierung konfigurieren. Für die ersten Schritte ist dies jedoch nicht erforderlich. 

> [!IMPORTANT]
> Möglicherweise müssen Sie TLS für Ihre Web-App aktivieren, um sie unter ihrer `https`-Adresse anzuzeigen. Befolgen Sie die Anweisungen in [Aktivieren eines TLS-Endpunkts](https://docs.microsoft.com/azure/container-instances/container-instances-container-group-ssl), um einen Sidecar-Container einzurichten, und aktivieren Sie dann TLS/SSL für Ihre Web-App.

### <a name="azure-cli"></a>Azure CLI

Als Alternative zum Azure-Portal können Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) zum Erstellen einer Ressource verwenden. Bevor Sie fortfahren, müssen Sie die [Azure CLI](/cli/azure/install-azure-cli) installieren. Sie können diesen Schritt überspringen, falls Sie sie bereits verwenden. 

Es gibt einige Dinge, die Sie über diesen Befehl wissen sollten:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` generiert einen zufälligen DNS-Namen. 
* Dieses Beispiel setzt voraus, dass Sie über eine Ressourcengruppe verfügen, die Sie zum Erstellen einer Ressource verwenden können. Ersetzen Sie `<resource_group_name>` durch eine gültige Ressourcengruppe, die Ihrem Abonnement zugeordnet ist. 
* Sie müssen angeben, wo die Ressource erstellt werden soll. Ersetzen Sie `<region name>` durch die gewünschte Region für die Web-App. 
* Dieser Befehl akzeptiert automatisch die Lizenzbedingungen.

Führen Sie in der Azure CLI den folgenden Befehl aus, um eine Web-App-Ressource für das Tool für die Beschriftung von Beispielen zu erstellen: 


# <a name="v20"></a>[v2.0](#tab/v2-0)   
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 
# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

---

### <a name="connect-to-azure-ad-for-authorization"></a>Herstellen einer Verbindung mit Azure AD zur Autorisierung

Wir empfehlen, Ihre Web-App mit Azure Active Directory zu verbinden. Dadurch wird sichergestellt, dass nur Benutzer mit gültigen Anmeldeinformationen sich anmelden und Ihre Web-App verwenden können. Folgen Sie den Anweisungen unter [Konfigurieren Ihrer App Service-App ](../../app-service/configure-authentication-provider-aad.md), um eine Verbindung mit Azure Active Directory herzustellen.

## <a name="open-source-on-github"></a>Open-Source auf GitHub

Das OCR Form Labeling Tool steht auch als Open-Source-Projekt auf GitHub zur Verfügung. Bei dem Tool handelt es sich um eine mit React + Redux erstellte und in TypeScript geschriebene Webanwendung. Besuchen Sie [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md), wenn Sie weitere Informationen benötigen oder sich beteiligen möchten.

## <a name="next-steps"></a>Nächste Schritte

Im Schnellstart zum [Trainieren mit Beschriftungen](./quickstarts/label-tool.md) erfahren Sie, wie Sie mit dem Tool Trainingsdaten manuell beschriften und überwachtes Lernen ausführen.
