---
title: Verwenden von Speech Services mit privaten Endpunkten
titleSuffix: Azure Cognitive Services
description: Anleitung zur Verwendung von Speech Services mit privaten Azure Private Link-Endpunkten
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: alexeyo
ms.openlocfilehash: 01a0171ed2b660fbabebf4276a74f8a3ea631bde
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516529"
---
# <a name="using-speech-services-with-private-endpoints-provided-by-azure-private-link"></a>Verwendung von Speech Services mit privaten Azure Private Link-Endpunkten

[Azure Private Link](../../private-link/private-link-overview.md) ermöglicht das Herstellen von Verbindungen mit verschiedenen PaaS-Diensten in Azure über einen [privaten Endpunkt](../../private-link/private-endpoint-overview.md). Ein privater Endpunkt ist eine private IP-Adresse in einem bestimmten [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) und Subnetz.

In diesem Artikel wird erläutert, wie Sie Private Link und private Endpunkte mit Azure Cognitive Speech Services einrichten und verwenden. 

> [!NOTE]
> In diesem Artikel werden die Besonderheiten bei der Einrichtung und Verwendung von Private Link mit Azure Cognitive Speech Services erläutert. Bevor Sie fortfahren, sollten Sie den allgemeinen Artikel zur [Verwendung virtueller Netzwerke mit Cognitive Services](../cognitive-services-virtual-networks.md) lesen.

