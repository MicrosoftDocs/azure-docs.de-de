---
title: Verwenden privater Endpunkte mit Speech-Diensten
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Speech-Dienste mit privaten Endpunkten verwenden, die durch Azure Private Link bereitgestellt werden
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: c9af0cda14261e8eab7f1ecc05c50a289d7ddfdb
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559657"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Verwenden von Speech-Diensten über einen privaten Endpunkt

Mithilfe von [Azure Private Link](../../private-link/private-link-overview.md) können Sie Verbindungen mit Diensten in Azure über einen [privaten Endpunkt](../../private-link/private-endpoint-overview.md) herstellen. Ein privater Endpunkt ist eine private IP-Adresse, die nur in einem bestimmten [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) und Subnetz zugänglich ist.

In diesem Artikel wird erläutert, wie Sie Private Link und private Endpunkte mit Speech-Diensten in Azure Cognitive Services einrichten und verwenden.

> [!NOTE]
> Bevor Sie fortfahren, informieren Sie sich über das [Verwenden von virtuellen Netzwerken mit Cognitive Services](../cognitive-services-virtual-networks.md).

In diesem Artikel wird außerdem erläutert, wie Sie [private Endpunkte später entfernen, aber die Speech-Ressource weiterverwenden](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Erstellen eines benutzerdefinierten Domänennamens

Für private Endpunkte ist ein [benutzerdefinierter Unterdomänenname für Cognitive Services](../cognitive-services-custom-subdomains.md) erforderlich. Mithilfe der folgenden Anweisungen können Sie einen solchen für Ihre Speech-Ressource erstellen.

> [!WARNING]
> Eine Speech-Ressource mit aktiviertem benutzerdefinierten Domänennamen interagiert auf andere Weise mit Speech-Diensten. Sie müssen Ihren Anwendungscode für Szenarios [mit aktivierten privaten Endpunkten](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) und [*ohne* aktivierte private Endpunkte](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints) anpassen.
>
> Wenn Sie einen benutzerdefinierten Domänennamen aktivieren, wird der Vorgang [unumkehrbar](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). Sie können den [regionalen Namen](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) nur wiederherstellen, indem Sie eine neue Speech-Ressource erstellen.
>
> Wenn Ihrer Speech-Ressource viele benutzerdefinierte Modelle und Projekte zugeordnet sind, die über [Speech Studio](https://speech.microsoft.com/) erstellt wurden, wird dringend empfohlen, die Konfiguration mit einer Testressource auszuprobieren und Produktionsressourcen erst im Anschluss zu bearbeiten.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Befolgen Sie diese Schritte, um einen benutzerdefinierten Domänennamen im Azure-Portal zu erstellen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich bei Ihrem Azure-Konto an.
1. Wählen Sie die erforderliche Speech-Ressource aus.
1. Wählen Sie in der Gruppe **Ressourcenverwaltung** im linken Bereich die Option **Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Firewalls und virtuelle Netzwerke** die Option **Benutzerdefinierten Domänennamen generieren** aus. Dadurch wird rechts ein Panel mit Anweisungen zum Erstellen einer eindeutigen benutzerdefinierten Unterdomäne für Ihre Ressource geöffnet.
1. Geben Sie im Bereich **Benutzerdefinierten Domänennamen generieren** einen benutzerdefinierten Domänennamen ein. Ihre vollständige benutzerdefinierte Domäne sieht wie `https://{your custom name}.cognitiveservices.azure.com` aus. 
    
    Hinweis: Nachdem Sie einen benutzerdefinierten Domänennamen erstellt haben, kann dieser _nicht mehr geändert_ werden.
    
    Nachdem Sie Ihren benutzerdefinierten Domänennamen eingegeben haben, wählen Sie **Speichern** aus.
1. Nachdem der Vorgang abgeschlossen ist, wählen Sie in der Gruppe **Ressourcenverwaltung** die Option **Schlüssel und Endpunkt** aus. Vergewissern Sie sich, dass der neue Endpunktname Ihrer Ressource mit `https://{your custom name}.cognitiveservices.azure.com` beginnt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Vergewissern Sie sich, dass auf Ihrem Computer PowerShell 7.x oder höher mit dem Azure PowerShell-Modul 5.1.0 oder höher installiert ist. Andernfalls können Sie keinen benutzerdefinierten Domänennamen mit PowerShell erstellen. Befolgen Sie diese Schritte, um die Versionen dieser Tools anzuzeigen:

1. Geben Sie in einem PowerShell-Fenster Folgendes ein:

    `$PSVersionTable`

    Überprüfen Sie, ob der Wert von `PSVersion` 7.x oder höher ist. Befolgen Sie die Anweisungen unter [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell), um ein Upgrade für PowerShell durchzuführen.

1. Geben Sie in einem PowerShell-Fenster Folgendes ein:

    `Get-Module -ListAvailable Az`

    Wenn nichts angezeigt wird oder die Version des Azure PowerShell-Moduls niedriger als 5.1.0 ist, befolgen Sie die Anweisungen unter [installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps), um das Upgrade durchzuführen.

Bevor Sie fortfahren, führen Sie `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Vergewissern Sie sich, dass ein benutzerdefinierter Domänenname verfügbar ist

Überprüfen Sie die Verfügbarkeit der benutzerdefinierten Domäne, die Sie verwenden möchten. Der folgende Code überprüft mit der Methode [checkDomainAvailability](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) der Cognitive Services-REST-API, ob die Domäne verfügbar ist.

> [!TIP]
> Dieser Code funktioniert *nicht* in Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Wenn der gewünschte Name verfügbar ist, wird eine Antwort wie diese angezeigt:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Wenn der Name bereits verwendet wird, wird die folgende Antwort angezeigt:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Erstellen Ihres benutzerdefinierten Domänennamens

Verwenden Sie das Cmdlet [Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount), um einen benutzerdefinierten Domänennamen für die ausgewählte Speech-Ressource zu aktivieren.

> [!WARNING]
> Nach erfolgreicher Ausführung des folgenden Codes können Sie einen benutzerdefinierten Domänennamen für Ihre Speech-Ressource erstellen. Dieser Name kann jedoch *nicht* geändert werden.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Für diesen Abschnitt ist die aktuelle Version der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Überprüfen der Verfügbarkeit des benutzerdefinierten Domänennamens

Überprüfen Sie die Verfügbarkeit der benutzerdefinierten Domäne, die Sie verwenden möchten. Verwenden Sie hierfür die [checkDomainAvailability](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)-Methode der Cognitive Services-REST-API.

Kopieren Sie den folgenden Codeblock, fügen Sie Ihren bevorzugten benutzerdefinierten Domänennamen ein, und speichern Sie ihn in der Datei `subdomain.json`.

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Kopieren Sie die Datei in den aktuellen Ordner, oder laden Sie sie in Azure Cloud Shell hoch, und führen Sie den folgenden Befehl aus. Ersetzen Sie `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` durch Ihre Azure-Abonnement-ID.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Wenn der gewünschte Name verfügbar ist, wird eine Antwort wie diese angezeigt:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Wenn der Name bereits verwendet wird, wird die folgende Antwort angezeigt:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>Aktivieren eines benutzerdefinierten Domänennamens

Verwenden Sie den Befehl [az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update), um einen benutzerdefinierten Domänennamen für die ausgewählte Speech-Ressource zu aktivieren.

Wählen Sie das Azure-Abonnement aus, das die Speech-Ressource enthält. Wenn nur ein aktives Abonnement in Ihrem Azure-Konto vorhanden ist, können Sie diesen Schritt überspringen. Ersetzen Sie `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` durch Ihre Azure-Abonnement-ID.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Legen Sie den benutzerdefinierten Domänennamen auf die ausgewählte Ressource fest. Ersetzen Sie die Beispielparameterwerte durch die tatsächlichen Parameterwerte, und führen Sie den folgenden Befehl aus.

> [!WARNING]
> Nach erfolgreicher Ausführung des folgenden Befehls können Sie einen benutzerdefinierten Domänennamen für Ihre Speech-Ressource erstellen. Dieser Name kann jedoch *nicht* geändert werden.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>Aktivieren privater Endpunkte

Es wird empfohlen, die an das virtuelle Netzwerk mit den erforderlichen Updates für die privaten Endpunkte angefügte [private DNS-Zone](../../dns/private-dns-overview.md) zu verwenden. Eine private DNS-Zone wird beim Bereitstellungsprozess standardmäßig erstellt. Wenn Sie einen eigenen DNS-Server verwenden, müssen Sie möglicherweise auch die DNS-Konfiguration ändern. 

Sie sollten sich für eine DNS-Strategie entscheiden, *bevor* Sie private Endpunkte für eine Speech-Produktionsressource bereitstellen. Testen Sie Ihre DNS-Änderungen insbesondere dann, wenn Sie einen eigenen DNS-Server verwenden.

Erstellen Sie mithilfe eines der folgenden Artikel private Endpunkte. In diesen Artikeln wird eine Web-App als Beispielressource verwendet, die mit privaten Endpunkten aktiviert werden soll.

- [Erstellen eines privaten Endpunkts über das Azure-Portal](../../private-link/create-private-endpoint-portal.md)
- [Erstellen eines privaten Endpunkts mit Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Erstellen eines privaten Endpunkts mit der Azure CLI](../../private-link/create-private-endpoint-cli.md)

Verwenden Sie diese Parameter anstelle der Parameter in dem Artikel, den Sie ausgewählt haben:

| Einstellung             | Wert                                    |
|---------------------|------------------------------------------|
| Ressourcentyp       | **Microsoft.CognitiveServices/accounts** |
| Resource            | **\<your-speech-resource-name>**         |
| Zielunterressource | **Konto**                              |

**DNS für private Endpunkte:** Sehen Sie sich die Grundprinzipien für [DNS für private Endpunkte in Cognitive Services-Ressourcen](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints) an. Überprüfen Sie dann wie in den folgenden Abschnitten beschrieben, ob Ihre DNS-Konfiguration ordnungsgemäß funktioniert.

### <a name="resolve-dns-from-the-virtual-network"></a>DNS-Auflösung über das virtuelle Netzwerk

Diese Überprüfung ist *erforderlich*.

Befolgen Sie diese Schritte, um den benutzerdefinierten DNS-Eintrag über Ihr virtuelles Netzwerk zu testen:

1. Melden Sie sich bei einem virtuellen Computer an, der sich in dem virtuellen Netzwerk befindet, an das Sie Ihren privaten Endpunkt angefügt haben. 
1. Öffnen Sie die Windows-Eingabeaufforderung oder die Bash-Shell, führen Sie `nslookup` aus, und vergewissern Sie sich, dass der benutzerdefinierte Domänenname Ihrer Ressource erfolgreich aufgelöst wird.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Überprüfen Sie, ob die IP-Adresse mit der IP-Adresse Ihres privaten Endpunkts übereinstimmt.

### <a name="resolve-dns-from-other-networks"></a>DNS-Auflösung über andere Netzwerke

Nehmen Sie diese Überprüfung nur vor, wenn Sie die Option **Alle Netzwerke** oder die Zugriffsoption **Ausgewählte Netzwerke und private Endpunkte** im Abschnitt **Netzwerk** Ihrer Ressource aktiviert haben. 

Wenn Sie nur über private Endpunkte auf die Ressource zugreifen, können Sie diesen Abschnitt überspringen.

1. Melden Sie sich bei einem Computer an, der an ein Netzwerk angefügt ist, das auf die Ressource zugreifen kann.
2. Öffnen Sie die Windows-Eingabeaufforderung oder die Bash-Shell, führen Sie `nslookup` aus, und vergewissern Sie sich, dass der benutzerdefinierte Domänenname Ihrer Ressource erfolgreich aufgelöst wird.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> Die aufgelöste IP-Adresse verweist auf einen Proxyendpunkt eines virtuellen Netzwerks, der den Netzwerkdatenverkehr an den privaten Endpunkt für die Cognitive Services-Ressource sendet. Das Verhalten unterscheidet sich bei einer Ressource mit einem benutzerdefinierter Domänennamen aber *ohne* private Endpunkte. Details dazu finden Sie in [diesem Abschnitt](#dns-configuration).

## <a name="adjust-existing-applications-and-solutions"></a>Anpassen vorhandener Anwendungen und Lösungen

Eine Speech-Ressource mit aktivierter benutzerdefinierten Domäne interagiert auf andere Weise mit Speech Services. Das gilt für Speech-Ressourcen mit aktivierter benutzerdefinierter Domäne mit und ohne private Endpunkte. Die Informationen in diesem Abschnitt beziehen sich auf beide Szenarios.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und aktiviertem privaten Endpunkt

Eine Speech-Ressource, für ein benutzerdefinierter Domänennamen und ein privater Endpunkt aktiviert sind, interagiert auf andere Weise mit Speech-Diensten. In diesem Abschnitt wird erläutert, wie eine solche Ressource mit der Speech-REST-API und dem [Speech-SDK](speech-sdk.md) verwendet wird.

> [!NOTE]
> Eine Speech-Ressource ohne private Endpunkte, aber mit aktiviertem benutzerdefinierten Domänennamen, interagiert auf besondere Weise mit Speech-Diensten. Diese unterscheidet sich vom Szenario einer Speech-Ressource mit aktivierten privaten Endpunkten. Weitere Informationen zu diesen Ressourcen (falls Sie z. B. die privaten Endpunkte aus einer Ressource entfernt haben) finden Sie im Abschnitt [Verwenden einer Speech-Ressource mit benutzerdefiniertem Domänennamen und ohne private Endpunkte](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und privatem Endpunkt: Verwendung mit den REST-APIs

In diesem Abschnitt wird `my-private-link-speech.cognitiveservices.azure.com` als DNS-Beispielname (benutzerdefinierte Domäne) für die Speech-Ressource verwendet.

Die Speech-Dienste beinhalten REST-APIs für die [Spracherkennung](rest-speech-to-text.md) und die [Sprachsynthese](rest-text-to-speech.md). Bei Szenarios mit aktivierten privaten Endpunkten muss Folgendes berücksichtigt werden:

Für die Spracherkennung gibt es zwei REST-APIs. Jede API dient einem anderen Zweck, verwendet unterschiedliche Endpunkte und erfordert einen anderen Ansatz, wenn sie in einem Szenario mit aktivierten privaten Endpunkten verwendet wird.

Es gibt folgende REST-APIs für die Spracherkennung:
- Die [Spracherkennungs-REST-API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) wird für die [Batchtranskription](batch-transcription.md) und [Custom Speech eingesetzt](custom-speech-overview.md). Version 3.0 ist die [Nachfolgeversion von Version 2.0](./migrate-v2-to-v3.md).
- Die [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) wird für die Onlinetranskription eingesetzt. 

Die Verwendung der Spracherkennungs-REST-API für kurze Audiodaten und der Sprachsynthese-REST-API ist in Szenarios mit privaten Endpunkten identisch. Sie entspricht dem [Speech-SDK-Ansatz](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), der im Verlauf dieses Artikels erläutert wird. 

Die Spracherkennungs-REST-API 3.0 verwendet andere Endpunkte und erfordert daher einen anderen Ansatz für Szenarios mit aktivierten privaten Endpunkten.

In den nächsten Unterabschnitten werden beide Fälle erläutert.

##### <a name="speech-to-text-rest-api-v30"></a>Spracherkennungs-REST-API 3.0

Speech-Ressourcen verwenden üblicherweise [regionale Cognitive Services-Endpunkte](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) für die Kommunikation mit der [Spracherkennungs-REST-API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Für diese Ressourcen gilt das folgende Benennungsformat: <p/>`{region}.api.cognitive.microsoft.com`.

Hier sehen Sie ein Beispiel für eine Anforderungs-URL:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> In [diesem Artikel](sovereign-clouds.md) finden Sie Informationen zu Azure Government- und Azure China-Endpunkten.

Nachdem Sie benutzerdefinierte Domänen für eine Speech-Ressource aktiviert haben (für private Endpunkte erforderlich), verwendet diese das folgende DNS-Namensmuster für den grundlegenden REST-API-Endpunkt: <p/>`{your custom name}.cognitiveservices.azure.com`.

In diesem Beispiel lautet der Name des REST-API-Endpunkts dann: <p/>`my-private-link-speech.cognitiveservices.azure.com`.

Die Anforderungs-URL aus dem Beispiel muss in folgendes Format konvertiert werden:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Diese URL sollte (bei [korrekter DNS-Auflösung](#resolve-dns-from-the-virtual-network)) von dem virtuellen Netzwerk aus erreichbar sein, dem der private Endpunkt angefügt ist.

Nach dem Aktivieren des benutzerdefinierten Domänennamens für eine Speech-Ressource ersetzen Sie in der Regel den Hostnamen in allen Anforderungs-URLs durch den neuen Hostnamen der benutzerdefinierten Domäne. Die restlichen Bestandteile der Anforderung (wie der Pfad `/speechtotext/v3.0/transcriptions` im obigen Beispiel) bleiben unverändert.

> [!TIP]
> Einige Kunden entwickeln Anwendungen, die die Region des DNS-Namens des regionalen Endpunkts verwenden, um beispielsweise eine Anforderung an die Speech-Ressource zu senden, die in der jeweiligen Azure-Region bereitgestellt wurde.
>
> Der benutzerdefinierte Domänenname einer Speech-Ressource enthält *keine* Informationen zu der Region, in der die Ressource bereitgestellt wird. Die oben beschriebene Anwendungslogik funktioniert also *nicht* und muss abgeändert werden.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Spracherkennungs-REST-API für kurze Audiodaten und Sprachsynthese-REST-API

Die [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) und die [Sprachsynthese-REST-API](rest-text-to-speech.md) verwenden zwei Endpunktarten:
- [Regionale Cognitive Services-Endpunkte](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) für die Kommunikation mit der Cognitive Services-REST-API, um ein Autorisierungstoken abzurufen
- Spezielle Endpunkte für alle sonstigen Vorgänge

> [!NOTE]
> In [diesem Artikel](sovereign-clouds.md) finden Sie Informationen zu Azure Government- und Azure China-Endpunkten.

Eine ausführliche Beschreibung dieser speziellen Endpunkte und Informationen zur Transformation der URL für eine Speech-Ressource mit aktivierten privaten Endpunkten finden Sie in [diesem Unterabschnitt](#construct-endpoint-url) zur Verwendung mit dem Speech-SDK. Das Prinzip für das SDK gilt auch für die Spracherkennungs-REST-API für kurze Audiodaten und die Sprachsynthese-REST-API.

Lesen Sie das Material im entsprechenden Unterabschnitt im vorherigen Absatz, und sehen Sie sich dann das folgende Beispiel an. In diesem Beispiel wird die Sprachsynthese-REST-API beschrieben. Die Verwendung der Spracherkennungs-REST-API für kurze Audiodaten ist identisch.

> [!NOTE]
> Wenn Sie die Spracherkennungs-REST-API für kurze Audiodaten und die Sprachsynthese-REST-API in Szenarien mit privaten Endpunkten verwenden, nutzen Sie einen Abonnementschlüssel, der über den `Ocp-Apim-Subscription-Key`-Header übergeben wird. (Weitere Informationen finden Sie unter [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#request-headers) und [Sprachsynthese-REST-API](rest-text-to-speech.md#request-headers))
>
> Das Verwenden eines Autorisierungstokens und dessen Übergabe an den speziellen Endpunkt über den `Authorization`-Header funktioniert *nur*, wenn Sie die Zugriffsoption **Alle Netzwerke** im Abschnitt **Netzwerk** Ihrer Speech-Ressource aktiviert haben. In anderen Fällen erhalten Sie entweder den Fehler `Forbidden` oder `BadRequest`, wenn Sie versuchen, ein Autorisierungstoken abzurufen.

**Verwendungsbeispiel für die Sprachsynthese-REST-API**

In diesem Beispiel wird „Europa, Westen“ als Azure-Region und `my-private-link-speech.cognitiveservices.azure.com` als DNS-Name der Speech-Ressource (benutzerdefinierte Domäne) verwendet. Der benutzerdefinierte Domänenname `my-private-link-speech.cognitiveservices.azure.com` in diesem Beispiel gehört zu der Ressource, die in der Region „Europa, Westen“ erstellt wurde.

Führen Sie die folgende Anforderung durch, um die Liste der unterstützten Stimmen für die Region abzurufen:

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
Weitere Informationen finden Sie in der [Dokumentation zur Sprachsynthese-REST-API](rest-text-to-speech.md).

Für Speech-Ressourcen mit aktivierten privaten Endpunkten muss die Endpunkt-URL für denselben Vorgang geändert werden. Dieselbe Anforderung sieht dann wie folgt aus:

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Eine ausführliche Erläuterung finden Sie im Unterabschnitt [Erstellen der Endpunkt-URL](#construct-endpoint-url) für das Speech SDK.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und privatem Endpunkt: Verwendung mit dem Speech-SDK

Für die Verwendung des Speech-SDK mit Speech-Ressourcen, für die benutzerdefinierte Domänennamen und private Endpunkte aktiviert sind, sind höchstwahrscheinlich ein Review und Änderungen an Ihrem Anwendungscode notwendig.

In diesem Abschnitt wird `my-private-link-speech.cognitiveservices.azure.com` als DNS-Beispielname (benutzerdefinierte Domäne) für die Speech-Ressource verwendet.

##### <a name="construct-endpoint-url"></a>Erstellen der Endpunkt-URL

In SDK-Szenarios (und Szenarios mit der Spracherkennungs-REST-API für kurze Audiodaten und der Sprachsynthese-REST-API) verwenden Speech-Ressourcen in der Regel die dedizierten regionalen Endpunkte für verschiedene Dienstangebote. Das DNS-Namensformat für diese Endpunkte lautet:

`{region}.{speech service offering}.speech.microsoft.com`

Ein DNS-Name kann beispielsweise folgendermaßen lauten:

`westeurope.stt.speech.microsoft.com`

Die möglichen Werte für die Region (erstes Element des DNS-Namens) werden [hier](regions.md) aufgeführt. (In [diesem Artikel](sovereign-clouds.md) finden Sie Informationen zu Azure Government- und Azure China-Endpunkten.) Die folgende Tabelle enthält die möglichen Werte für das jeweilige Speech-Angebot (zweites Element des DNS-Namens):

| DNS-Namenswert | Speech-Angebot                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Benutzerdefinierte Befehle](custom-commands.md)                       |
| `convai`       | [Unterhaltungstranskription](conversation-transcription.md) |
| `s2s`          | [Sprachübersetzung](speech-translation.md)                 |
| `stt`          | [Spracherkennung](speech-to-text.md)                         |
| `tts`          | [Sprachsynthese](text-to-speech.md)                         |
| `voice`        | [Custom Voice](how-to-custom-voice.md)                      |

Das obige Beispiel (`westeurope.stt.speech.microsoft.com`) steht also für den Spracherkennungs-Endpunkt in der Region „Europa, Westen“.

Für private Endpunkte aktivierte Endpunkte kommunizieren mit Speech-Diensten über einen speziellen Proxy. Daher *müssen Sie die Endpunktverbindungs-URLs ändern*. 

Eine URL für einen „Standardendpunkt“ sieht wie folgt aus: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Eine URL für einen privaten Endpunkt sieht wie folgt aus: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**1. Beispiel:** Eine Anwendung kommuniziert mithilfe der folgenden URL (Spracherkennung mit Basismodell für Englisch (USA) in „Europa, Westen“):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Damit Sie die URL in einem Szenario mit aktivierten privaten Endpunkten verwenden können, in dem der benutzerdefinierte Domänenname der Speech-Ressource `my-private-link-speech.cognitiveservices.azure.com` lautet, müssen Sie die URL folgendermaßen anpassen:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Beachten Sie die folgenden Details:

- Der Hostname (`westeurope.stt.speech.microsoft.com`) wird durch den Hostnamen der benutzerdefinierten Domäne (`my-private-link-speech.cognitiveservices.azure.com`) ersetzt.
- Das zweite Element des ursprünglichen DNS-Namens (`stt`) wird zum ersten Element des URL-Pfads und steht vor dem ursprünglichen Pfad. Die ursprüngliche URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` wird folglich zu `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

**2. Beispiel:** Eine Anwendung verwendet die folgende URL für die Sprachsynthese mit einem Custom Voice-Modell in der Region „Europa, Westen“:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Im Folgenden ist eine entsprechende URL mit einem privaten Endpunkt aufgeführt, bei der der benutzerdefinierte Domänenname der Speech-Ressource `my-private-link-speech.cognitiveservices.azure.com` lautet:

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Es wird das gleiche Prinzip wie im ersten Beispiel angewendet, doch das zentrale Element ist dieses Mal `voice`.

##### <a name="modifying-applications"></a>Ändern von Anwendungen

Führen Sie die folgenden Schritte aus, um Ihren Code zu ändern:

1. Bestimmen Sie die Anwendungsendpunkt-URL:

   - [Aktivieren Sie die Protokollierung für Ihre Anwendung](how-to-use-logging.md), und führen Sie die Anwendung aus, um Aktivitäten zu protokollieren.
   - Suchen Sie in der Protokolldatei nach `SPEECH-ConnectionUrl`. Der `value`-Parameter in den entsprechenden Zeilen enthält die vollständige URL, die Ihre Anwendung verwendet hat, um die Speech-Dienste zu erreichen.

   Beispiel:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Die Anwendung hat in diesem Beispiel also die folgende URL verwendet:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Erstellen Sie eine `SpeechConfig`-Instanz, indem Sie eine vollständige Endpunkt-URL verwenden:

   1. Ändern Sie den festgelegten Endpunkt wie zuvor im Abschnitt [Erstellen der Endpunkt-URL](#construct-endpoint-url) beschrieben.

   1. Passen Sie die Erstellung der `SpeechConfig`-Instanz an. Ihre Anwendung enthält wahrscheinlich eine Zeile, die der folgenden ähnelt:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Diese funktioniert für Speech-Ressourcen mit aktivierten privaten Endpunkten nicht, da der Hostname und die URL sich wie in den vorherigen Abschnitten beschrieben ändern. Wenn Sie Ihre vorhandene Anwendung ohne Änderungen mit dem Schlüssel der Ressource mit aktivierten privaten Endpunkten verwenden, tritt der Authentifizierungsfehler 401 auf.

      Damit dies funktioniert, müssen Sie die Instanziierung der `SpeechConfig`-Klasse ändern und die Initialisierung mit FromEndpoint oder WithEndpoint verwenden. Nehmen Sie an, dass die folgenden beiden Variablen definiert sind:
      - `subscriptionKey` enthält den Schlüssel der Speech-Ressource, für die private Endpunkte aktiviert sind.
      - `endPoint` enthält die vollständige, *geänderte* Endpunkt-URL (mit dem Typ, der in der entsprechenden Programmiersprache erforderlich ist). In diesem Beispiel sollte die Variable den folgenden Inhalt aufweisen:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Erstellen Sie eine `SpeechConfig`-Instanz:
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> Die Abfrageparameter im Endpunkt-URI werden nicht geändert – auch dann nicht, wenn sie von anderen APIs festgelegt wurden. Wenn die Erkennungssprache z. B. im URI als Abfrageparameter `language=en-US` definiert ist, über die entsprechende Eigenschaft aber auf `ru-RU` festgelegt ist, wird die Spracheinstellung im URI verwendet. Die tatsächlich verwendete Sprache ist also `en-US`.
>
> Im Endpunkt-URI festgelegte Parameter haben immer Vorrang. Nur Parameter, die nicht im Endpunkt-URI angegeben sind, können von anderen APIs überschrieben werden.

Nachdem Sie diese Änderung vorgenommen haben, sollte Ihre Anwendung mit Speech-Ressourcen mit aktivierten privaten Endpunkten kompatibel sein. Eine nahtlosere Unterstützung für Szenarios mit privaten Endpunkten ist in Arbeit.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>Verwenden einer Speech-Ressource mit benutzerdefiniertem Domänennamen und ohne private Endpunkte

In diesem Artikel wurde bereits mehrfach darauf hingewiesen, dass das Aktivieren einer benutzerdefinierten Domäne für eine Speech-Ressource *nicht* rückgängig gemacht werden kann. Eine solche Ressource kommuniziert im Vergleich zu Ressourcen mit [regionalen Endpunktnamen](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) auf andere Weise mit Speech-Diensten.

In diesem Abschnitt wird erläutert, wie Sie eine Speech-Ressource mit aktiviertem benutzerdefinierten Domänennamen, aber *ohne* private Endpunkte, mit den Speech-REST-APIs und dem [Speech-SDK](speech-sdk.md) verwenden. Dabei kann es sich um eine Ressource handeln, die in einem Szenario mit privaten Endpunkten eingesetzt wurde, deren private Endpunkte jedoch gelöscht wurden.

#### <a name="dns-configuration"></a>DNS-Konfiguration

Rufen Sie sich ins Gedächtnis, wie der DNS-Name einer benutzerdefinierten Domäne der Speech-Ressource mit aktivierten privaten Endpunkten [über öffentliche Netzwerke aufgelöst wird](#resolve-dns-from-other-networks). In diesem Fall verweist die aufgelöste IP-Adresse auf einen Proxyendpunkt für ein virtuelles Netzwerk. Dieser Endpunkt wird zum Weiterleiten des Netzwerkdatenverkehrs an die Cognitive Services-Ressource verwendet, für die private Endpunkte aktiviert sind.

Wenn *alle* privaten Endpunkte einer Ressource entfernt werden (bzw. direkt nach der Aktivierung des benutzerdefinierten Domänennamens), wird der CNAME-Eintrag der Speech-Ressource erneut bereitgestellt. Dieser verweist nun auf die IP-Adresse des zugehörigen [regionalen Cognitive Services-Endpunkts](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Die Ausgabe des Befehls `nslookup` sieht also wie folgt aus:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Vergleichen Sie diese mit der Ausgabe in [diesem Abschnitt](#resolve-dns-from-other-networks).

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Verwenden einer Speech-Ressource mit benutzerdefiniertem Domänennamen und ohne private Endpunkte: Verwendung mit den REST-APIs

##### <a name="speech-to-text-rest-api-v30"></a>Spracherkennungs-REST-API 3.0

Die Verwendung der Spracherkennungs-REST-API 3.0 entspricht dem Szenario für [Speech-Ressourcen mit aktivierten privaten Endpunkten](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Spracherkennungs-REST-API für kurze Audiodaten und Sprachsynthese-REST-API

In diesem Fall besteht im Allgemeinen kein Unterschied zwischen der Verwendung der Spracherkennungs-REST-API für kurze Audiodaten und der Sprachsynthese-REST-API. Es gibt lediglich eine Ausnahme. Beachten Sie hierzu den folgenden Hinweis. Beide APIs sollten wie in den Dokumentationen [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) und [Sprachsynthese-REST-API](rest-text-to-speech.md) beschrieben eingesetzt werden.

> [!NOTE]
> Wenn Sie die Spracherkennungs-REST-API für kurze Audiodaten und die Sprachsynthese-REST-API in Szenarien mit benutzerdefinierten Domänen verwenden, nutzen Sie einen Abonnementschlüssel, der über den `Ocp-Apim-Subscription-Key`-Header übergeben wird. (Weitere Informationen finden Sie unter [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#request-headers) und [Sprachsynthese-REST-API](rest-text-to-speech.md#request-headers))
>
> Das Verwenden eines Autorisierungstokens und dessen Übergabe an den speziellen Endpunkt über den `Authorization`-Header funktioniert *nur*, wenn Sie die Zugriffsoption **Alle Netzwerke** im Abschnitt **Netzwerk** Ihrer Speech-Ressource aktiviert haben. In anderen Fällen erhalten Sie entweder den Fehler `Forbidden` oder `BadRequest`, wenn Sie versuchen, ein Autorisierungstoken abzurufen.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Verwenden einer Speech-Ressource mit benutzerdefiniertem Domänennamen und ohne private Endpunkte: Verwendung mit dem Speech-SDK

Die Verwendung des Speech SDK mit Speech-Ressourcen mit aktivierter benutzerdefinierter Domäne *ohne* private Endpunkte entspricht dem allgemeinen Fall, wie er in der [Dokumentation zum Speech SDK](speech-sdk.md) beschrieben ist.

Falls Sie Ihren Code geändert haben, um ihn mit einer [Speech-Ressource mit aktiviertem privaten Endpunkt](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) zu verwenden, beachten Sie Folgendes.

Im Abschnitt zu [Speech-Ressourcen mit aktivierten privaten Endpunkten](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) wurde erläutert, wie die Endpunkt-URL bestimmt, geändert und über die Initialisierungen FromEndpoint und WithEndpoint der Klasseninstanz `SpeechConfig` funktionsfähig gemacht werden kann.

Wenn Sie diese Anwendung jedoch ausführen, nachdem alle privaten Endpunkte entfernt wurden (mit etwas Zeit für die erneute Bereitstellung des entsprechenden DNS-Eintrags), tritt ein interner Dienstfehler (404) auf. Das liegt daran, dass der [DNS-Eintrag](#dns-configuration) jetzt auf den regionalen Cognitive Services-Endpunkt anstatt auf den Proxy des virtuellen Netzwerks verweist. Dort werden URL-Pfade wie `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` nicht gefunden.

Sie müssen ein Rollback Ihrer Anwendung auf die Standardinstanziierung von `SpeechConfig` im Stil des folgenden Codes ausführen:

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>Preise

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Weitere Informationen

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [Spracherkennungs-REST-API](rest-speech-to-text.md)
* [Text-to-Speech-REST-API](rest-text-to-speech.md)