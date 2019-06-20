---
title: Azure-SKUs für ein IoT Edge-Modul | Azure Marketplace
description: Erstellen von SKUs für ein IoT Edge-Modul.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: bf8957c315909785112dea947f2e2e0f7b26349f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942191"
---
# <a name="iot-edge-module-skus-tab"></a>Registerkarte „SKUs“ für ein IoT Edge-Modul

Auf der Registerkarte **SKUs** auf der Seite **Neues Angebot** können Sie eine oder mehrere SKUs erstellen und Ihrem neuen Angebot zuordnen.  Mit unterschiedlichen SKUs können Sie eine Lösung nach Funktionsgruppen, Abrechnungsmodellen oder einem anderen Merkmal unterscheiden.


## <a name="sku-settings"></a>SKU-Einstellungen

Wenn Sie ein neues Angebot erstellen, sind dem Angebot keine SKUs zugeordnet. Führen Sie die folgenden Schritte aus, um eine neue SKU zu erstellen:

- Wählen Sie auf der Seite **IoT Edge-Module > Neues Angebot** die Registerkarte **SKUs** aus.
- Wählen Sie unter „SKUs“ die Option **+ Neue SKU** aus, um das entsprechende Dialogfeld zu öffnen.

  ![Schaltfläche „Neue SKU“ auf der Registerkarte „Neues Angebot“ für IoT Edge-Module](./media/iot-edge-module-skus-tab-new-sku.png)

- Geben Sie im Dialogfeld **Neue SKU** einen Bezeichner für die SKU ein, und wählen Sie dann **OK** aus.
(In der nachfolgenden Tabelle ist die Namenskonvention für Bezeichner angegeben.)

Die Registerkarte **SKUs** wird aktualisiert. Auf der Registerkarte werden nun die Felder angezeigt, die Sie zum Konfigurieren der SKU bearbeiten. Mit einem Sternchen (*) gekennzeichnete Felder sind Pflichtfelder.

|  **Feld**       |     **Beschreibung**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU-ID\***       | Der Bezeichner für diese SKU. Dieser Name darf maximal 50 Zeichen lang sein und aus alphanumerischen Kleinbuchstaben oder Bindestrichen (-) bestehen. Er darf allerdings nicht mit einem Bindestrich enden. **Hinweis:** Sie können diesen Namen nicht mehr ändern, nachdem das Angebot veröffentlicht wurde. Der Name ist in Produkt-URLs öffentlich sichtbar. |
|  |  |


## <a name="sku-details"></a>SKU-Details

Konfigurieren Sie die **SKU-Details**, um zu definieren, wie die SKU auf den Websites des Azure Marketplace und Azure-Portals angezeigt wird.

![SKU-Metadaten für IoT Edge-Module](media/iot-edge-module-skus-tab-metadata.png)

In der folgenden Tabelle werden der Zweck, der Inhalt und die Formatierung der Felder unter **SKU-Details** beschrieben. Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

|  **Feld**       |     **Beschreibung**                                                          |
|  ---------       |     ---------------                                                          |
| **Titel\***        | Der Titel der SKU. Die maximale Länge beträgt 50 Zeichen. <br/> Er wird im Azure-Portal angezeigt und als Standardname für das Modul verwendet (ohne Leerzeichen und Sonderzeichen), wenn es bereitgestellt wird. In den Abbildungen unten können Sie sehen, wo genau dieses Feld angezeigt wird.|
| **Zusammenfassung\***      | Kurze Zusammenfassung zur SKU. Die maximale Länge beträgt 100 Zeichen. Geben Sie **KEINE** Zusammenfassung für das Angebot, sondern nur für die SKU ein.  Diese Zusammenfassung wird im Azure Marketplace angezeigt. In den Abbildungen unten können Sie sehen, wo genau dieses Feld angezeigt wird.|
| **Beschreibung\***  | Kurze Beschreibung der SKU. Die maximale Länge beträgt 3.000 Zeichen. Geben Sie KEINE Beschreibung des Angebots, sondern nur eine der SKU ein. Die Beschreibung wird im Azure Marketplace und im Azure-Portal angezeigt. Im Azure-Portal wird sie der Marketplace-Beschreibung hinzugefügt, mit der das auf der Registerkarte „Marketplace“ definierte Angebot beschrieben wird.  Die Beschreibung kann identisch sein mit der Zusammenfassung für die SKU. In den Abbildungen unten können Sie sehen, wo genau dieses Feld angezeigt wird.|
| **Diese SKU ausblenden\*** | Behalten Sie die Standardeinstellung **Nein** bei. |
|  |  |


