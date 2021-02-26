---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: fcdccf6701afe73ab0f11a7a907072b01a9d5aa4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373305"
---
# <a name="azure-instance-metadata-service"></a>Azure-Instanzmetadatendienst

Der Azure Instance Metadata Service (IMDS) stellt Informationen zu Instanzen virtueller Computer bereit, die derzeit ausgeführt werden. Sie können ihn zur Verwaltung und Konfiguration Ihrer virtuellen Computer verwenden.
Hierzu gehören die SKU, der Speicher, Netzwerkkonfigurationen und bevorstehende Wartungsereignisse. Eine umfassende Liste der verfügbaren Daten finden Sie in der [Übersicht über die Endpunktkategorien](#endpoint-categories).

Der IMDS ist für das Ausführen von Instanzen virtueller Computer (VMs) und VM-Skalierungsgruppen verfügbar. Alle Endpunkte unterstützen VMs, die mit [Azure Resource Manager](/rest/api/resources/) erstellt und verwaltet werden. Nur die Kategorie „attested“ und der Teil „network“ der Kategorie „instance“ unterstützen VMs, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden. Beim Endpunkt der Kategorie „attested“ erfolgt dies nur in begrenztem Umfang.

IMDS ist eine REST-API, die unter einer bekannten, nicht routingfähigen IP-Adresse (`169.254.169.254`) verfügbar ist. Sie können nur vom virtuellen Computer aus darauf zugreifen. Die Kommunikation zwischen dem virtuellen Computer und IMDS verlässt niemals den Host.
Die HTTP-Clients sollten bei Abfragen von IMDS Webproxys innerhalb des virtuellen Computers umgehen und `169.254.169.254` gleich behandeln wie [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="usage"></a>Verwendung

### <a name="access-azure-instance-metadata-service"></a>Zugreifen auf den Azure Instance Metadata Service

Um auf den IMDS zuzugreifen, erstellen Sie eine VM über [Azure Resource Manager](/rest/api/resources/) oder das [Azure-Portal](https://portal.azure.com), und verwenden Sie die folgenden Beispiele.
Weitere Beispiele finden Sie unter [Azure Instance Metadata Samples](https://github.com/microsoft/azureimds).

Hier finden Sie ein Codebeispiel zum Abrufen aller Metadaten für eine Instanz. Für den Zugriff auf eine bestimmte Datenquelle finden Sie unter [Endpunktkategorien](#endpoint-categories) eine Übersicht über alle verfügbaren Features.

**Anforderung**

> [!IMPORTANT]
> In diesem Beispiel werden Proxys umgangen. Beim Abfragen von IMDS **müssen** Sie Proxys umgehen. Weitere Informationen finden Sie unter [Proxys](#proxies).

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

---

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Sicherheit und Authentifizierung

Auf den Instance Metadata Service kann nur innerhalb einer ausgeführten VM-Instanz an einer nicht routingfähigen IP-Adresse zugegriffen werden. VMs sind auf die Interaktion mit Metadaten/Funktionen beschränkt, die für sich selbst gelten. Die API ist auf HTTP beschränkt und verlässt niemals den Host.

Um sicherzustellen, dass Anforderungen direkt für IMDS vorgesehen sind und keine unbeabsichtigten oder unerwünschten Umleitungen von Anforderungen erfolgen, müssen Anforderungen folgende Bedingungen erfüllen:
- Sie **müssen** den Header `Metadata: true` enthalten.
- Sie dürfen **nicht** den Header `X-Forwarded-For` enthalten.

Jede Anforderung, die nicht **beide** dieser Anforderungen erfüllt, wird vom Dienst abgelehnt.

> [!IMPORTANT]
> IMDS ist **kein** Kanal für vertrauliche Daten. Die API ist nicht authentifiziert und ist für alle Prozesse auf der VM geöffnet. Informationen, die über diesen Dienst verfügbar gemacht werden, sollten als freigegebene Informationen für alle Anwendungen betrachtet werden, die in der VM ausgeführt werden.

## <a name="proxies"></a>Proxys

IMDS ist **nicht** für die Verwendung hinter einem Proxy vorgesehen. Dies wird nicht unterstützt. Die meisten HTTP-Clients bieten Ihnen die Möglichkeit, Proxys für Anforderungen zu deaktivieren. Diese Funktionalität muss bei der Kommunikation mit IMDS verwendet werden. Weitere Informationen finden Sie in der Dokumentation zu dem Client.

> [!IMPORTANT]
> Auch wenn Sie die Proxykonfiguration in Ihrer Umgebung nicht kennen, **müssen Sie dennoch alle Clientproxy-Standardeinstellungen außer Kraft setzen**. Proxykonfigurationen können automatisch ermittelt werden. Falls solche Konfigurationen nicht umgangen werden, können erhebliche Probleme auftreten, wenn die Konfiguration des Computers in Zukunft geändert wird.

## <a name="rate-limiting"></a>Ratenbegrenzung

Im Allgemeinen sind Anforderungen an IMDS auf 5 Anforderungen pro Sekunde beschränkt. Anforderungen, die diesen Schwellenwert überschreiten, werden mit der Antwort 429 abgelehnt. Anforderungen an die Kategorie [Verwaltete Identität](#managed-identity) sind auf 20 Anforderungen pro Sekunde und 5 gleichzeitige Anforderungen beschränkt.

## <a name="http-verbs"></a>HTTP-Verben

Die folgenden HTTP-Verben werden derzeit unterstützt:

| Verb | BESCHREIBUNG |
|------|-------------|
| `GET` | Abrufen der angeforderten Ressource

## <a name="parameters"></a>Parameter

Endpunkte unterstützen möglicherweise erforderliche und/oder optionale Parameter. Weitere Informationen finden Sie unter [Schema](#schema) und in der Dokumentation für den jeweiligen Endpunkt.

### <a name="query-parameters"></a>Abfrageparameter

IMDS-Endpunkte unterstützen HTTP-Abfragezeichenfolgenparameter. Zum Beispiel: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Gibt die Parameter an:

| Name | Wert |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Anforderungen mit doppelten Abfrageparameternamen werden abgelehnt.

### <a name="route-parameters"></a>Routenparameter

Bei einigen Endpunkten, die größere JSON-Blobs zurückgeben, wird das Anfügen von Routenparametern an den Anforderungsendpunkt unterstützt, um nach einer Teilmenge der Antwort zu filtern: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Die Parameter entsprechen den Indizes/Schlüsseln, die zum Durchlaufen des JSON-Objekts verwendet werden, wenn Sie mit einer analysierten Darstellung interagieren.

`/metatadata/instance` gibt z. B. das JSON-Objekt zurück:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Wenn Sie die Antwort nach der compute-Eigenschaft filtern möchten, senden Sie die Anforderung: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Wenn Sie nach einer geschachtelten Eigenschaft oder einem bestimmten Arrayelement filtern möchten, fügen Sie Schlüssel an: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
filtert nach dem ersten Element in der `Network.interface`-Eigenschaft und gibt Folgendes zurück:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> Beim Filtern nach einem Blattknoten kann `format=json` nicht verwendet werden. Für diese Abfragen muss `format=text` explizit angegeben werden, da das Standardformat JSON ist.

## <a name="schema"></a>Schema

### <a name="data-format"></a>Datenformat

Standardmäßig gibt der IMDS Daten im JSON-Format (`Content-Type: application/json`) zurück. Endpunkte, die das Filtern von Antworten unterstützen (siehe [Routenparameter](#route-parameters)), unterstützen jedoch auch das Format `text`.

Um auf ein nicht standardmäßiges Antwortformat zuzugreifen, geben Sie das angeforderte Format als QueryString-Parameter in der Anforderung an. Beispiel:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

In JSON-Antworten sind alle Primitiven vom Typ `string`, und fehlende oder nicht anwendbare Werte sind immer enthalten, werden jedoch auf eine leere Zeichenfolge festgelegt.

### <a name="versioning"></a>Versionsverwaltung

Der Instance Metadata Service (IMDS) verwendet Versionsangaben, und die Angabe der API-Version in der HTTP-Anforderung ist zwingend erforderlich. Die einzige Ausnahme von dieser Anforderung ist der Endpunkt [Versionen](#versions), der zum dynamischen Abrufen der verfügbaren API-Versionen verwendet werden kann.

Wenn neuere Versionen hinzugefügt werden, kann auf ältere Versionen aus Kompatibilitätsgründen weiterhin zugegriffen werden, falls Ihre Skripts von bestimmten Datenformaten abhängig sind.

Wenn Sie keine Version angeben, erhalten Sie eine Fehlermeldung mit einer Liste der neuesten unterstützten Versionen:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Unterstützte API-Versionen
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 11.03.2019
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> Version 2020-10-01 wird zurzeit eingeführt und ist möglicherweise noch nicht in jeder Region verfügbar.

### <a name="swagger"></a>Swagger

Eine vollständige Swagger-Definition für IMDS finden Sie unter: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Der Dienst ist in allen Azure-Clouds **allgemein verfügbar**.

## <a name="root-endpoint"></a>Stammendpunkt

Der Stammendpunkt lautet `http://169.254.169.254/metadata`.

## <a name="endpoint-categories"></a>Endpunktkategorien

Die IMDS-API enthält mehrere Endpunktkategorien, die verschiedene Datenquellen darstellen, die jeweils einen oder mehrere Endpunkte enthalten. Ausführlichere Informationen finden Sie in der jeweiligen Kategorie.

| Kategoriestamm | BESCHREIBUNG | Eingeführt in Version |
|---------------|-------------|--------------------|
| `/metadata/attested` | Siehe [Bestätigte Daten](#attested-data) | 2018-10-01
| `/metadata/identity` | Siehe [Verwaltete Identität](#managed-identity) | 2018-02-01
| `/metadata/instance` | Siehe [Instanzmetadaten](#instance-metadata) | 2017-04-02
| `/metadata/scheduledevents` | Siehe [Geplante Ereignisse](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Siehe [Versionen](#versions) | –

## <a name="versions"></a>Versionen

> [!NOTE]
> Dieses Feature wurde mit Version 2020-10-01 veröffentlicht, die zurzeit eingeführt wird und möglicherweise noch nicht in jeder Region verfügbar ist.

### <a name="list-api-versions"></a>Auflisten von API-Versionen

Gibt den Satz unterstützter API-Versionen zurück.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parameter

Keine (dieser Endpunkt ist ohne Versionsangabe)

#### <a name="response"></a>Antwort

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Instanzmetadaten

### <a name="get-vm-metadata"></a>Abrufen von VM-Metadaten

Macht die wichtigen Metadaten für die VM-Instanz verfügbar, einschließlich Compute, Netzwerk und Speicher. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parameter

| Name | Erforderlich/Optional | Beschreibung |
|------|-------------------|-------------|
| `api-version` | Erforderlich | Die zum Durchführen der Anforderung verwendete Version
| `format` | Optional* | Das Format (`json` oder `text`) der Antwort. *Hinweis: Ist möglicherweise erforderlich, wenn Anforderungsparameter verwendet werden.

Dieser Endpunkt unterstützt das Filtern von Antworten über [Routenparameter](#route-parameters).

#### <a name="response"></a>Antwort

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Aufschlüsselung des Schemas:

**Compute**

| Daten | BESCHREIBUNG | Eingeführt in Version |
|------|-------------|--------------------|
| `azEnvironment` | Azure-Umgebung, in der die VM ausgeführt wird | 2018-10-01
| `customData` | Diese Funktion ist derzeit deaktiviert. Diese Dokumentation wird aktualisiert, wenn die Funktion verfügbar wird. | 2019-02-01
| `isHostCompatibilityLayerVm` | Gibt an, ob die VM auf der Hostkompatibilitätsebene ausgeführt wird | 2020-06-01
| `licenseType` | Typ der Lizenz für [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit). Dies ist nur für AHB-aktivierte VMs verfügbar. | 2020-09-01
| `location` | Azure-Region, in der die VM ausgeführt wird | 2017-04-02
| `name` | Name des virtuellen Computers | 2017-04-02
| `offer` | Angebotsinformationen für das VM-Image, diese sind nur für Images vorhanden, die über den Azure-Imagekatalog bereitgestellt werden | 2017-04-02
| `osProfile.adminUsername` | Gibt den Namen des Administratorkontos an. | 2020-07-15
| `osProfile.computerName` | Gibt den Namen des Computers an. | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Gibt an, ob die Kennwortauthentifizierung deaktiviert ist. Dies ist nur für Linux-VMs verfügbar. | 2020-10-01
| `osType` | Linux oder Windows | 2017-04-02
| `placementGroupId` | [Platzierungsgruppe](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) der VM-Skalierungsgruppe | 2017-08-01
| `plan` | Der [Plan](/rest/api/compute/virtualmachines/createorupdate#plan) mit Name, Produkt und Herausgeber für einen virtuellen Computer, wenn es sich um ein Azure Marketplace-Image handelt | 2018-04-02
| `platformUpdateDomain` |  [Updatedomäne](../articles/virtual-machines/manage-availability.md), in der die VM ausgeführt wird | 2017-04-02
| `platformFaultDomain` | [Fehlerdomäne](../articles/virtual-machines/manage-availability.md), in der die VM ausgeführt wird | 2017-04-02
| `provider` | Anbieter des virtuellen Computers | 2018-10-01
| `publicKeys` | [Sammlung von öffentlichen Schlüsseln](/rest/api/compute/virtualmachines/createorupdate#sshpublickey), dem virtuellen Computer und den entsprechenden Pfaden zugewiesen | 2018-04-02
| `publisher` | Herausgeber des VM-Images | 2017-04-02
| `resourceGroupName` | [Ressourcengruppe](../articles/azure-resource-manager/management/overview.md) für den virtuellen Computer | 2017-08-01
| `resourceId` | Die [vollqualifizierte](/rest/api/resources/resources/getbyid) ID der Ressource | 11.03.2019
| `sku` | Spezifische SKU für das VM-Image | 2017-04-02
| `securityProfile.secureBootEnabled` | Gibt an, ob UEFI Secure Boot auf der VM aktiviert ist | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Gibt an, ob das virtuelle Trusted Platform Module (TPM) auf der VM aktiviert ist | 2020-06-01
| `storageProfile` | Siehe „Speicherprofil“ weiter unten | 2019-06-01
| `subscriptionId` | Azure-Abonnement für den virtuellen Computer | 2017-08-01
| `tags` | [Tags](../articles/azure-resource-manager/management/tag-resources.md) für den virtuellen Computer  | 2017-08-01
| `tagsList` | Tags, die zur einfacheren programmgesteuerten Analyse als JSON-Arrays formatiert sind  | 2019-06-04
| `version` | Version des VM-Image | 2017-04-02
| `vmId` | [Eindeutiger Bezeichner](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) für die VM | 2017-04-02
| `vmScaleSetName` | [Name Ihrer VM-Skalierungsgruppe](../articles/virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [Größe des virtuellen Computers](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Verfügbarkeitszone](../articles/availability-zones/az-overview.md) Ihres virtuellen Computers | 2017-12-01

**Speicherprofil**

Das Speicherprofil eines virtuellen Computers ist in drei Kategorien unterteilt: Imagereferenz, Betriebssystemdatenträger und Datenträger für Daten.

Das Imagereferenzobjekt enthält die folgenden Informationen zum Betriebssystemimage:

| Daten | BESCHREIBUNG |
|------|-------------|
| `id` | Ressourcen-ID
| `offer` | Angebot des Plattform- oder Marketplace-Images
| `publisher` | Imageherausgeber
| `sku` | Image-SKU
| `version` | Version des Plattform- oder Marketplace-Images

Das Betriebssystemdatenträgerobjekt enthält die folgenden Informationen zum Betriebssystemdatenträger, der vom virtuellen Computer verwendet wird:

| Daten | BESCHREIBUNG |
|------|-------------|
| `caching` | Cachinganforderungen
| `createOption` | Informationen zur Erstellung des virtuellen Computers
| `diffDiskSettings` | Einstellungen für kurzlebige Datenträger
| `diskSizeGB` | Größe des Datenträgers in GB
| `image`   | Virtuelle Festplatte des Quellenbenutzerimages
| `lun`     | Logische Gerätenummer des Datenträgers
| `managedDisk` | Parameter für verwaltete Datenträger
| `name`    | Name des Datenträgers
| `vhd`     | Virtuelle Festplatte
| `writeAcceleratorEnabled` | Gibt an, ob writeAccelerator für den Datenträger aktiviert ist.

Das Datenträgerarray für Daten enthält eine Liste der Datenträger für Daten, die an den virtuellen Computer angefügt sind. Jedes Datenträgerobjekt enthält die folgenden Informationen:

Daten | BESCHREIBUNG |
-----|-------------|
| `caching` | Cachinganforderungen
| `createOption` | Informationen zur Erstellung des virtuellen Computers
| `diffDiskSettings` | Einstellungen für kurzlebige Datenträger
| `diskSizeGB` | Größe des Datenträgers in GB
| `encryptionSettings` | Verschlüsselungseinstellungen für den Datenträger
| `image` | Virtuelle Festplatte des Quellenbenutzerimages
| `managedDisk` | Parameter für verwaltete Datenträger
| `name` | Name des Datenträgers
| `osType` | Typ des Betriebssystems, das auf dem Datenträger enthalten ist
| `vhd` | Virtuelle Festplatte
| `writeAcceleratorEnabled` | Gibt an, ob writeAccelerator für den Datenträger aktiviert ist.

**Network**

| Daten | BESCHREIBUNG | Eingeführt in Version |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Lokale IPv4-Adresse der VM | 2017-04-02
| `ipv4.publicIpAddress` | Öffentliche IPv4-Adresse der VM | 2017-04-02
| `subnet.address` | Subnetzadresse der VM | 2017-04-02
| `subnet.prefix` | Subnetzpräfix, Beispiel 24 | 2017-04-02
| `ipv6.ipAddress` | Lokale IPv6-Adresse der VM | 2017-04-02
| `macAddress` | VM-Mac-Adresse | 2017-04-02

**VM-Tags**

VM-Tags sind in der Instanz-API unter dem Instanz/Compute/Tags-Endpunkt enthalten.
Möglicherweise wurden Tags auf Ihre Azure-VM angewendet, um sie logisch in einer Taxonomie zu strukturieren. Die einer VM zugewiesenen Tags können mithilfe der Abfrage unten abgerufen werden.

Das `tags` Feld ist eine Zeichenfolge, in der die Tags durch Semikolons getrennt sind. Diese Ausgabe kann ein Problem darstellen, wenn in den Tags Semikolons verwendet werden. Wenn ein Parser geschrieben wurde, um die Tags programmgesteuert zu extrahieren, sollten Sie auf das Feld `tagsList` zurückgreifen. Das Feld `tagsList` ist ein JSON-Array ohne Trennzeichen und folglich einfacher zu analysieren.


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Beispiel 1: Nachverfolgen einer in Azure ausgeführten VM

Als Dienstanbieter müssen Sie möglicherweise die Anzahl von VMs nachverfolgen, auf denen Ihre Software ausgeführt wird, oder benötigen Agents, die die Eindeutigkeit der VM überwachen müssen. Um eine eindeutige ID für eine VM abrufen zu können, verwenden Sie das Feld `vmId` des Instanzmetadatendiensts.

**Anforderung**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"| ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Antwort**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Beispiel 2: Platzierung unterschiedlicher Datenreplikate

In bestimmten Szenarien ist die Platzierung unterschiedlicher Datenreplikate von primärer Bedeutung. Beispielsweise müssen Sie bei der [HDFS-Replikatplatzierung](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) oder bei der Containerplatzierung über einen [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) die `platformFaultDomain` und die `platformUpdateDomain` kennen, auf denen die VM ausgeführt wird.
Sie können auch [Verfügbarkeitszonen](../articles/availability-zones/az-overview.md) für die Instanzen nutzen, um diese Entscheidungen zu treffen.
Sie können diese Daten direkt über den IMDS abfragen.

**Anforderung**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Antwort**

```
0
```

#### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Beispiel 3: Abrufen von weiteren Informationen zur VM während einer Supportanfrage

Als Dienstanbieter erhalten Sie möglicherweise eine Supportanfrage, für die Sie weitere Informationen zur VM erfahren möchten. Wenn Sie den Kunden auffordern, die Computemetadaten weiterzuleiten, können Sie dem Supportmitarbeiter grundlegende Informationen über die Art von VM in Azure bereitstellen.

**Anforderung**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Antwort**

> [!NOTE]
> Die Antwort stellt eine JSON-Zeichenfolge dar. Die folgende Beispielantwort wird zur besseren Lesbarkeit im Schöndruck gedruckt.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Beispiel 4: Abrufen der Azure-Umgebung, in der die VM ausgeführt wird

Azure verfügt über verschiedene Sovereign Clouds wie [Azure Government](https://azure.microsoft.com/overview/clouds/government/). In manchen Fällen muss die Azure-Umgebung gewisse Laufzeitentscheidungen treffen. Im folgenden Beispiel wird gezeigt, wie Sie dieses Verhalten erzielen können.

**Anforderung**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Antwort**

```
AzurePublicCloud
```

Die Cloud und die Werte der Azure-Umgebung sind hier aufgelistet.

| Cloud | Azure-Umgebung |
|-------|-------------------|
| [Alle allgemein verfügbaren globalen Azure-Regionen](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-5-retrieve-network-information"></a>Beispiel 5: Abrufen von Netzwerkinformationen

**Anforderung**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Antwort**

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

#### <a name="sample-6-retrieve-public-ip-address"></a>Beispiel 6: Abrufen der öffentlichen IP-Adresse

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Bestätigte Daten

### <a name="get-attested-data"></a>Abrufen bestätigter Daten

Mithilfe des IMDS kann sichergestellt werden, dass die bereitgestellten Daten aus Azure stammen. Microsoft signiert einen Teil dieser Informationen, sodass Sie bestätigen können, dass ein Image in Azure Marketplace dasjenige ist, das Sie auf Azure ausführen.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parameter

| Name | Erforderlich/Optional | Beschreibung |
|------|-------------------|-------------|
| `api-version` | Erforderlich | Die zum Durchführen der Anforderung verwendete Version
| `nonce` | Optional | Eine 10-stellige Zeichenfolge, die als kryptografische Nonce fungiert. Wenn kein Wert angegeben wird, verwendet IMDS den aktuellen UTC-Zeitstempel.

#### <a name="response"></a>Antwort

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Wegen des Cachemechanismus von IMDS kann ein zuvor zwischengespeicherter Nonce-Wert zurückgegeben werden.

Das Signaturblob ist eine signierte [pkcs7](https://aka.ms/pkcs7)-Version des Dokuments. Es enthält das zum Signieren verwendete Zertifikat zusammen mit bestimmten VM-spezifischen Details.

Für VMs, die mithilfe von Azure Resource Manager erstellt wurden, umfasst das Dokument `vmId`, `sku`, `nonce`, `subscriptionId` und `timeStamp` für die Erstellung und den Ablauf des Dokuments sowie die Planinformationen über das Image. Die Planinformationen werden nur für Azure Marketplace-Images ausgefüllt.

Für VMs, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden, wird sichergestellt, dass nur die `vmId` ausgefüllt wird. Sie können das Zertifikat aus der Antwort extrahieren und mithilfe dieses Zertifikats bestätigen, dass die Antwort gültig ist und von Azure stammt.

Das decodierte Dokument enthält die folgenden Felder:

| Daten | BESCHREIBUNG | Eingeführt in Version |
|------|-------------|--------------------|
| `licenseType` | Typ der Lizenz für [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit). Dies ist nur für AHB-aktivierte VMs verfügbar. | 2020-09-01
| `nonce` | Eine Zeichenfolge, die optional mit der Anforderung angegeben werden kann. Wenn kein `nonce` angegeben wurde, wird der aktuelle Zeitstempel der koordinierten Weltzeit verwendet. | 2018-10-01
| `plan` | Der [Plan für das Azure Marketplace-Image](/rest/api/compute/virtualmachines/createorupdate#plan). Enthält die Plan-ID (name), das Produktimage oder -angebot (product) und die Herausgeber-ID (publisher). | 2018-10-01
| `timestamp.createdOn` | Der UTC-Zeitstempel für den Zeitpunkt, an dem das signierte Dokument erstellt wurde | 2018-20-01
| `timestamp.expiresOn` | Der UTC-Zeitstempel für den Zeitpunkt, an dem das signierte Dokument abläuft | 2018-10-01
| `vmId` | [Eindeutiger Bezeichner](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) für die VM | 2018-10-01
| `subscriptionId` | Azure-Abonnement für den virtuellen Computer | 2019-04-30
| `sku` | Spezifische SKU für das VM-Image | 2019-11-01

> [!NOTE]
> Bei klassischen VMs (Nicht-Azure Resource Manager-VMs) wird nur „vmId“ garantiert ausgefüllt.

Beispieldokument:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Beispiel 1: Überprüfen der Ausführung des virtuellen Computers in Azure

Anbieter in Azure Marketplace möchten sicherstellen, dass ihre Software nur für die Ausführung in Azure lizenziert ist. Wenn jemand die virtuelle Festplatte (VHD) in eine lokale Umgebung kopiert, muss der Anbieter in der Lage sein, dies zu erkennen. Über den IMDS können diese Anbieter signierte Daten erhalten, die sicherstellen, dass Antworten nur von Azure stammen.

> [!NOTE]
> Für dieses Beispiel muss das Hilfsprogramm „jq“ installiert sein.

**Überprüfung**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Vergewissern Sie sich, dass die Signatur von Microsoft Azure stammt, und überprüfen Sie die Zertifikatkette auf Fehler.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Ergebnisse**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Vergewissern Sie sich, dass die Signatur von Microsoft Azure stammt, und überprüfen Sie die Zertifikatkette auf Fehler.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> Wegen des Cachemechanismus von IMDS kann ein zuvor zwischengespeicherter `nonce`-Wert zurückgegeben werden.

Die `nonce` im signierten Dokument kann verglichen werden, wenn Sie in der ursprünglichen Anforderung einen `nonce`-Parameter angegeben haben.

> [!NOTE]
> Die Zertifikate für die öffentliche Cloud und jede Sovereign Cloud unterscheiden sich voneinander.

| Cloud | Zertifikat |
|-------|-------------|
| [Alle allgemein verfügbaren globalen Azure-Regionen](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> Die Zertifikate stimmen mit `metadata.azure.com` für die öffentliche Cloud möglicherweise nicht exakt überein. Aus diesem Grund sollte die Überprüfung der Zertifizierung einen allgemeinen Namen aus einer beliebigen Unterdomäne von `.metadata.azure.com` zulassen.

Falls das Zwischenzertifikat bei der Überprüfung aufgrund von Netzwerkeinschränkungen nicht heruntergeladen werden kann, können Sie das Zwischenzertifikat anheften. Azure führt ein Rollover für die Zertifikate durchführt, was eine standardmäßige PKI-Methode darstellt. Sie müssen die angehefteten Zertifikate aktualisieren, wenn ein Rollover erfolgt. Wenn eine Änderung geplant ist, bei der das Zwischenzertifikat aktualisiert werden muss, wird der Azure-Blog auf den neuesten Stand gebracht, und Azure-Kunden werden benachrichtigt. 

Die Zwischenzertifikate finden Sie im [PKI-Repository](https://www.microsoft.com/pki/mscorp/cps/default.htm). Die Zwischenzertifikate können sich für die einzelnen Regionen unterscheiden.

> [!NOTE]
> Das Zwischenzertifikat für Azure China 21ViaNet stammt von DigiCert Global Root CA anstelle von Baltimore.
Wenn Sie im Rahmen der Änderung der Stammzertifizierungsstelle Zwischenzertifikate für Azure China angeheftet haben, müssen zudem die Zwischenzertifikate aktualisiert werden.


## <a name="managed-identity"></a>Verwaltete Identität

Eine verwaltete Identität, die vom System zugewiesen wird, kann auf der VM aktiviert werden. Sie können der VM auch eine oder mehrere vom Benutzer zugeordnete verwaltete Identitäten zuweisen.
Sie können dann Token für verwaltete Identitäten vom IMDS anfordern. Verwenden Sie diese Token für die Authentifizierung bei anderen Azure-Diensten, z. B. Azure Key Vault.

Ausführliche Schritte zum Aktivieren dieses Features finden Sie unter [Abrufen eines Zugriffstokens](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Geplante Ereignisse
Sie können den Status der geplanten Ereignisse mithilfe des IMDS abrufen. Dann kann der Benutzer eine Reihe von Aktionen festlegen, die bei diesen Ereignissen ausgeführt werden sollen. Weitere Informationen finden Sie unter [Scheduled Events für Linux](../articles/virtual-machines/linux/scheduled-events.md) oder [Scheduled Events für Windows](../articles/virtual-machines/windows/scheduled-events.md).

## <a name="sample-code-in-different-languages"></a>Beispielcode in verschiedenen Sprachen

Die folgende Tabelle listet Beispiele für den Aufruf des IMDS mithilfe verschiedener Sprachen innerhalb der VM auf:

| Sprache | Beispiel |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Fehler und Debuggen

Wenn ein Datenelement nicht gefunden wird oder eine Anforderung ungültig ist, gibt der Instanzmetadatendienst standardmäßige HTTP-Fehler zurück. Beispiel:

| HTTP-Statuscode | `Reason` |
|------------------|--------|
| `200 OK` | Die Anforderung wurde erfolgreich gesendet.
| `400 Bad Request` | Fehlender `Metadata: true`-Header oder fehlender `format=json`-Parameter beim Abfragen eines Blattknotens
| `404 Not Found` | Das angeforderte Element ist nicht vorhanden.
| `405 Method Not Allowed` | Die HTTP-Methode (Verb) wird für den Endpunkt nicht unterstützt.
| `410 Gone` | Wiederholen Sie den Vorgang später (nach max. 70 Sekunden).
| `429 Too Many Requests` | Die API-[Ratenbegrenzung](#rate-limiting) wurde überschritten.
| `500 Service Error` | Wiederholen Sie den Vorgang später.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Ich erhalte die Fehlermeldung `400 Bad Request, Required metadata header not specified`. Was bedeutet das?**

Für den IMDS muss der Header `Metadata: true` in der Anforderung übergeben werden. Die Übergabe dieses Headers im REST-Aufruf ermöglicht den Zugriff auf den IMDS.

**Warum erhalte ich keine Compute-Informationen für meine VM?**

Derzeit unterstützt der IMDS nur Instanzen, die mit Azure Resource Manager erstellt wurden.

**Ich habe meine VM vor einiger Zeit mithilfe von Azure Resource Manager erstellt. Warum sehe ich keine Computemetadateninformationen?**

Wenn Sie Ihre VM nach September 2016 erstellt haben, fügen Sie ein [Tag](../articles/azure-resource-manager/management/tag-resources.md) hinzu, damit Computemetadaten angezeigt werden. Wenn Sie Ihre VM vor September 2016 erstellt haben, fügen Sie der VM-Instanz Erweiterungen oder Datenträger für Daten hinzu oder entfernen Sie diese, um die Metadaten zu aktualisieren.

**Warum werden nicht alle Daten für eine neue Version ausgefüllt?**

Wenn Sie Ihre VM nach September 2016 erstellt haben, fügen Sie ein [Tag](../articles/azure-resource-manager/management/tag-resources.md) hinzu, damit Computemetadaten angezeigt werden. Wenn Sie Ihre VM vor September 2016 erstellt haben, fügen Sie der VM-Instanz Erweiterungen oder Datenträger für Daten hinzu oder entfernen Sie diese, um die Metadaten zu aktualisieren.

**Warum erhalte ich die Fehler `500 Internal Server Error` oder `410 Resource Gone`?**

Wiederholen Sie die Anforderung. Weitere Informationen finden Sie unter [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults). Wenn das Problem weiterhin besteht, erstellen Sie im Azure-Portal ein Supportproblem für den virtuellen Computer.

**Gilt dies auch für VM-Skalierungsgruppeninstanzen?**

Ja, IMDS ist für Instanzen von VM-Skalierungsgruppen verfügbar.

**Ich habe die Tags in VM-Skalierungsgruppen aktualisiert, aber sie werden (im Gegensatz zu Einzelinstanz-VMs) nicht in den Instanzen angezeigt. Mache ich etwas falsch?**

Derzeit werden Tags für VM-Skalierungsgruppen nur nach einem Neustart, einer Neuerstellung des Images oder der Änderung eines Datenträgers für die Instanz angezeigt.

**Warum ist bei meiner Anforderung für meinen Dienstaufruf ein Timeout aufgetreten?**

Metadatenaufrufe müssen über die primäre IP-Adresse erfolgen, die der primären Netzwerkkarte des virtuellen Computers zugewiesen ist. Wenn Sie die Routen geändert haben, muss außerdem eine Route für die Adresse 169.254.169.254/32 in der lokalen Routingtabelle des virtuellen Computers vorhanden sein.

#### <a name="windows"></a>[Windows](#tab/windows/)

1. Erstellen Sie eine Sicherung der lokalen Routingtabelle, und suchen Sie den IMDS-Eintrag. Zum Beispiel:
    ```console
    > route print
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
      169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
    ... (continues) ...
    ```
1. Überprüfen Sie, ob eine Route für `169.254.169.254` vorhanden ist, und notieren Sie sich die entsprechende Netzwerkschnittstelle (z. B. `172.16.69.7`).
1. Erstellen Sie eine Sicherung der Schnittstellenkonfiguration, und suchen Sie die Schnittstelle, die der in der Routingtabelle referenzierten Schnittstelle entspricht. Notieren Sie sich die (physische) MAC-Adresse.
    ```console
    > ipconfig /all
    ... (continues) ...
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
       Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
       Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
       DHCP Enabled. . . . . . . . . . . : Yes
       Autoconfiguration Enabled . . . . : Yes
       Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
       IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
       Subnet Mask . . . . . . . . . . . : 255.255.255.0
    ... (continues) ...
    ```
1. Vergewissern Sie sich, dass die Schnittstelle der primären NIC und der primären IP-Adresse des virtuellen Computers entspricht. Sie finden die primäre NIC und primäre IP-Adresse in der Netzwerkkonfiguration im Azure-Portal oder durch Suchen mithilfe der Azure-Befehlszeilenschnittstelle. Notieren Sie sich die privaten IP-Adressen (und bei Verwendung der Azure CLI auch die MAC-Adresse). Hier ist ein PowerShell-CLI-Beispiel:
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: wintest767 True 00-0D-3A-E5-1C-C0
    ```
1. Wenn sie nicht übereinstimmen, aktualisieren Sie die Routingtabelle so, dass die primäre NIC und die primäre IP-Adresse als Ziel festgelegt sind.

#### <a name="linux"></a>[Linux](#tab/linux/)

 1. Erstellen Sie eine Sicherung der lokalen Routingtabelle mit einem Befehl wie beispielsweise `netstat -r`, und suchen Sie den IMDS-Eintrag, z. B.:
    ```console
    ~$ netstat -r
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         _gateway        0.0.0.0         UG        0 0          0 eth0
    168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
    169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
    172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
    ```
1. Überprüfen Sie, ob eine Route für `169.254.169.254` vorhanden ist, und notieren Sie sich die entsprechende Netzwerkschnittstelle (z. B. `eth0`).
1. Speichern Sie die Schnittstellenkonfiguration für die entsprechende Schnittstelle in der Routingtabelle. (Beachten Sie, dass der genaue Name der Konfigurationsdatei variieren kann.)
    ```console
    ~$ cat /etc/netplan/50-cloud-init.yaml
    network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 100
            dhcp6: false
            match:
                macaddress: 00:0d:3a:e4:c7:2e
            set-name: eth0
    version: 2
    ```
1. Notieren Sie sich bei Verwendung einer dynamischen IP-Adresse die MAC-Adresse (Media Access Control). Wenn Sie eine statische IP-Adresse verwenden, notieren Sie sich die aufgelisteten IPs und/oder die MAC-Adresse.
1. Vergewissern Sie sich, dass die Schnittstelle der primären NIC und der primären IP-Adresse des virtuellen Computers entspricht. Sie finden die primäre NIC und primäre IP-Adresse in der Netzwerkkonfiguration im Azure-Portal oder durch Suchen mithilfe der Azure-Befehlszeilenschnittstelle. Notieren Sie sich die privaten IP-Adressen (und bei Verwendung der Azure CLI auch die MAC-Adresse). Hier ist ein PowerShell-CLI-Beispiel:
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: ipexample606 True 00-0D-3A-E4-C7-2E
    ```
1. Wenn sie nicht übereinstimmen, aktualisieren Sie die Routingtabelle so, dass die primäre NIC und die primäre IP-Adresse als Ziel festgelegt sind.

---

**Failoverclustering in Windows Server**

Wenn Sie den IMDS mit Failoverclustering abfragen, ist es gelegentlich erforderlich, der Routingtabelle eine Route hinzuzufügen. Gehen Sie dabei folgendermaßen vor:

1. Öffnen Sie eine Eingabeaufforderung mit Administratorrechten.

1. Führen Sie den folgenden Befehl aus, und notieren Sie die Adresse der Schnittstelle für das Netzwerkziel (`0.0.0.0`) in der IPv4-Routingtabelle.

```bat
route print
```

> [!NOTE]
> Die folgende Beispielausgabe stammt aus einer Windows Server-VM mit aktiviertem Failovercluster. Der Einfachheit halber enthält die Ausgabe nur die IPv4-Routingtabelle.

```
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
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Führen Sie den folgenden Befehl aus, und verwenden Sie dabei die Adresse der Schnittstelle für das Netzwerkziel (`0.0.0.0`), in diesem Beispiel `10.0.1.10`.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="support"></a>Support

Wenn Sie nach mehreren Versuchen keine Antwort auf die Metadaten erhalten, können Sie ein Supportproblem im Azure-Portal erstellen.

## <a name="product-feedback"></a>Produktfeedback

Sie können in unserem Kanal für Benutzerfeedback in https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627 unter „Virtuelle Computer“ > „Instance Metadata Service“ Feedback zu Produkten geben und Ideen unterbreiten.

## <a name="next-steps"></a>Nächste Schritte

[Abrufen eines Zugriffstokens für die VM](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Scheduled Events für Linux](../articles/virtual-machines/linux/scheduled-events.md)

[Scheduled Events für Windows](../articles/virtual-machines/windows/scheduled-events.md)
