---
title: Erstellen von DNS-Zonen und -Ressourceneintragssätzen mithilfe des .NET SDK
titleSuffix: Azure DNS
description: In diesem Lernpfad erhalten Sie eine Einführung in das Erstellen von DNS-Zonen und -Ressourceneintragssätzen in Azure DNS mit dem .NET SDK.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e116096afbd01af4914be49d5675881724d5069
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015059"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Erstellen von DNS-Zonen und -Ressourceneintragssätzen mithilfe des .NET SDK

Sie können die Vorgänge zum Erstellen, Löschen oder Aktualisieren von DNS-Zonen, Ressourceneintragssätzen und Einträgen mithilfe des DNS SDK mit der .NET DNS Management-Bibliothek automatisieren. Ein vollständiges Visual Studio-Projekt steht Ihnen [hier](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True) zur Verfügung.

## <a name="create-a-service-principal-account"></a>Erstellen eines Dienstprinzipalkontos

Normalerweise wird der programmgesteuerte Zugriff auf Azure-Ressourcen über ein dediziertes Konto und nicht über Ihre eigenen Benutzeranmeldeinformationen gewährt. Diese dedizierten Konten werden als „Dienstprinzipalkonten“ bezeichnet. Zum Verwenden des Azure DNS SDK-Beispielprojekts müssen Sie zuerst ein Dienstprinzipalkonto erstellen und diesem die richtigen Berechtigungen zuweisen.

1. Führen Sie diese [Anleitungsschritte](../active-directory/develop/howto-authenticate-service-principal-powershell.md) aus, um ein Dienstprinzipalkonto zu erstellen (für das Azure DNS SDK-Beispielprojekt wird die kennwortbasierte Authentifizierung vorausgesetzt).
2. Erstellen Sie eine Ressourcengruppe ([Anleitung](../azure-resource-manager/templates/deploy-portal.md)).
3. Verwenden Sie Azure RBAC, um dem Dienstprinzipalkonto Berechtigungen vom Typ „DNS Zone Contributor“ (DNS-Zone – Mitwirkender) für die Ressourcengruppe zu gewähren ([Anleitung](../role-based-access-control/role-assignments-portal.md)).
4. Bearbeiten Sie die Datei „program.cs“ wie folgt, wenn Sie das Azure DNS SDK-Beispielprojekt verwenden:

   * Fügen Sie die richtigen Werte für `tenantId`, `clientId` (auch als Konto-ID bezeichnet), `secret` (Kennwort des Dienstprinzipalkontos) und `subscriptionId` gemäß Schritt 1 ein.
   * Geben Sie den Ressourcengruppennamen ein, den Sie in Schritt 2 gewählt haben.
   * Geben Sie einen DNS-Zonennamen Ihrer Wahl ein.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-Pakete und Namespacedeklarationen

Zum Verwenden des Azure DNS .NET SDK müssen Sie das NuGet-Paket **Azure DNS Management Library** und andere erforderliche Azure-Pakete installieren.

1. Öffnen Sie in **Visual Studio** ein vorhandenes oder neues Projekt.
2. Navigieren Sie zu **Extras** **>** **NuGet-Paket-Manager** **>** **NuGet-Pakete für Projektmappe verwalten**.
3. Klicken Sie auf **Durchsuchen**, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**, und geben Sie im Suchfeld den Text **Microsoft.Azure.Management.Dns** ein.
4. Wählen Sie das Paket aus, und klicken Sie auf **Installieren** , um es dem Visual Studio-Projekt hinzuzufügen.
5. Wiederholen Sie den obigen Vorgang, um auch die folgenden Pakete zu installieren: **Microsoft.Rest.ClientRuntime.Azure.Authentication** und **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Hinzufügen von Namespace-Deklarationen

Fügen Sie die folgenden Namespacedeklarationen hinzu:

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Initialisieren des DNS-Verwaltungsclients

Der `DnsManagementClient` enthält die Methoden und Eigenschaften, die für die Verwaltung von DNS-Zonen und Ressourceneintragssätzen erforderlich sind.  Mit dem folgenden Code wird die Anmeldung am Dienstprinzipalkonto durchgeführt und ein `DnsManagementClient`-Objekt erstellt.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Erstellen oder Aktualisieren einer DNS-Zone

Für die Erstellung einer DNS-Zone wird zuerst ein Zone-Objekt erstellt, das die DNS-Zonenparameter enthält. Da DNS-Zonen nicht mit einer bestimmten Region verknüpft sind, wird der Standort auf „global“ festgelegt. In diesem Beispiel wird der Zone auch ein [Azure Resource Manager-„Tag“](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) hinzugefügt.

Für die eigentliche Erstellung oder Aktualisierung der Zone in Azure DNS wird das Zonenobjekt, das die Zonenparameter enthält, an die `DnsManagementClient.Zones.CreateOrUpdateAsyc`-Methode übergeben.