### <a name="sku-example"></a>Beispiele für eine SKU

 In den folgenden Beispielen sehen Sie, wie die Felder **Titel**, **Zusammenfassung** und **Beschreibung** für eine SKU in verschiedenen Ansichten angezeigt werden.
 

#### <a name="on-the-azure-marketplace-website"></a>Auf der Azure Marketplace-Website:

- Beim Anzeigen von SKU-Details:

    ![Ansicht von SKUs auf der Azure Marketplace-Website](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Auf der Website des Azure-Portals:

- Beim Durchsuchen von SKUs:

    ![Ansicht des IoT Edge-Moduls beim Durchsuchen des Azure-Portals (1)](media/iot-edge-module-portal-browse.png)

    ![Ansicht des IoT Edge-Moduls beim Durchsuchen des Azure-Portals (2)](media/iot-edge-module-portal-product-picker.png)

- Beim Suchen nach SKUs:

    ![Ansicht des IoT Edge-Moduls beim Durchsuchen des Azure-Portals](media/iot-edge-module-portal-search.png)

- Beim Anzeigen von SKU-Details:

    ![Ansicht des IoT Edge-Moduls beim Anzeigen der Produktdetails im Portal](./media/iot-edge-module-portal-pdp.png)

- Beim Bereitstellen des Moduls:
    
    ![Ansicht des IoT Edge-Moduls beim Bereitstellen](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>SKU-Inhalt

Geben Sie unter **Edge-Modulimages** die Informationen an, die wir benötigen, um Ihr IoT Edge-Modul hochzuladen.

Geben Sie uns Zugriff auf Ihre [Azure Container Registry-Instanz](https://azure.microsoft.com/services/container-registry/) (ACR), die das IoT Edge-Modulimage enthält, damit wir es hochladen und zertifizieren können. Nach der Veröffentlichung wird Ihr IoT Edge-Modul kopiert und über eine öffentliche Containerregistrierung, die vom Azure Marketplace gehostet wird, verteilt.

Über Tags können Sie mehrere Plattformen festlegen und verschiedene Versionen angeben. Weitere Informationen zu Tags und zur Versionsverwaltung finden Sie unter [Vorbereiten der technischen Ressourcen für das IoT Edge-Modul](./cpp-create-technical-assets.md).

![IoT Edge-Modulimages](./media/iot-edge-module-skus-tab-acr.png)

In der folgenden Tabelle werden der Zweck, der Inhalt und die Formatierung der Felder in folgenden Abschnitten beschrieben: **Details des Imagerepositorys** und **Imageversion**.  Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.


|  **Feld**       |     **Beschreibung**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Image Repository Details*** (Details des Imagerepositorys)    |
| **Abonnement-ID\***        | Die Azure-Abonnement-ID Ihrer ACR-Instanz.|
| **Ressourcengruppenname\***      | Der Ressourcengruppenname Ihrer ACR-Instanz.|
| **Registrierungsname\***  | Der Registrierungsname Ihrer ACR-Instanz. Kopieren Sie nur den Registrierungsnamen, NICHT den Namen des Anmeldeservers (z.B. ohne `azurecr.io`). |
| **Repositoryname\***  | Der Repositoryname Ihrer ACR-Instanz, die Ihr IoT Edge-Modul enthält. **Hinweis:** Nachdem der Name festgelegt wurde, kann er später nicht mehr geändert werden. Verwenden Sie einen eindeutigen Namen, sodass in Ihrem Konto kein anderes Angebot mit dem gleichen Namen vorhanden ist. |
| **Benutzername\*** | Der Benutzername, der Ihrer ACR-Instanz zugeordnet ist (Administratorbenutzername). |
| **Kennwort\*** | Das Kennwort, das Ihrer ACR-Instanz zugeordnet ist. |
|    |  ***Imageversion***   |
| **Image Tag or Digest\*** (Imagetag oder -hashwert) | Die Version muss mindestens ein `latest`-Tag und ein Versionstag enthalten (z.B. beginnend mit `xx.xx.xx-`, wobei xx Zahlen sind). Es sollte sich um [Manifesttags](https://github.com/estesp/manifest-tool) handeln, die für mehrere Zielplattformen verwendet werden können. Zudem müssen alle Tags, auf die von einem Manifesttag verwiesen wird, hinzugefügt werden, damit wir sie hochladen können. Mithilfe von Tags können Sie mehrere Versionen eines IoT Edge-Moduls hinzufügen. Alle Manifesttags (mit Ausnahme von `latest`) müssen entweder mit `X.Y-` oder `X.Y.Z-` beginnen, wobei X, Y und Z ganze Zahlen sind. Weitere Informationen zu Tags und zur Versionsverwaltung finden Sie unter [Vorbereiten der technischen Ressourcen für das IoT Edge-Modul](./cpp-create-technical-assets.md). <br/> Wenn ein `latest`-Tag beispielsweise auf ein weiteres Tag und dieses auf `1.0.1-linux-x64`, `1.0.1-linux-arm32`, `1.0.1-windows-arm32` und ein viertes Tag verweist, müssen diese 6 Tags hier hinzugefügt werden. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Unterstützung Ihrer Kunden beim Starten durch Verwenden von Standardeinstellungen

Definieren Sie die am häufigsten verwendeten Einstellungen zum Bereitstellen des IoT Edge-Moduls. Sie können die Bereitstellungen von Kunden optimieren, indem diese Ihr IoT Edge-Modul mit diesen vorkonfigurierten Standardeinstellungen starten können.

![Standardeinstellungen für ein IoT Edge-Modul bei der Bereitstellung](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

In der folgenden Tabelle werden der Zweck, der Inhalt und die Formatierung der Felder für **Default Routes** (Standardrouten), **Default twin desired properties** (Gewünschte Standardeigenschaften von Gerätezwillingen), **Default environment variables** (Standardumgebungsvariablen) und **Default CreateOptions** (Standarderstellungsoptionen) beschrieben.

|  **Feld**       |     **Beschreibung**                                                          |
|  ---------       |     ---------------                                                          |
| **Default Routes** (Standardrouten)        | Der Name und der Wert für eine Standardroute dürfen maximal 512 Zeichen lang sein. Sie können bis zu 5 Standardrouten definieren. Achten Sie darauf, dass Sie die korrekte [Syntax](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) im Wert der Route verwenden. Verwenden Sie zum Verweisen auf Ihr Modul den entsprechenden Standardnamen. Dabei handelt es sich um den **SKU-Titel** ohne Leerzeichen und Sonderzeichen. Um auf andere noch nicht bekannte Module zu verweisen, verwenden Sie die `<FROM_MODULE_NAME>`-Konvention. So wissen Ihre Kunden, dass sie diese Informationen aktualisieren müssen. Weitere Informationen zu IoT Edge-Routen finden Sie [hier](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Wenn das Modul `ContosoModule` beispielsweise auf Eingaben für `ContosoInput` und Ausgabedaten für `ContosoOutput` lauscht, empfiehlt es sich, die folgenden 2 Standardrouten zu definieren:<br/>- Name 1: `ToContosoModule`<br/>- Wert 1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Name 2: `FromContosoModuleToCloud`<br/>- Wert 2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Default twin desired properties** (Gewünschte Standardeigenschaften von Gerätezwillingen)      | Der Name und der Wert für gewünschte Eigenschaften von Gerätezwillingen können maximal 512 Zeichen lang sein. Sie können bis zu 5 Namen und Werte für gewünschte Eigenschaften von Gerätezwillingen definieren. Die Werte für gewünschte Eigenschaften von Gerätezwillingen müssen gültige JSON-Werte ohne Escapezeichen und ohne Arrays sein. Sie können eine geschachtelte Hierarchie mit maximal 4 Ebenen aufweisen. Weitere Informationen zu gewünschten Eigenschaften von Gerätezwillingen finden Sie [hier](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Wenn ein Modul z.B. eine dynamisch konfigurierbare Aktualisierungsrate über gewünschte Eigenschaften von Gerätezwillingen unterstützt, empfiehlt es sich, die folgende gewünschte Standardeigenschaft für Gerätezwillinge zu definieren:<br/> - Name 1: `RefreshRate`<br/>- Wert 1: `60`|
| **Default environment variables** (Standardumgebungsvariablen)  | Der Name und der Wert für Standardumgebungsvariablen dürfen maximal 512 Zeichen lang sein. Sie können bis zu 5 Namen und Werte für Umgebungsvariablen definieren. <br/>Wenn vor dem Start eines Moduls beispielsweise die Zustimmung zu den Nutzungsbedingungen erforderlich ist, können Sie die folgende Standardumgebungsvariable definieren:<br/> - Name 1: `ACCEPT_EULA`<br/>- Wert 1: `Y`|
| **Default CreateOptions** (Standarderstellungsoptionen)  | Der createOptions-Wert darf maximal 512 Zeichen lang sein. Es muss ein gültiger JSON-Wert ohne Escapezeichen sein. Weitere Informationen zu createOptions finden Sie [hier](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Wenn es für ein Modul z.B. erforderlich ist, dass ein Port gebunden wird, können Sie die folgenden createOptions definieren:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Wählen Sie **Speichern** aus, um die SKU-Einstellungen zu speichern. 


## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie auf der Registerkarte [Marketplace](./cpp-marketplace-tab.md) eine Marketplace-Beschreibung für Ihr Angebot.