Für die Aktivierung einer Speech-Ressource für Szenarios mit privaten Endpunkten sind folgende Schritte erforderlich:
- [Erstellen eines benutzerdefinierten Domänennamens für die Speech-Ressource](#create-custom-domain-name)
- [Erstellen und Konfigurieren privater Endpunkte](#enabling-private-endpoints)
- [Anpassen vorhandener Anwendungen und Lösungen](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Wenn Sie später alle privaten Endpunkte entfernen, aber die Ressource weiterverwenden möchten, können Sie in [diesem Abschnitt](#using-speech-resource-with-custom-domain-name-without-private-endpoints) nachlesen, wie Sie vorgehen müssen.

## <a name="create-custom-domain-name"></a>Erstellen von benutzerdefinierten Domänennamen

Für private Endpunkte müssen [benutzerdefinierte Cognitive Services-Unterdomänennamen](../cognitive-services-custom-subdomains.md) verwendet werden. Mithilfe der folgenden Anweisungen können Sie einen für Ihre Speech-Ressource erstellen.

> [!WARNING]
> Eine Speech-Ressource mit aktiviertem benutzerdefinierten Domänennamen interagiert auf andere Weise mit Speech Services. Höchstwahrscheinlich müssen Sie Ihren Anwendungscode für Szenarios [mit aktivierten privaten Endpunkten](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) und [**ohne** aktivierte private Endpunkte](#using-speech-resource-with-custom-domain-name-without-private-endpoints) anpassen.
>
> Die Aktivierung des benutzerdefinierten Domänennamens kann [**nicht rückgängig gemacht werden**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). Sie können den [regionalen Namen](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) nur wiederherstellen, indem Sie eine neue Speech-Ressource erstellen. 
>
> Insbesondere in Fällen, in denen der Speech-Ressource viele benutzerdefinierte Modelle und Projekte zugeordnet sind, die über [Speech Studio](https://speech.microsoft.com/) erstellt wurden, wird **dringend** empfohlen, die Konfiguration an einer Testressource auszuprobieren und Produktionsressourcen erst im Anschluss zu bearbeiten.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

- Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich bei Ihrem Azure-Konto an.
- Wählen Sie die erforderliche Speech-Ressource aus.
- Wählen Sie *Netzwerk* (unter *Ressourcenverwaltung*) aus. 
- Klicken Sie auf der Registerkarte *Firewalls und virtuelle Netzwerke* (Standard) auf die Schaltfläche **Benutzerdefinierten Domänennamen generieren**.
- Dadurch wird ein Panel mit Anweisungen zum Erstellen einer eindeutigen benutzerdefinierten Unterdomäne für Ihre Ressource geöffnet.
> [!WARNING]
> Nachdem Sie einen benutzerdefinierten Domänennamen erstellt haben, kann dieser **nicht mehr** geändert werden. Weitere Informationen finden Sie in der obigen Warnung.
- Nachdem der Vorgang abgeschlossen wurde, sollten Sie *Schlüssel und Endpunkt* (unter *Ressourcenverwaltung*) auswählen und sich vergewissern, dass der neue Endpunktname für Ihre Ressource das folgende Format aufweist: <p />`{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Für diesen Abschnitt muss PowerShell 7.x oder höher mit Version 5.1.0 oder höher des Azure PowerShell-Moduls lokal ausgeführt werden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Installieren des Azure PowerShell-Moduls) Informationen dazu.

Bevor Sie fortfahren, müssen Sie `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="verify-custom-domain-name-availability"></a>Überprüfen der Verfügbarkeit des benutzerdefinierten Domänennamens

Sie müssen überprüfen, ob die gewünschte benutzerdefinierte Domäne verfügbar ist. Verwenden Sie hierfür die [checkDomainAvailability](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)-Methode der Cognitive Services-REST-API. Die einzelnen Schritte werden in den Kommentaren im folgenden Codeblock erklärt.

> [!TIP]
> Der folgende Code funktioniert **nicht** in Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
Set-AzContext -SubscriptionId $subId

# Preparing OAuth token which is used in request
# to Cognitive Services REST API
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Preparing and executing the request to Cognitive Services REST API
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
Wenn der gewünschte Name verfügbar ist, sieht die Antwort folgendermaßen aus:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Wenn der Name bereits verwendet wird, erhalten Sie die folgende Antwort:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="enabling-custom-domain-name"></a>Aktivieren des benutzerdefinierten Domänennamens

Verwenden Sie das Cmdlet [Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount), um den benutzerdefinierten Domänennamen für die ausgewählte Speech-Ressource zu aktivieren. Die einzelnen Schritte werden in den Kommentaren im folgenden Codeblock erklärt.

> [!WARNING]
> Nach erfolgreicher Ausführung des folgenden Codes können Sie einen benutzerdefinierten Domänennamen für Ihre Speech-Ressource erstellen. Dieser Name kann **nicht** geändert werden. Weitere Informationen finden Sie in der obigen Warnung.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource
# WARNING! THIS IS NOT REVERSIBLE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Abschnitt ist die aktuelle Version der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="verify-custom-domain-name-availability"></a>Überprüfen der Verfügbarkeit des benutzerdefinierten Domänennamens

Sie müssen überprüfen, ob die gewünschte benutzerdefinierte Domäne verfügbar ist. Verwenden Sie hierfür die [checkDomainAvailability](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)-Methode der Cognitive Services-REST-API. 

Kopieren Sie den folgenden Codeblock, fügen Sie den benutzerdefinierten Domänennamen ein, und speichern Sie ihn in der Datei `subdomain.json`.

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
Wenn der gewünschte Name verfügbar ist, sieht die Antwort folgendermaßen aus:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Wenn der Name bereits verwendet wird, erhalten Sie die folgende Antwort:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enabling-custom-domain-name"></a>Aktivieren des benutzerdefinierten Domänennamens

Verwenden Sie den Befehl [az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update), um den benutzerdefinierten Domänennamen für die ausgewählte Speech-Ressource zu aktivieren.

Wählen Sie das Azure-Abonnement mit der Speech-Ressource aus. Wenn nur ein aktives Abonnement in Ihrem Azure-Konto vorhanden ist, können Sie diesen Schritt überspringen. Ersetzen Sie `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` durch Ihre Azure-Abonnement-ID.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Legen Sie den benutzerdefinierten Domänennamen auf die ausgewählte Ressource fest. Ersetzen Sie die Beispielparameterwerte durch die tatsächlichen Parameterwerte, und führen Sie den folgenden Befehl aus.
> [!WARNING]
> Nach erfolgreicher Ausführung des folgenden Befehls können Sie einen benutzerdefinierten Domänennamen für Ihre Speech-Ressource erstellen. Dieser Name kann **nicht** geändert werden. Weitere Informationen finden Sie in der obigen Warnung.
```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enabling-private-endpoints"></a>Aktivieren privater Endpunkte

Sie können private Endpunkte über das Azure-Portal, Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI) abrufen.

Es wird empfohlen, die an das virtuelle Netzwerk angefügte [private DNS-Zone](../../dns/private-dns-overview.md) mit den erforderlichen Updates für private Endpunkte zu verwenden. Diese wurde standardmäßig beim Bereitstellungsprozess erstellt. Wenn Sie jedoch einen eigenen DNS-Server verwenden, müssen Sie möglicherweise zusätzliche Änderungen an Ihrer DNS-Konfiguration vornehmen. Weitere Informationen finden Sie im Abschnitt [DNS für private Endpunkte](#dns-for-private-endpoints). Sie sollten sich für eine DNS-Strategie entscheiden, _ *bevor** private Endpunkte für eine Speech-Produktionsressource bereitgestellt werden. Diese sollten Sie zudem testen. Das gilt insbesondere dann, wenn Sie einen eigenen DNS-Server verwenden.

Die folgenden Artikel enthalten Anleitungen zum Erstellen privater Endpunkte. In diesen Artikeln wird eine Web-App als Beispielressource verwendet, für die private Endpunkte aktiviert werden sollen. Verwenden Sie stattdessen die folgenden Parameter:

| Einstellung             | Wert                                    |
|---------------------|------------------------------------------|
| Ressourcentyp       | **Microsoft.CognitiveServices/accounts** |
| Resource            | **\<your-speech-resource-name>**         |
| Zielunterressource | **Konto**                              |

- [Schnellstart: Erstellen eines privaten Endpunkts mit dem Azure-Portal](../../private-link/create-private-endpoint-portal.md)
- [Erstellen eines privaten Endpunkts mit Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Erstellen eines privaten Endpunkts mit der Azure CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>DNS für private Endpunkte

Machen Sie sich mit den Grundprinzipien von [DNS für private Endpunkte in Cognitive Services-Ressourcen](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints) vertraut. Testen Sie anschließend, ob Ihre DNS-Konfiguration ordnungsgemäß funktioniert. Weitere Informationen finden Sie in den nächsten Unterabschnitten.

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a>Obligatorisch: DNS-Auflösung über das virtuelle Netzwerk

In diesem Abschnitt wird `my-private-link-speech.cognitiveservices.azure.com` als DNS-Beispielname für die Speech-Ressource verwendet.

Melden Sie sich bei einem virtuellen Computer an, der sich in dem virtuellen Netzwerk befindet, an das Sie Ihren privaten Endpunkt angefügt haben. Öffnen Sie die Windows-Eingabeaufforderung oder die Bash-Shell, führen Sie den Befehl nslookup aus, und vergewissern Sie sich, dass dieser den benutzerdefinierten Domänennamen Ihrer Ressource richtig auflöst:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
Überprüfen Sie, ob die aufgelöste IP-Adresse der Adresse Ihres privaten Endpunkts entspricht.

#### <a name="optional-check-dns-resolution-from-other-networks"></a>Optional: DNS-Auflösung über andere Netzwerke

Diese Überprüfung ist erforderlich, wenn Sie die Speech-Ressource, für die private Endpunkte aktiviert wurden, im Hybridmodus verwenden möchten. Dieser wird verwendet, wenn Sie die Zugriffsoption *Alle Netzwerke* oder *Ausgewählte Netzwerke und private Endpunkte* im Abschnitt *Netzwerk* Ihrer Ressource aktiviert haben. Wenn Sie nur über private Endpunkte auf die Ressource zugreifen, können Sie diesen Abschnitt überspringen.

In diesem Abschnitt wird `my-private-link-speech.cognitiveservices.azure.com` als DNS-Beispielname für die Speech-Ressource verwendet.

Öffnen Sie auf einem Computer in einem Netzwerk, von dem aus der Zugriff auf die Ressource zugelassen werden soll, die Windows-Eingabeaufforderung oder die Bash-Shell, führen Sie den Befehl nslookup aus, und vergewissern Sie sich, dass dieser den benutzerdefinierten Domänennamen Ihrer Ressource richtig auflöst:
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

Sie sehen, dass die aufgelöste IP-Adresse auf einen VNET-Proxyendpunkt verweist. Dieser wird zum Weiterleiten des Netzwerkdatenverkehrs an die Cognitive Services-Ressource verwendet, für die private Endpunkte aktiviert sind. Dieses Verhalten unterscheidet sich bei einer Ressource, für die ein benutzerdefinierter Domänenname aktiviert ist, aber *keine* privaten Endpunkte konfiguriert wurden. Weitere Informationen finden Sie in [diesem Abschnitt](#dns-configuration).

## <a name="adjusting-existing-applications-and-solutions"></a>Anpassen vorhandener Anwendungen und Lösungen 

Eine Speech-Ressource mit aktivierter benutzerdefinierten Domäne interagiert auf andere Weise mit Speech Services. Das gilt für Speech-Ressourcen mit aktivierter benutzerdefinierter Domäne [mit](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) und [ohne](#using-speech-resource-with-custom-domain-name-without-private-endpoints) private Endpunkte. In diesem Abschnitt finden Sie Informationen zu beiden Szenarios.

### <a name="using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und privaten Endpunkten

Eine Speech-Ressource, für die benutzerdefinierte Domänennamen und private Endpunkte aktiviert sind, interagiert auf andere Weise mit Speech Services. In diesem Abschnitt wird erläutert, wie eine solche Ressource mit Speech Services-REST-API und dem [Speech-SDK](speech-sdk.md) verwendet wird.

> [!NOTE]
> Beachten Sie, dass eine Speech-Ressource ohne private Endpunkte, aber mit einem **benutzerdefinierten Domänennamen** auf andere Weise als eine Speech-Ressource mit aktivierten privaten Endpunkten mit Speech Services interagiert. Wenn Sie eine solche Ressource besitzen (also falls Sie beispielsweise eine Ressource mit privaten Endpunkten besaßen, diese aber entfernt haben), sollten Sie den [Abschnitt zu diesem Thema](#using-speech-resource-with-custom-domain-name-without-private-endpoints) lesen.

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und privaten Endpunkten mit der REST-API

In diesem Abschnitt wird `my-private-link-speech.cognitiveservices.azure.com` als DNS-Beispielname (benutzerdefinierte Domäne) für die Speech-Ressource verwendet.

##### <a name="note-on-speech-services-rest-api"></a>Anmerkung zur Speech Services-REST-API

Speech Services beinhaltet eine REST-API für die [Spracherkennung](rest-speech-to-text.md) und die [Sprachsynthese](rest-text-to-speech.md). Bei Szenarios mit aktivierten privaten Endpunkten muss Folgendes berücksichtigt werden:

Für die Spracherkennung gibt es zwei unterschiedliche REST-APIs. Jede API dient einem anderen Zweck, verwendet unterschiedliche Endpunkte und erfordert einen anderen Ansatz, wenn sie in einem Szenario mit aktivierten privaten Endpunkten verwendet wird.

Es gibt folgende REST-APIs für die Spracherkennung:
- Die [Spracherkennungs-REST-API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) wird für die [Batchtranskription](batch-transcription.md) und [Custom Speech](custom-speech-overview.md) eingesetzt. Version 3.0 ist die [Nachfolgeversion von Version 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- Die [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) wird für die Onlinetranskription eingesetzt. 

Die Verwendung der Spracherkennungs-REST-API für kurze Audiodaten und der Sprachsynthese-REST-API ist in Szenarios mit privaten Endpunkten identisch und entspricht dem [Speech-SDK-Ansatz](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk), der im Verlauf dieses Artikels erläutert wird. 

Die Spracherkennungs-REST-API 3.0 verwendet andere Endpunkte und erfordert daher einen anderen Ansatz für Szenarios mit aktivierten privaten Endpunkten.

Beide Fälle werden in den nächsten Unterabschnitten beschrieben.


##### <a name="speech-to-text-rest-api-v30"></a>Spracherkennungs-REST-API 3.0

Speech-Ressourcen verwenden üblicherweise [regionale Cognitive Services-Endpunkte](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) für die Kommunikation mit der [Spracherkennungs-REST-API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Für diese Ressourcen gilt das folgende Benennungsformat: <p/>`{region}.api.cognitive.microsoft.com`

Hier sehen Sie ein Beispiel für eine Anforderungs-URL:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Nachdem Sie benutzerdefinierte Domänen für eine Speech-Ressource aktiviert haben (für private Endpunkte erforderlich), verwendet diese Ressource das folgende DNS-Namensmuster für den grundlegenden REST-API-Endpunkt: <p/>`{your custom name}.cognitiveservices.azure.com`

In diesem Beispiel lautet der Name des REST-API-Endpunkts dann: <p/>`my-private-link-speech.cognitiveservices.azure.com`

Die Anforderungs-URL aus dem Beispiel muss in folgendes Format konvertiert werden:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Die URL sollte von dem virtuellen Netzwerk aus erreichbar sein, dem der private Endpunkt angefügt ist (bei [korrekter DNS-Auflösung](#mandatory-check-dns-resolution-from-the-virtual-network)).

Sie müssen also nach dem Aktivieren des benutzerdefinierten Domänennamens für eine Speech-Ressource den Hostnamen in allen Anforderungs-URLs durch den neuen benutzerdefinierten Domänenhostnamen ersetzen. Die restlichen Bestandteile der Anforderung (wie der Pfad `/speechtotext/v3.0/transcriptions` im obigen Beispiel) bleiben unverändert.

> [!TIP]
> Einige Kunden haben Anwendungen entwickelt, die die Region des DNS-Namens des regionalen Endpunkts verwenden, um beispielsweise eine Anforderung an die Speech-Ressource zu senden, die in der jeweiligen Azure-Region bereitgestellt wurde.
>
> Der benutzerdefinierte Domänenname von Speech-Ressourcen enthält **keine** Informationen zu der Region, in der die Ressource bereitgestellt wird. Die oben beschriebene Anwendungslogik funktioniert also **nicht** und muss abgeändert werden.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Spracherkennungs-REST-API für kurze Audiodaten und Sprachsynthese-REST-API

Die [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) und die [Sprachsynthese-REST-API](rest-text-to-speech.md) verwenden zwei Endpunktarten:
- [Regionale Cognitive Services-Endpunkte](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) für die Kommunikation mit der Cognitive Services-REST-API, um ein Autorisierungstoken abzurufen
- Spezielle Endpunkte für alle sonstigen Vorgänge

Eine ausführliche Beschreibung dieser speziellen Endpunkte und Informationen zur Transformation der URL für eine Speech-Ressource mit aktivierten privaten Endpunkten finden Sie in [diesem Abschnitt](#general-principle) unter „Verwendung mit dem Speech-SDK“. Das Prinzip für das SDK gilt auch für die Spracherkennungs-API 1.0 und die Sprachsynthese-REST-API.

Lesen Sie das Material im entsprechenden Unterabschnitt im vorherigen Absatz, und sehen Sie sich dann das folgende Beispiel an. Im Beispiel wird die Sprachsynthese-REST-API beschrieben. Die Verwendung der Spracherkennungs-REST-API für kurze Audiodaten ist jedoch identisch.

> [!NOTE]
> Wenn Sie die **Spracherkennungs-REST-API für kurze Audiodaten** in Szenarios mit privaten Endpunkten einsetzen, müssen Sie ein Autorisierungstoken verwenden, das über den [Header](rest-speech-to-text.md#request-headers) `Authorization` [übergeben](rest-speech-to-text.md#request-headers) wurde. Ein über den Header `Ocp-Apim-Subscription-Key` an den speziellen Endpunkt übergebener Speech-Abonnementschlüssel funktioniert **nicht** und generiert den Fehler 401.

**Verwendungsbeispiel für die Sprachsynthese-REST-API**

In diesem Beispiel wird „Europa, Westen“ als Azure-Region und `my-private-link-speech.cognitiveservices.azure.com` als DNS-Name der Speech-Ressource (benutzerdefinierte Domäne) verwendet. Der benutzerdefinierte Domänenname `my-private-link-speech.cognitiveservices.azure.com` in diesem Beispiel gehört zu der Ressource, die in der Region „Europa, Westen“ erstellt wurde.

Die folgenden beiden Vorgänge sind erforderlich, um die Liste der unterstützten Stimmen für die Region abzurufen:

- Rufen Sie das Autorisierungstoken ab:
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Verwenden Sie das Authentifizierungstoken, um die Liste der Stimmen abzurufen:
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
Eine ausführlichere Erläuterung der obigen Schritte finden Sie in der [Dokumentation zur Sprachsynthese-REST-API](rest-text-to-speech.md).

Für Speech-Ressourcen mit aktivierten privaten Endpunkten müssen die Endpunkt-URLs geändert werden, damit diese Abfolge funktioniert. Die Abfolge sollte folgendermaßen aussehen:
- Rufen Sie das Autorisierungstoken ab:
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
Eine ausführlichere Erläuterung finden Sie im Unterabschnitt [Spracherkennungs-REST-API 3.0](#speech-to-text-rest-api-v30).
- Verwenden Sie das Authentifizierungstoken, um die Liste der Stimmen abzurufen:
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Eine ausführlichere Erläuterung finden Sie im Abschnitt [Allgemeines Prinzip](#general-principle) unter „Verwendung mit dem Speech-SDK“.

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und privaten Endpunkten Verwendung mit dem Speech-SDK

Für die Verwendung des Speech-SDK mit Speech-Ressourcen, für die benutzerdefinierte Domänennamen und private Endpunkte aktiviert sind, sind höchstwahrscheinlich ein Review und Änderungen an Ihrem Anwendungscode notwendig. Eine nahtlosere Unterstützung des Szenarios mit privaten Endpunkten ist in Arbeit.

In diesem Abschnitt wird `my-private-link-speech.cognitiveservices.azure.com` als DNS-Beispielname (benutzerdefinierte Domäne) für die Speech-Ressource verwendet.

##### <a name="general-principle"></a>Allgemeines Prinzip

In SDK-Szenarios (und Szenarios mit der Sprachsynthese-REST-API) verwenden Speech-Ressourcen üblicherweise die jeweiligen regionalen Endpunkte für verschiedene Dienstangebote. Das DNS-Namensformat für diese Endpunkte lautet: </p>`{region}.{speech service offering}.speech.microsoft.com`

Beispiel: </p>`westeurope.stt.speech.microsoft.com`

Die möglichen Werte für die Region (erstes Element des DNS-Namens) werden [hier](regions.md) aufgeführt. Die folgende Tabelle enthält die möglichen Werte für das Speech Services-Angebot (zweites Element des DNS-Namens):

| DNS-Namenswert | Speech Services-Angebot                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Benutzerdefinierte Befehle](custom-commands.md)                       |
| `convai`       | [Unterhaltungstranskription](conversation-transcription.md) |
| `s2s`          | [Sprachübersetzung](speech-translation.md)                 |
| `stt`          | [Spracherkennung](speech-to-text.md)                         |
| `tts`          | [Sprachsynthese](text-to-speech.md)                         |
| `voice`        | [Custom Voice](how-to-custom-voice.md)                      |

Das obige Beispiel (`westeurope.stt.speech.microsoft.com`) steht also für den Spracherkennungs-Endpunkt in „Europa, Westen“.

Private Endpunkte kommunizieren mit Speech Services über einen speziellen Proxy. Deshalb müssen die **Endpunktverbindungs-URLs geändert werden**. Eine gewöhnliche Endpunkt-URL weist folgendes Muster auf: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Sollte sie geändert werden in: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**1. Beispiel:** Die Anwendung kommuniziert mithilfe der folgenden URL (Spracherkennung mit Basismodell für Englisch (USA) in „Europa, Westen“): 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Damit diese in einem Szenario mit aktivierten privaten Endpunkten verwendet werden kann, bei denen der benutzerdefinierte Domänenname der Speech-Ressource `my-private-link-speech.cognitiveservices.azure.com` lautet, muss diese URL folgendermaßen geändert werden:
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Genauer Ablauf:
- Der Hostname `westeurope.stt.speech.microsoft.com` wird durch den benutzerdefinierte Domänenhostnamen `my-private-link-speech.cognitiveservices.azure.com` ersetzt.
- Das zweite Element des ursprünglichen DNS-Namens (`stt`) wird zum ersten Element des URL-Pfads. Diesem folgt der ursprüngliche Pfad. Die URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` wird also zu `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.
 
**2. Beispiel:** Die Anwendung kommuniziert mithilfe der folgenden URL (Sprachsynthese mit Custom Voice-Modell in „Europa, Westen“): 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
Damit diese in einem Szenario mit aktivierten privaten Endpunkten verwendet werden kann, bei denen der benutzerdefinierte Domänenname der Speech-Ressource `my-private-link-speech.cognitiveservices.azure.com` lautet, muss diese URL folgendermaßen geändert werden: 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Es wird das gleiche Prinzip wie im ersten Beispiel angewendet, doch das zentrale Element ist dieses Mal `voice`.

##### <a name="modifying-applications"></a>Ändern von Anwendungen

Insbesondere diese beiden Schritte sind erforderlich, um das im vorherigen Abschnitt beschriebene Prinzip auf Ihren Anwendungscode anzuwenden:

- Bestimmen der Endpunkt-URL, die von der Anwendung verwendet wird
- Ändern der Endpunkt-URL wie im vorherigen Abschnitt beschrieben und Erstellen der `SpeechConfig`-Klasseninstanz durch explizite Verwendung der geänderten URL

###### <a name="determining-application-endpoint-url"></a>Bestimmen der Anwendungsendpunkt-URL

- [Aktivieren Sie die Protokollierung für Ihre Anwendung](how-to-use-logging.md), und führen Sie diese aus, um ein Protokoll zu generieren.
- Suchen Sie in der Protokolldatei nach `SPEECH-ConnectionUrl`. Diese Zeichenfolge enthält den Parameter `value`, der wiederum die vollständige URL enthält, die Ihre Anwendung verwendet hat.

Hier sehen Sie ein Beispiel für eine Protokolldateizeile mit der Endpunkt-URL:
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
Die Anwendung hat also die folgende URL verwendet:
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="creating-speechconfig-instance-using-full-endpoint-url"></a>Erstellen einer `SpeechConfig`-Instanz mithilfe der vollständigen Endpunkt-URL

Ändern Sie den Endpunkt, den Sie im vorherigen Abschnitt festgelegt haben, wie unter [Allgemeines Prinzip](#general-principle) beschrieben.

Nun müssen Sie ändern, wie Sie die Instanz von `SpeechConfig` erstellen. Ihre Anwendung enthält wahrscheinlich eine Zeile, die der folgenden ähnelt:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Diese funktioniert für Speech-Ressourcen mit aktivierten privaten Endpunkten nicht, da der Hostname und die URL sich wie im vorherigen Abschnitt beschrieben ändern. Wenn Sie Ihre vorhandene Anwendung ohne Änderungen mit dem Schlüssel der Ressource mit aktivierten privaten Endpunkten verwenden, tritt der Authentifizierungsfehler 401 auf.

Sie müssen zunächst die Instanziierung der Klasse `SpeechConfig` ändern und die Initialisierung FromEndpoint oder WithEndpoint verwenden. Nehmen Sie an, dass die folgenden beiden Variablen definiert sind:
- `subscriptionKey` enthält den Schlüssel der Speech-Ressource, für die private Endpunkte aktiviert sind.
- `endPoint` enthält die vollständige, **geänderte** Endpunkt-URL (mit dem Typ, der in der entsprechenden Programmiersprache erforderlich ist). In diesem Beispiel sollte die Variable den folgenden Inhalt aufweisen:
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
Die Klasse `SpeechConfig` muss anschließend folgendermaßen instanziiert werden:
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
> Die Abfrageparameter im Endpunkt-URI werden nicht geändert – auch dann nicht, wenn sie von anderen APIs festgelegt wurden. Wenn die Erkennungssprache im URL als Abfrageparameter language=en-US definiert ist und über die zugehörige Eigenschaft auch auf ru-RU festgelegt wurde, hat die Spracheinstellung im URI Vorrang. Die verwendete Sprache ist also en-US. Nur die Parameter, die nicht im Endpunkt-URI angegeben sind, können von anderen APIs festgelegt werden.

Nachdem Sie diese Änderung vorgenommen haben, sollte Ihre Anwendung mit Speech-Ressourcen mit aktivierten privaten Endpunkten kompatibel sein. Eine nahtlosere Unterstützung des Szenarios mit privaten Endpunkten ist in Arbeit.

### <a name="using-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und ohne private Endpunkte

In diesem Artikel wurde mehrmals betont, dass die Aktivierung der benutzerdefinierten Domäne für eine Speech-Ressource **nicht rückgängig gemacht werden kann** und eine solche Ressource nicht auf die herkömmliche Weise (also nicht wie bei der Verwendung von [regionalen Endpunktnamen](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)) mit Speech Services kommuniziert.

In diesem Abschnitt wird erläutert, wie Sie eine Speech-Ressource mit aktiviertem benutzerdefinierten Domänennamen, aber **ohne** private Endpunkte, mit der Speech Services-REST-API und dem [Speech-SDK](speech-sdk.md) verwenden. Dabei kann es sich um eine Ressource handeln, die in einem Szenario mit privaten Endpunkten eingesetzt wurde, deren private Endpunkte jedoch gelöscht wurden.

#### <a name="dns-configuration"></a>DNS-Konfiguration

Rufen Sie sich ins Gedächtnis, wie der DNS-Name einer benutzerdefinierten Domäne der Speech-Ressource mit aktivierten privaten Endpunkten [über öffentliche Netzwerke aufgelöst wird](#optional-check-dns-resolution-from-other-networks). In diesem Fall verweist die aufgelöste IP-Adresse auf einen VNET-Proxyendpunkt. Dieser wird zum Weiterleiten des Netzwerkdatenverkehrs an die Cognitive Services-Ressource verwendet, für die private Endpunkte aktiviert sind.

Wenn **alle** privaten Endpunkte einer Ressource entfernt werden (bzw. direkt nach der Aktivierung des benutzerdefinierten Domänennamens), wird der CNAME-Eintrag der Speech-Ressource erneut bereitgestellt. Dieser verweist nun auf die IP-Adresse des zugehörigen [regionalen Cognitive Services-Endpunkts](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

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
Vergleichen Sie diese mit der Ausgabe in [diesem Abschnitt](#optional-check-dns-resolution-from-other-networks).

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und ohne private Endpunkte mit der REST-API

##### <a name="speech-to-text-rest-api-v30"></a>Spracherkennungs-REST-API 3.0

Die Verwendung der Spracherkennungs-REST-API 3.0 entspricht dem Szenario für [Speech-Ressourcen mit aktivierten privaten Endpunkten](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Spracherkennungs-REST-API für kurze Audiodaten und Sprachsynthese-REST-API

In diesem Fall besteht nur ein Unterschied zwischen der Verwendung der Spracherkennungs-REST-API für kurze Audiodaten und der Sprachsynthese-REST-API. Dieser wird im folgenden Hinweis erläutert. Beide APIs sollten wie in den Dokumentationen [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) und [Sprachsynthese-REST-API](rest-text-to-speech.md) beschrieben eingesetzt werden.

> [!NOTE]
> Wenn Sie die **Spracherkennungs-REST-API für kurze Audiodaten** in Szenarios mit benutzerdefinierten Domänen einsetzen, müssen Sie ein Autorisierungstoken verwenden, das über den [Header](rest-speech-to-text.md#request-headers) `Authorization` [übergeben](rest-speech-to-text.md#request-headers) wurde. Ein über den Header `Ocp-Apim-Subscription-Key` an den speziellen Endpunkt übergebener Speech-Abonnementschlüssel funktioniert **nicht** und generiert den Fehler 401.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und ohne private Endpunkte Verwendung mit dem Speech-SDK

Für die Verwendung des Speech-SDK mit Speech-Ressourcen, für die benutzerdefinierte Domänennamen, aber **keine** privaten Endpunkte aktiviert sind, sind höchstwahrscheinlich ein Review und Änderungen an Ihrem Anwendungscode notwendig. Beachten Sie, dass diese Änderungen sich vom Szenario einer [Speech-Ressource mit aktivierten privaten Endpunkten](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) **unterscheidet**. Eine nahtlosere Unterstützung des Szenarios mit privaten Endpunkten und benutzerdefinierten Domänen ist in Arbeit.

In diesem Abschnitt wird `my-private-link-speech.cognitiveservices.azure.com` als DNS-Beispielname (benutzerdefinierte Domäne) für die Speech-Ressource verwendet.

Im Abschnitt zu [Speech-Ressourcen mit aktivierten privaten Endpunkten](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) wurde erläutert, wie die verwendete URL bestimmt, geändert und über die Initialisierungen FromEndpoint und WithEndpoint der Klasseninstanz `SpeechConfig` funktionsfähig gemacht werden kann.

Wenn Sie diese Anwendung jedoch ausführen, nachdem alle privaten Endpunkte entfernt wurden (mit etwas Zeit für die erneute Bereitstellung des entsprechenden DNS-Eintrags), tritt ein interner Dienstfehler (404) auf. Das liegt daran, dass der [DNS-Eintrag](#dns-configuration) jetzt auf den regionalen Cognitive Services-Endpunkt anstatt auf den VNET-Proxy verweist. Dort werden URL-Pfade wie `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` nicht gefunden, sodass der Fehler „Nicht gefunden“ (404) angezeigt wird.

Ändern Sie Ihre Anwendung beispielsweise in die Standardinstanziierung von `SpeechConfig` zurück:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Dann wird die Anwendung mit einem Authentifizierungsfehler (401) beendet.

##### <a name="modifying-applications"></a>Ändern von Anwendungen

Folgende Schritte sind erforderlich, damit Ihre Anwendung in einem Szenario mit Speech-Ressourcen eingesetzt werden kann, die benutzerdefinierte Domänennamen, aber keine privaten Endpunkte aufweisen:
- Anfordern des Autorisierungstokens über die Cognitive Services-REST-API
- Instanziieren der Klasse `SpeechConfig` mithilfe der Methode FromAuthorizationToken oder WithAuthorizationToken 

###### <a name="requesting-authorization-token"></a>Anfordern des Autorisierungstokens

In [diesem Artikel](../authentication.md#authenticate-with-an-authentication-token) erfahren Sie, wie Sie das Token über die Cognitive Services-REST-API abrufen können. 

Verwenden Sie den benutzerdefinierten Domänennamen in der Endpunkt-URL. In diesem Beispiel lautet die URL:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Sie finden diese URL im Abschnitt *Schlüssel und Endpunkt* (unter *Ressourcenverwaltung*) Ihrer Speech-Ressource im Azure-Portal.

###### <a name="creating-speechconfig-instance-using-authorization-token"></a>Erstellen einer `SpeechConfig`-Instanz mithilfe des Autorisierungstokens

Sie müssen die Klasse `SpeechConfig` mit dem Autorisierungstoken instanziieren, das Sie im vorherigen Abschnitt abgerufen haben. Nehmen Sie an, dass die folgenden Variablen definiert sind:

- `token` enthält das Autorisierungstoken, das im vorherigen Abschnitt abgerufen wurde.
- `azureRegion` enthält die [Region](regions.md) der Speech-Ressource (z. B. `westeurope`).
- `outError` (nur für [Objective-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror)-Szenarios)

Die Klasse `SpeechConfig` muss anschließend folgendermaßen instanziiert werden:
```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> Der Aufrufer muss sicherstellen, dass das Autorisierungstoken gültig ist. Bevor das Autorisierungstoken abläuft, muss der Aufrufer es aktualisieren, indem er diesen Setter mit einem neuen gültigen Token aufruft. Da bei der Erstellung eines neuen Erkennungs-/Synthetisierungsmoduls Konfigurationswerte kopiert werden, gilt der neue Tokenwert nicht für bereits erstellte Erkennungsmodule. Für bereits erstellte Erkennungs-/Synthetisierungsmodule müssen Sie das Autorisierungstoken des entsprechenden Erkennungs-/Synthetisierungsmoduls festlegen, um das Token zu aktualisieren. Andernfalls treten bei der Erkennung/Synthetisierung Fehler auf.

Nachdem Sie diese Änderung vorgenommen haben, sollte Ihre Anwendung mit Speech-Ressourcen mit aktiviertem benutzerdefinierten Domänennamen und ohne private Endpunkte kompatibel sein. Eine nahtlosere Unterstützung des Szenarios mit privaten Endpunkten und benutzerdefinierten Domänen ist in Arbeit.

## <a name="pricing"></a>Preise

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure Private Link](../../private-link/private-link-overview.md).
* Weitere Informationen zum [Speech-SDK](speech-sdk.md)
* Weitere Informationen zur [Spracherkennungs-REST-API](rest-speech-to-text.md)
* Weitere Informationen zur [Sprachsynthese-REST-API](rest-text-to-speech.md)