> [!NOTE]
> DnsManagementClient unterstützt drei Betriebsmodi: synchron („CreateOrUpdate“), asynchron („CreateOrUpdateAsync“) oder asynchron mit Zugriff auf die HTTP-Antwort („CreateOrUpdateWithHttpMessagesAsync“).  Sie können je nach Ihren Anwendungsanforderungen einen beliebigen Modus auswählen.

Azure DNS unterstützt die optimistische Parallelität, die [Etags](./dns-getstarted-powershell.md)genannt wird. In diesem Beispiel wird Azure DNS durch die Angabe von „*“ für den „If-None-Match“-Header angewiesen, eine DNS-Zone zu erstellen, falls diese noch nicht vorhanden ist.  Der Aufruf schlägt fehl, wenn in der jeweiligen Ressourcengruppe bereits eine Zone mit dem angegebenen Namen vorhanden ist.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Erstellen von DNS-Ressourceneintragssätzen und -Einträgen

DNS-Einträge werden als Ressourceneintragssatz verwaltet. Ein Ressourceneintragssatz ist eine Gruppe von Einträgen mit demselben Namen und Eintragstyp in einer Zone.  Der Name des Ressourceneintragssatzes ist relativ zum Zonennamen, nicht zum vollqualifizierten DNS-Namen.

Zum Erstellen oder Aktualisieren eines Ressourceneintragssatzes wird ein RecordSet-Parameterobjekt erstellt und an `DnsManagementClient.RecordSets.CreateOrUpdateAsync` übergeben. Wie bei DNS-Zonen auch, werden drei Betriebsmodi unterstützt: synchron („CreateOrUpdate“), asynchron („CreateOrUpdateAsync“) oder asynchron mit Zugriff auf die HTTP-Antwort („CreateOrUpdateWithHttpMessagesAsync“).

Ebenfalls wie bei DNS-Zonen verfügen Vorgänge für Ressourceneintragssätze über Unterstützung der optimistischen Nebenläufigkeit.  Da in diesem Beispiel weder „If-Match“ noch „If-None-Match“ angegeben ist, wird der Ressourceneintragssatz immer erstellt.  Mit diesem Aufruf werden alle vorhandenen Ressourceneintragssätze mit demselben Namen und Eintragstyp in dieser DNS-Zone überschrieben.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Abrufen von Zonen und Ressourceneintragssätzen

Die Methoden `DnsManagementClient.Zones.Get` und `DnsManagementClient.RecordSets.Get` rufen einzelne Zonen bzw. Ressourceneintragssätze ab. Ressourceneintragssätze werden anhand des Typs, Namens und der Zone (und Ressourcengruppe) bestimmt, in der sie sich befinden. Zonen werden anhand des Namens und der Ressourcengruppe bestimmt, in der sie sich befinden.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualisieren eines vorhandenen Ressourceneintragssatzes

Rufen Sie zum Aktualisieren eines vorhandenen DNS-Ressourceneintragssatzes zuerst den Ressourceneintragssatz auf, und aktualisieren Sie dann seinen Inhalt, bevor Sie die Änderung übermitteln.  In diesem Beispiel geben wir das „ETag“ aus dem abgerufenen Ressourceneintragssatz im Parameter „If-Match“ an. Der Aufruf schlägt fehl, wenn ein gleichzeitiger Vorgang den Ressourceneintragssatz in der Zwischenzeit geändert hat.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Auflisten von Zonen und Ressourceneintragssätzen

Verwenden Sie zum Auflisten von Zonen die *DnsManagementClient.Zones.List...* -Methoden, die entweder das Auflisten aller Zonen einer bestimmten Ressourcengruppe oder aller Zonen eines bestimmten Azure-Abonnements (ressourcenübergreifend) unterstützen. Verwenden Sie zum Auflisten von Ressourceneintragssätzen die *DnsManagementClient.RecordSets.List...* -Methoden, die entweder das Auflisten aller Ressourceneintragssätze einer bestimmten Zone oder nur der Ressourceneintragssätze eines bestimmten Typs unterstützen.

Beachten Sie beim Auflisten von Zonen und Ressourceneintragssätzen, dass die Ergebnisse auf mehrere Seiten aufgeteilt sein können.  Im folgenden Beispiel wird veranschaulicht, wie Sie die Seiten mit den Ergebnissen durchlaufen können. (Um die Aufteilung auf Seiten zu erzwingen, wird eine sehr kleine Seitengröße von „2“ verwendet. In der Praxis sollte dieser Parameter weggelassen und die Standardseitengröße verwendet werden.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Nächste Schritte

Laden Sie das [Azure DNS .NET SDK-Beispielprojekt](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True) herunter, in dem weitere Beispiele für die Verwendung des Azure DNS .NET SDK enthalten sind, z.B. für andere DNS-Eintragstypen.