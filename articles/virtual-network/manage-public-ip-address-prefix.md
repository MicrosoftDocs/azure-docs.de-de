---
title: Erstellen, Ändern oder Löschen des Präfix einer öffentlichen Azure-IP-Adresse
titlesuffix: Azure Virtual Network
description: Hier erhalten Sie Informationen zu Präfixen öffentlicher IP-Adressen und dazu, wie Sie diese erstellen, ändern und löschen. Sie erfahren außerdem, wo Sie weitere Informationen finden.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 3b86f9bcbc863a78fd5f8f748e973a20ea709636
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573169"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Erstellen, Ändern oder Löschen des Präfix einer öffentlichen IP-Adresse

Sie erhalten Informationen über Präfixe öffentlicher IP-Adressen und darüber, wie Sie diese erstellen, ändern und löschen können. Das Präfix einer öffentlichen IP-Adresse ist ein zusammenhängender Adressbereich, der auf der Anzahl der von Ihnen angegeben IP-Adressen basiert. Die Adressen sind Ihrem Abonnement zugewiesen. Wenn Sie eine öffentliche IP-Adressressource erstellen, können Sie eine statische öffentliche IP-Adresse aus dem Präfix zuweisen und virtuellen Computern, Lastenausgleichsmodulen oder anderen Ressourcen zuordnen, um Internetkonnektivität zu ermöglichen. Wenn Sie mit Präfixen öffentlicher IP-Adressen nicht vertraut sind, finden Sie weitere Informationen unter [Übersicht über Präfixe öffentlicher IP-Adressen](public-ip-address-prefix.md).

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Öffnen Sie bei Verwendung des Portals https://portal.azure.com, und melden Sie sich mit Ihrem Azure-Konto an.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist die Azure CLI-Version 2.0.41 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli) Informationen dazu. Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

Für Präfixe öffentlicher IP-Adressen fällt eine Gebühr an. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Erstellen des Präfix einer öffentlichen IP-Adresse

