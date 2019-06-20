---
title: Azure-Instanzmetadatendienst | Microsoft-Dokumentation
description: RESTful-Schnittstelle zum Abrufen von Informationen über Compute-, Netzwerk- und anstehende Wartungsereignisse einer Windows-VM.
services: virtual-machines-windows
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 160d494eea4bd597725a4e7c21ad9b763502bee6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65792096"
---
# <a name="azure-instance-metadata-service"></a>Azure-Instanzmetadatendienst

Der Azure-Instanzmetadatendienst stellt Informationen zum Ausführen von Instanzen virtueller Computer bereit, die zum Verwalten und Konfigurieren Ihrer virtuellen Computer verwendet werden können.
Hierzu gehören Informationen wie die SKU, die Netzwerkkonfiguration und bevorstehende Wartungsereignisse. Weitere Informationen zu der Art der verfügbaren Informationen finden Sie unter [Metadaten-APIs](#metadata-apis).

Der Instanzmetadatendienst von Azure ist ein REST-Endpunkt, der für alle IaaS-VMs verfügbar ist, die mit dem neuen [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) erstellt wurden.
Der Endpunkt steht unter einer bekannten, nicht routingfähigen IP-Adresse zur Verfügung (`169.254.169.254`), auf die nur innerhalb der VM zugegriffen werden kann.

> [!IMPORTANT]
> Dieser Dienst ist in allen Azure-Regionen **allgemein verfügbar**.  Er empfängt regelmäßig Updates, um neue Informationen zu Instanzen virtueller Computer verfügbar zu machen. Diese Seite behandelt die neuesten [Metadaten-APIs](#metadata-apis).

## <a name="service-availability"></a>Dienstverfügbarkeit

Der Dienst ist in Azure-Regionen allgemein verfügbar. Unter Umständen sind nicht alle API-Versionen in allen Azure-Regionen verfügbar.

Regions                                        | Verfügbarkeit?                                 | Unterstützte Versionen
-----------------------------------------------|-----------------------------------------------|-----------------
[Allgemein verfügbar in globalen Azure-Regionen](https://azure.microsoft.com/regions/)     | Allgemein verfügbar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Allgemein verfügbar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure China](https://www.azure.cn/)                                                     | Allgemein verfügbar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/)                    | Allgemein verfügbar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Öffentlich, USA, Westen-Mitte](https://azure.microsoft.com/regions/)                           | Allgemein verfügbar | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01

Diese Tabelle wird aktualisiert, wenn Dienstupdates oder neue unterstützte Versionen verfügbar sind.

> [!NOTE]
> 2019-02-01 wird derzeit eingeführt und ist in Kürze in weiteren Regionen verfügbar.

Um den Instanzmetadatendienst zu testen, erstellen Sie eine VM über den [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) oder das [Azure-Portal](https://portal.azure.com) in den oben genannten Regionen, und absolvieren Sie die folgenden Beispiele.

## <a name="usage"></a>Verwendung

### <a name="versioning"></a>Versionsverwaltung

Für den Instanzmetadatendienst wird die Versionsverwaltung genutzt. Versionen sind obligatorisch, und die aktuelle Version in globalen Azure-Regionen ist `2018-10-01`. Aktuell unterstützte Versionen: 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01.

Wenn neuere Versionen hinzugefügt werden, kann auf ältere Versionen aus Kompatibilitätsgründen weiterhin zugegriffen werden, falls Ihre Skripts von bestimmten Datenformaten abhängig sind.

Wenn keine Version angegeben ist, wird ein Fehler mit einer Liste der neuesten unterstützten Versionen zurückgegeben.

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

**Anforderung**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Antwort**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Verwenden von Headern

Bei der Abfrage des Instanzmetadatendiensts müssen Sie den Header `Metadata: true` angeben, um sicherzustellen, dass die Anforderung nicht unbeabsichtigt umgeleitet wurde.

### <a name="retrieving-metadata"></a>Abrufen von Metadaten

Instanzmetadaten stehen für die Ausführung von VMs zur Verfügung, die mit dem [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) erstellt/verwaltet werden. Über folgende Anforderung können Sie auf alle Datenkategorien einer Instanz eines virtuellen Computers zugreifen:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Bei Instanzmetadatenabfragen ist Groß-/Kleinschreibung zu berücksichtigen.

### <a name="data-output"></a>Datenausgabe

Standardmäßig gibt der Instanzmetadatendienst Daten im JSON-Format (`Content-Type: application/json`) zurück. Falls angefordert, geben andere APIs jedoch Daten in verschiedenen Formaten zurück.
In der folgenden Tabelle werden andere Datenformate aufgeführt, die die APIs eventuell unterstützen.

API | Standarddatenformat | Andere Formate
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | none
/attested | json | none

Um auf ein nicht standardmäßiges Antwortformat zuzugreifen, geben Sie das angeforderte Format als QueryString-Parameter in der Anforderung an. Beispiel:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Für Blattknoten funktioniert `format=json` nicht. Für diese Abfragen muss `format=text` explizit angegeben werden, wenn das Standardformat JSON ist.

### <a name="security"></a>Sicherheit

Auf den Instanzmetadatendienst-Endpunkt kann nur innerhalb der ausgeführten Instanz des virtuellen Computers an einer nicht routingfähigen IP-Adresse zugegriffen werden. Darüber hinaus wird jede Anforderung mit einem `X-Forwarded-For`-Header vom Dienst abgelehnt.
Zudem müssen Anforderungen einen `Metadata: true`-Header enthalten, um sicherzustellen, dass die eigentliche Anforderung direkt vorgesehen war und nicht Teil einer unbeabsichtigten Umleitung ist.

### <a name="error"></a>Error

Wenn ein Datenelement nicht gefunden wird oder eine Anforderung ungültig ist, gibt der Instanzmetadatendienst standardmäßige HTTP-Fehler zurück. Beispiel:

HTTP-Statuscode | Grund
----------------|-------
200 – OK |
400 – Ungültige Anforderung | Fehlender `Metadata: true`-Header oder fehlendes Format beim Abfragen eines Blattknotens
404 – Nicht gefunden | Das angeforderte Element ist nicht vorhanden.
405 – Methode unzulässig | Es werden ausschließlich `GET`- und `POST`-Anforderungen unterstützt.
429 – Zu viele Anforderungen | Die API unterstützt derzeit maximal 5 Abfragen pro Sekunde.
500 – Dienstfehler     | Wiederholen Sie den Vorgang später.

### <a name="examples"></a>Beispiele

> [!NOTE]
> Alle API-Antworten stellen JSON-Zeichenfolgen dar. Alle folgenden Beispielantworten werden zur besseren Lesbarkeit im Schöndruck gedruckt.

#### <a name="retrieving-network-information"></a>Abrufen von Netzwerkinformationen

**Anforderung**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Abrufen der öffentlichen IP-Adresse

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Abrufen aller Metadaten für eine Instanz

**Anforderung**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"
```

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
    "location": "westus",
    "name": "jubilee",
    "offer": "Windows-10",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftWindowsDesktop",
    "resourceGroupName": "myrg",
    "sku": "rs4-pro",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "17134.345.59",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Abrufen von Metadaten auf einem virtuellen Windows-Computer

**Anforderung**

Die Instanzmetadaten können unter Windows über das `curl`-Programm abgerufen werden:

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2018-10-01 | select -ExpandProperty Content
```

Oder über das folgende `Invoke-RestMethod`-PowerShell-Cmdlet:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2018-10-01 -Method get
```

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftSQLServer",
    "resourceGroupName": "myrg",
    "sku": "Enterprise",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmScaleSetName": "",
    "vmSize": "Standard_DS2",
    "zone": ""
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Metadaten-APIs

#### <a name="the-following-apis-are-available-through-the-metadata-endpoint"></a>Die folgenden APIs stehen über den Metadatenendpunkt zur Verfügung:

Daten | BESCHREIBUNG | Eingeführt in Version
-----|-------------|-----------------------
attested | Siehe [Bestätigte Daten](#attested-data) | 01\.10.2018
identity | Verwaltete Identitäten für Azure-Ressourcen. Siehe [Abrufen eines Zugriffstokens](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | Siehe [Instanz-API](#instance-api) | 2017-04-02
scheduledevents | Siehe [Azure-Metadatendienst: Geplante Ereignisse (Vorschau) für Windows-VMs](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>Instanz-API
##### <a name="the-following-compute-categories-are-available-through-the-instance-api"></a>Die folgenden Computekategorien werden über die Instanz-API zur Verfügung gestellt:

> [!NOTE]
> Über den Metadatenendpunkt sind die folgenden Kategorien über „Instanz/Compute“ zugänglich.

Daten | BESCHREIBUNG | Eingeführt in Version
-----|-------------|-----------------------
azEnvironment | Azure-Umgebung, in der die VM ausgeführt wird | 01\.10.2018
customData | Siehe [Benutzerdefinierte Daten](#custom-data) | 2019-02-01
location | Azure-Region, in der die VM ausgeführt wird | 2017-04-02
name | Name des virtuellen Computers | 2017-04-02
offer | Angebotsinformationen für das VM-Image, diese sind nur für Images vorhanden, die über den Azure-Imagekatalog bereitgestellt werden | 2017-04-02
osType | Linux oder Windows | 2017-04-02
placementGroupId | [Platzierungsgruppe](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) der VM-Skalierungsgruppe | 2017-08-01
Tarif | Der [Tarif](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) mit Name, Produkt und Herausgeber für einen virtuellen Computer, wenn es sich um ein Azure Marketplace-Image handelt | 2018-04-02
platformUpdateDomain |  [Updatedomäne](manage-availability.md), in der die VM ausgeführt wird | 2017-04-02
platformFaultDomain | [Fehlerdomäne](manage-availability.md), in der die VM ausgeführt wird | 2017-04-02
Anbieter | Anbieter des virtuellen Computers | 01\.10.2018
publicKeys | [Sammlung von öffentlichen Schlüsseln](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey), dem virtuellen Computer und den entsprechenden Pfaden zugewiesen | 2018-04-02
publisher | Herausgeber des VM-Images | 2017-04-02
resourceGroupName | [Ressourcengruppe](../../azure-resource-manager/resource-group-overview.md) für den virtuellen Computer | 2017-08-01
sku | Spezifische SKU für das VM-Image | 2017-04-02
subscriptionId | Azure-Abonnement für den virtuellen Computer | 2017-08-01
tags | [Tags](../../azure-resource-manager/resource-group-using-tags.md) für den virtuellen Computer  | 2017-08-01
version | Version des VM-Image | 2017-04-02
vmId | [Eindeutiger Bezeichner](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) für die VM | 2017-04-02
vmScaleSetName | [Name Ihrer VM-Skalierungsgruppe](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
vmSize | [Größe des virtuellen Computers](sizes.md) | 2017-04-02
Zone | [Verfügbarkeitszone](../../availability-zones/az-overview.md) Ihres virtuellen Computers | 2017-12-01

##### <a name="the-following-network-categories-are-available-through-the-instance-api"></a>Die folgenden Netzwerkkategorien werden über die Instanz-API zur Verfügung gestellt:

> [!NOTE]
> Über den Metadatenendpunkt sind die folgenden Kategorien über „Instanz/Netzwerk/Schnittstelle“ zugänglich.

Daten | BESCHREIBUNG | Eingeführt in Version
-----|-------------|-----------------------
ipv4/privateIpAddress | Lokale IPv4-Adresse der VM | 2017-04-02
ipv4/publicIpAddress | Öffentliche IPv4-Adresse der VM | 2017-04-02
subnet/address | Subnetzadresse der VM | 2017-04-02
subnet/prefix | Subnetzpräfix, Beispiel 24 | 2017-04-02
ipv6/ipAddress | Lokale IPv6-Adresse der VM | 2017-04-02
macAddress | VM-Mac-Adresse | 2017-04-02

## <a name="attested-data"></a>Bestätigte Daten

Instance Metadata reagiert am HTTP-Endpunkt unter 169.254.169.254. Das von Instance Metadata Service bereitgestellte Szenario beinhaltet die Garantie, dass die Antwortdaten von Azure stammen. Wir signieren einen Teil dieser Informationen, sodass sich Marketplace-Images darauf verlassen können, dass ihr Image in Azure ausgeführt wird.

### <a name="example-attested-data"></a>Beispiel für bestätigte Daten

> [!NOTE]
> Alle API-Antworten stellen JSON-Zeichenfolgen dar. Die folgenden Beispielantworten werden zur besseren Lesbarkeit automatische strukturiert und eingerückt.

 **Anforderung**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

„api-version“ ist ein Pflichtfeld, und die für bestätigte Daten unterstützte Version ist 2018-10-01.
Die Nonce ist eine bereitgestellte optionale 10-stellige Zeichenfolge. Sie kann zum Nachverfolgen der Anforderung verwendet werden. Wird keine Nonce angegeben, wird in der codierten Antwortzeichenfolge der aktuelle UTC-Zeitstempel zurückgegeben.

 **Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Das Signaturblob ist eine signierte [pkcs7](https://aka.ms/pkcs7)-Version des Dokuments. Es enthält das zum Signieren verwendete Zertifikat zusammen mit den VM-Details wie vmId, nonce, timeStamp für die Erstellung und den Ablauf des Dokuments sowie die Planinformationen zum Image. Die Planinformationen werden nur für Azure Marketplace-Images ausgefüllt. Das Zertifikat kann aus der Antwort extrahiert und verwendet werden, um sicherzustellen, dass die Antwort gültig ist und von Azure stammt.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Abrufen bestätigter Metadaten auf einem virtuellen Windows-Computer

 **Anforderung**

Die Instanzmetadaten können unter Windows über das PowerShell-Hilfsprogramm `curl` abgerufen werden:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Alternativ ist dies auch über das Cmdlet `Invoke-RestMethod` möglich:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

„api-version“ ist ein Pflichtfeld, und die für bestätigte Daten unterstützte Version ist 2018-10-01.
Die Nonce ist eine bereitgestellte optionale 10-stellige Zeichenfolge. Sie kann zum Nachverfolgen der Anforderung verwendet werden. Wird keine Nonce angegeben, wird in der codierten Antwortzeichenfolge der aktuelle UTC-Zeitstempel zurückgegeben.

 **Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Das Signaturblob ist eine signierte [pkcs7](https://aka.ms/pkcs7)-Version des Dokuments. Es enthält das zum Signieren verwendete Zertifikat zusammen mit den VM-Details wie vmId, nonce, timeStamp für die Erstellung und den Ablauf des Dokuments sowie die Planinformationen zum Image. Die Planinformationen werden nur für Azure Marketplace-Images ausgefüllt. Das Zertifikat kann aus der Antwort extrahiert und verwendet werden, um sicherzustellen, dass die Antwort gültig ist und von Azure stammt.


## <a name="example-scenarios-for-usage"></a>Beispielszenarien für die Verwendung  

### <a name="tracking-vm-running-on-azure"></a>Nachverfolgen einer in Azure ausgeführten VM

Als Dienstanbieter müssen Sie möglicherweise die Anzahl von VMs nachverfolgen, auf denen Ihre Software ausgeführt wird, oder benötigen Agents, die die Eindeutigkeit der VM überwachen müssen. Um eine eindeutige ID für eine VM abrufen zu können, verwenden Sie das Feld `vmId` des Instanzmetadatendiensts.

**Anforderung**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Antwort**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Platzierung von Containern und Datenpartitionen basierend auf der Fehler-/Updatedomäne 

In bestimmten Szenarien ist die Platzierung unterschiedlicher Datenreplikate von primärer Bedeutung. Beispielsweise müssen Sie bei der [HDFS-Replikatplatzierung](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) oder bei der Containerplatzierung über einen [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) die `platformFaultDomain` und die `platformUpdateDomain` kennen, auf denen die VM ausgeführt wird.
Sie können auch [Verfügbarkeitszonen](../../availability-zones/az-overview.md) für die Instanzen nutzen, um diese Entscheidungen zu treffen.
Sie können diese Daten direkt über den Instanzmetadatendienst abfragen.

**Anforderung**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Antwort**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Abrufen von weiteren Informationen zur VM während einer Supportanfrage

Als Dienstanbieter erhalten Sie möglicherweise eine Supportanfrage, für die Sie weitere Informationen zur VM erfahren möchten. Wenn Sie den Kunden auffordern, die Computemetadaten weiterzuleiten, können Sie dem Supportmitarbeiter grundlegende Informationen über die Art von VM in Azure bereitstellen. 

**Anforderung**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Abrufen der Azure-Umgebung, in der die VM ausgeführt wird

Azure verfügt über verschiedene Sovereign Clouds wie [Azure Government](https://azure.microsoft.com/overview/clouds/government/). In manchen Fällen muss die Azure-Umgebung gewisse Laufzeitentscheidungen treffen. Im folgenden Beispiel wird gezeigt, wie Sie dieses Verhalten erzielen können.

**Anforderung**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Antwort**
```bash
AZUREPUBLICCLOUD
```

### <a name="getting-the-tags-for-the-vm"></a>Abrufen der Tags für die VM

Möglicherweise wurden Tags auf Ihre Azure-VM angewendet, um sie logisch in einer Taxonomie zu strukturieren. Die einer VM zugewiesenen Tags können mithilfe der Abfrage unten abgerufen werden.

**Anforderung**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Antwort**

```text
Department:IT;Environment:Test;Role:WebRole
```

> [!NOTE]
> Die Tags sind durch Semikolons getrennt. Wenn ein Parser zur programmgesteuerten Extraktion der Tags geschrieben wird, sollten die Tagnamen und -werte keine Semikolons enthalten, damit der Parser ordnungsgemäß funktionieren kann.

### <a name="validating-that-the-vm-is-running-in-azure"></a>Überprüfen der Ausführung des virtuellen Computers in Azure

Marketplace-Anbieter möchten sicherstellen, dass ihre Software nur für die Ausführung in Azure lizenziert ist. Wenn die virtuelle Festplatte in eine lokale Umgebung kopiert wird, müssen sie über die Möglichkeit verfügen, dies zu erkennen. Durch Aufrufen von Instance Metadata Service können Marketplace-Anbieter signierte Daten abrufen, die garantieren, dass Antworten ausschließlich von Azure kommen.

> [!NOTE]
> Die Installation von jq ist erforderlich.

**Anforderung**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2018-10-01 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Antwort**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34"
}
```

Daten | BESCHREIBUNG
-----|------------
nonce | Vom Benutzer bereitgestellte optionale Zeichenfolge mit der Anforderung. Wenn in der Anforderung keine Nonce angegeben wurde, wird der aktuelle UTC-Zeitstempel zurückgegeben.
Tarif | Der [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) für einen virtuellen Computer im entsprechenden Azure Marketplace-Image, enthält Name, Produkt und Herausgeber
timestamp/createdOn | Der Zeitstempel, zu dem das erste signierte Dokument erstellt wurde
timestamp/expiresOn | Der Zeitstempel, zu dem das signierte Dokument abläuft
vmId |  [Eindeutiger Bezeichner](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) für die VM

#### <a name="verifying-the-signature"></a>Überprüfen der Signatur

Sobald Sie über die oben beschriebene Signatur verfügen, können Sie verifizieren, dass die Signatur von Microsoft stammt. Außerdem können Sie das Zwischenzertifikat und die Zertifikatkette überprüfen.

> [!NOTE]
> Die Zertifikate für öffentliche Clouds und Sovereign Clouds unterscheiden sich voneinander.

 Regions | Zertifikat
---------|-----------------
[Allgemein verfügbar in globalen Azure-Regionen](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China](https://www.azure.cn/)                                                           | metadata.azure.cn
[Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

Falls das Zwischenzertifikat bei der Überprüfung aufgrund von Netzwerkeinschränkungen nicht heruntergeladen werden kann, können Sie das Zwischenzertifikat anheften. Azure übergibt die Zertifikate jedoch beim Rollover gemäß der Standard-PKI-Methode. Die angehefteten Zertifikate müssen bei einem Rollover aktualisiert werden. Wenn eine Änderung geplant ist, bei der das Zwischenzertifikat aktualisiert werden muss, wird der Azure-Blog auf den neuesten Stand gebracht, und Azure-Kunden werden benachrichtigt. Die Zwischenzertifikate sind [hier](https://www.microsoft.com/pki/mscorp/cps/default.htm) zu finden. Die Zwischenzertifikate können sich für die einzelnen Regionen unterscheiden.

### <a name="failover-clustering-in-windows-server"></a>Failoverclustering in Windows Server

Für bestimmte Szenarien ist es beim Abfragen von Instance Metadata Service mit Failoverclustering notwendig, der Routingtabelle eine Route hinzuzufügen.

1. Öffnen Sie eine Eingabeaufforderung mit Administratorberechtigungen.

2. Führen Sie den folgenden Befehl aus, und notieren Sie die Adresse der Schnittstelle für das Netzwerkziel (`0.0.0.0`) in der IPv4-Routingtabelle.

```bat
route print
```

> [!NOTE] 
> Die folgende Beispielausgabe von einer Windows Server-VM mit aktiviertem Failovercluster enthält aus Gründen der Vereinfachung nur die IPv4-Routingtabelle.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Führen Sie den folgenden Befehl aus, und verwenden Sie dabei die Adresse der Schnittstelle für das Netzwerkziel (`0.0.0.0`), in diesem Beispiel `10.0.1.10`.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="custom-data"></a>Benutzerdefinierte Daten
Instance Metadata Service bietet für den virtuellen Computer die Möglichkeit, auf eigene benutzerdefinierte Daten zuzugreifen. Die binären Daten müssen weniger als 64 KB umfassen und werden dem virtuellen Computer in base64-codiertem Format bereitgestellt.

Benutzerdefinierte Azure-Daten können über REST-APIs, PowerShell-Cmdlets, die Azure-Befehlszeilenschnittstelle oder eine ARM-Vorlage in die VM eingefügt werden.

Ein Beispiel für die Azure-Befehlszeilenschnittstelle finden Sie unter [Benutzerdefinierte Daten und Cloudinitialisierung in Microsoft Azure](https://azure.microsoft.com/blog/custom-data-and-cloud-init-on-windows-azure/).

Ein Beispiel für eine ARM-Vorlage finden Sie unter [Bereitstellen eines virtuellen Computers mit CustomData](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

Benutzerdefinierte Daten sind für alle auf dem virtuellen Computer ausgeführten Prozesse verfügbar. Es wird Kunden empfohlen, in benutzerdefinierte Daten keine geheimen Informationen einzuschließen.

Zurzeit wird gewährleistet, dass benutzerdefinierte Daten während des Startvorgangs einer VM verfügbar sind. Wenn Aktualisierungen an der VM vorgenommen werden (z.B. das Hinzufügen von Datenträgern oder das Ändern der Größe der VM), stellt der Instance Metadata Service keine benutzerdefinierten Daten bereit. Die dauerhafte Bereitstellung benutzerdefinierter Daten über Instance Metadata Service ist derzeit in Vorbereitung.

#### <a name="retrieving-custom-data-in-virtual-machine"></a>Abrufen von benutzerdefinierten Daten auf virtuellen Computern
Instance Metadata Service stellt dem virtuellen Computer benutzerdefinierte Daten in base64-codiertem Format bereit. Im folgenden Beispiel wird die base64-codierte Zeichenfolge decodiert.

> [!NOTE]
> Die benutzerdefinierten Daten in diesem Beispiel werden als ASCII-Zeichenfolge interpretiert, die „My custom data.“ lautet.

**Anforderung**

```bash
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute/customData?api-version=2019-02-01&&format=text" | base64 --decode
```

**Antwort**

```text
My custom data.
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Beispiele zum Aufrufen des Metadatendiensts über verschiedene Sprachen auf der VM 

Sprache | Beispiel
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Häufig gestellte Fragen

1. Ich erhalte die Fehlermeldung `400 Bad Request, Required metadata header not specified`. Was bedeutet dies?
   * Für den Instanzmetadatendienst muss der Header `Metadata: true` in der Anforderung übergeben werden. Durch Übergeben dieses Headers im REST-Aufruf wird der Zugriff auf den Instanzmetadatendienst ermöglicht.
2. Warum erhalte ich keine Compute-Informationen für meine VM?
   * Der Instanzmetadatendienst unterstützt derzeit nur Instanzen, die mit dem Azure Resource Manager erstellt wurden. In Zukunft kann zusätzliche Unterstützung für Clouddienst-VMs hinzugefügt werden.
3. Ich habe meinen virtuellen Computer vor einiger Zeit über den Azure Resource Manager erstellt. Warum sehe ich keine Computemetadateninformationen?
   * Fügen Sie für VMs, die nach September 2016 erstellt wurden, ein [Tag](../../azure-resource-manager/resource-group-using-tags.md) hinzu, damit Computemetadaten angezeigt werden. Fügen Sie bei älteren VMs (die vor September 2016 erstellt wurden) Erweiterungen oder Datenträger zur VM hinzu, bzw. entfernen Sie diese, um Metadaten zu aktualisieren.
4. Nicht alle Daten wurden für die neue Version eingetragen.
   * Fügen Sie für VMs, die nach September 2016 erstellt wurden, ein [Tag](../../azure-resource-manager/resource-group-using-tags.md) hinzu, damit Computemetadaten angezeigt werden. Fügen Sie bei älteren VMs (die vor September 2016 erstellt wurden) Erweiterungen oder Datenträger zur VM hinzu, bzw. entfernen Sie diese, um Metadaten zu aktualisieren.
5. Warum erhalte ich die Fehlermeldung `500 Internal Server Error`?
   * Wiederholen Sie Ihre Anforderung basierend auf dem Exponential-Backoff-System. Wenden Sie sich an den Azure-Support, wenn das Problem weiterhin besteht.
6. Wie kann ich weitere Fragen/Kommentare weiterleiten?
   * Senden Sie Ihre Kommentare an https://feedback.azure.com.
7. Gilt dies auch für VM-Skalierungsgruppeninstanzen?
   * Ja, der Metadatendienst ist für Skalierungsgruppeninstanzen verfügbar.
8. Wie beziehe ich Support für den Dienst?
   * Um Support für den Dienst zu beziehen, erstellen Sie im Azure-Portal ein Supportproblem für den virtuellen Computer, auf dem Sie nach wiederholten Versuchen keine Metadatenantwort erhalten.
9. Ich erhalte ein Anforderungstimeout für den Aufruf des Diensts.
   * Metadatenaufrufe müssen von der primären IP-Adresse erfolgen, die der Netzwerkkarte des virtuellen Computers zugewiesen ist. Wenn Sie außerdem Ihre Routen geändert haben, muss eine Route für die Adresse 169.254.0.0/16 von Ihrer Netzwerkkarte aus vorhanden sein.
10. Ich habe meine Tags in der VM-Skalierungsgruppe aktualisiert, aber sie werden im Gegensatz zu virtuellen Computern nicht in den Instanzen angezeigt.
    * Derzeit werden Skalierungsgruppen Tags zu dem virtuellen Computer nur nach einem Neustart, einer Neuerstellung des Images oder der Änderung eines Datenträgers für die Instanz angezeigt.

    ![Instanzmetadatenunterstützung](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [geplante Ereignisse](scheduled-events.md).