1. Wählen Sie in der linken oberen Ecke des Portals die Option **+ Ressource erstellen** aus.
2. Geben Sie im Feld *Marketplace durchsuchen* den Begriff *Präfix für öffentliche IP-Adresse* ein. Wenn **Präfix einer öffentlichen IP-Adresse** in den Suchergebnissen angezeigt wird, klicken Sie darauf.
3. Klicken Sie unter **Präfix einer öffentlichen IP-Adresse** auf **Erstellen**.
4. Geben Sie unter **Präfix einer öffentlichen IP-Adresse erstellen** Werte für folgende Einstellungen ein, oder wählen Sie Werte aus, und klicken Sie dann auf **Erstellen**:

   |Einstellung|Erforderlich?|Details|
   |---|---|---|
   |Subscription|Ja|Muss im selben [Abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) wie die Ressource vorhanden sein, der Sie die öffentliche IP-Adresse zuordnen möchten|
   |Resource group|Ja|Kann in derselben oder in einer anderen [Ressourcengruppe](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) wie die Ressource vorhanden sein, der Sie die öffentliche IP-Adresse zuordnen möchten|
   |Name|Ja|Der Name muss innerhalb der ausgewählten Ressourcengruppe eindeutig sein.|
   |Region|Ja|Muss in der gleichen [Region](https://azure.microsoft.com/regions) wie die öffentlichen IP-Adressen vorhanden sein, die Sie aus dem Bereich zuweisen.|
   |Präfixgröße|Ja| Die benötigte Größe des Präfix. /28- oder 16-IP-Adressen sind der Standard.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>In Regionen mit Verfügbarkeitszonen können Sie PowerShell- oder CLI-Befehle verwenden, um ein Präfix für eine öffentliche IP-Adresse zu erstellen: nicht zonal, mit einer bestimmten Zone verknüpft oder zur Verwendung von Zonenredundanz.  Wenn kein Zonenparameter bereitgestellt wird, wird für API-Version 2020-08-01 oder höher ein nicht zonales öffentliches IP-Adresspräfix erstellt. Für ältere API-Versionen als 2020-08-01 wird ein zonenredundantes öffentliches IP-Adresspräfix erstellt. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Erstellen einer statischen öffentlichen IP-Adresse aus einem Präfix
Sobald Sie ein Präfix erstellt haben, müssen Sie statische IP-Adressen aus dem Präfix erstellen. Führen Sie dazu die folgenden Schritte aus.

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Präfix öffentlicher IP-Adressen* ein. Wenn **Präfix öffentlicher IP-Adressen** in den Suchergebnissen angezeigt wird, klicken Sie darauf.
2. Wählen Sie das Präfix aus, aus dem Sie öffentliche IPs erstellen möchten.
3. Wenn das Präfix in den Suchergebnissen angezeigt wird, wählen Sie es aus, und klicken Sie im Abschnitt „Übersicht“ auf **+ IP-Adresse hinzufügen**.
4. Geben Sie unter **Öffentliche IP-Adresse erstellen** Werte für folgende Einstellungen ein, oder wählen Sie Werte aus. Da sich ein Präfix für Standard-SKUs, IPv4 und statische Adressen eignet, müssen Sie nur die folgenden Informationen angeben:

   |Einstellung|Erforderlich?|Details|
   |---|---|---|
   |Name|Ja|Der Name der öffentlichen IP-Adresse muss innerhalb der ausgewählten Ressourcengruppe eindeutig sein.|
   |Leerlaufzeitüberschreitung (Minuten)|Nein|Gibt an, wie viele Minuten eine TCP- oder HTTP-Verbindung geöffnet bleiben soll, ohne dass Clients Keep-Alive-Meldungen senden müssen. |
   |DNS-Namensbezeichnung|Nein|Muss in der Azure-Region, in der Sie den Namen erstellen, eindeutig sein (über alle Abonnements und Kunden hinweg). Azure registriert den Namen und die IP-Adresse automatisch im DNS, sodass Sie über den Namen eine Verbindung mit der Ressource herstellen können. Azure fügt ein Standardsubnetz wie etwa *location.cloudapp.azure.com* (wobei „location“ der Standort ist, den Sie auswählen) an den von Ihnen bereitgestellten Namen an, um den vollqualifizierten DNS-Namen zu erstellen. Weitere Informationen finden Sie unter [Verwenden von Azure DNS mit einer öffentlichen Azure-IP-Adresse](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternativ können Sie die folgenden CLI- und PS-Befehle mit den Parametern --public-ip-prefix (CLI) und -PublicIpPrefix (PS) verwenden, um eine öffentliche IP-Adressressource zu erstellen. 

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Anzeigen oder Löschen eines Präfix

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Präfix öffentlicher IP-Adressen* ein. Wenn **Präfix öffentlicher IP-Adressen** in den Suchergebnissen angezeigt wird, klicken Sie darauf.
2. Wählen Sie den Namen des Präfix der öffentlichen IP-Adresse aus, das Sie anzeigen oder aus der Liste löschen oder dessen Einstellungen Sie ändern möchten.
3. Führen Sie eine der folgenden Aktionen aus, je nachdem, ob Sie das Präfix der öffentlichen IP-Adresse anzeigen, löschen oder ändern möchten.
   - **Anzeigen**: Der Abschnitt **Übersicht** enthält wichtige Einstellungen für das Präfix der öffentlichen IP-Adresse.
   - **Löschen:** Klicken Sie im Abschnitt **Übersicht** auf **Löschen**, um das Präfix der öffentlichen IP-Adresse zu löschen. Wenn Adressen innerhalb des Präfix mit öffentlichen IP-Adressressourcen verknüpft sind, müssen Sie zuerst die öffentlichen IP-Adressressourcen löschen. Informationen dazu finden Sie unter [Löschen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) zum Auflisten der öffentlichen IP-Adressen, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) zum Anzeigen der Einstellungen, [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) zum Aktualisieren, [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) zum Löschen|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) zum Abrufen eines öffentlichen IP-Adressobjekts und Anzeigen der zugehörigen Einstellungen, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) zum Aktualisieren der Einstellungen, [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) zum Löschen|

## <a name="permissions"></a>Berechtigungen

Zum Durchführen von Aufgaben für Präfixe öffentlicher IP-Adressen muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die entsprechenden, in der folgenden Tabelle aufgeführten Aktionen zugewiesen wurden:

| Aktion                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Lesen des Präfix einer öffentlichen IP-Adresse                                |
| Microsoft.Network/publicIPPrefixes/write                          | Erstellen oder Aktualisieren des Präfix einer öffentlichen IP-Adresse                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Löschen des Präfix einer öffentlichen IP-Adresse                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Erstellen einer öffentlichen IP-Adresse aus einem Präfix |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über Szenarien und Vorteile der Verwendung des [Präfix einer öffentlichen IP](public-ip-address-prefix.md).
