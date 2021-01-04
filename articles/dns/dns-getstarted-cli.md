---
title: 'Schnellstart: Erstellen einer Azure DNS-Zone und eines entsprechenden Eintrags – Azure CLI'
titleSuffix: Azure DNS
description: 'Schnellstart: Erfahren Sie, wie Sie eine DNS-Zone und einen DNS-Eintrag in Azure DNS erstellen. Dies ist eine schrittweise Anleitung zum Erstellen und Verwalten Ihrer ersten DNS-Zone und Ihres ersten DNS-Eintrags mithilfe der Azure CLI.'
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1929cd512d18d7fd234aff1f55814c423455e63b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561368"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Schnellstart: Erstellen einer Azure DNS-Zone und eines Azure DNS-Eintrags mithilfe der Azure CLI

In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie mithilfe der für Windows, Mac und Linux verfügbaren Azure CLI Ihre erste DNS-Zone und Ihren ersten DNS-Eintrag erstellen. Sie können diese Schritte auch im [Azure-Portal](dns-getstarted-portal.md) oder mithilfe von [Azure PowerShell](dns-getstarted-powershell.md) ausführen.

Eine DNS-Zone wird zum Hosten der DNS-Einträge für eine bestimmte Domäne verwendet. Wenn Sie eine Domäne in Azure DNS hosten möchten, müssen Sie eine DNS-Zone für diesen Domänennamen erstellen. Jeder DNS-Eintrag für Ihre Domäne wird dann in dieser DNS-Zone erstellt. Und schließlich müssen Sie die Namenserver für die Domäne konfigurieren, um Ihre DNS-Zone im Internet zu veröffentlichen. Jeder dieser Schritte wird im Folgenden beschrieben.

Azure DNS unterstützt auch private DNS-Zonen. Weitere Informationen zu privaten DNS-Zonen finden Sie unter [Using Azure DNS for private domains](private-dns-overview.md) (Verwenden von Azure DNS für private Domänen). Ein Beispiel für die Erstellung einer privaten DNS-Zone finden Sie unter [Erstellen einer privaten Azure DNS-Zone mit der Azure-Befehlszeilenschnittstelle](./private-dns-getstarted-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Bevor Sie die DNS-Zone erstellen, erstellen Sie eine Ressourcengruppe für die DNS-Zone:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Erstellen einer DNS-Zone

Eine DNS-Zone wird mit dem `az network dns zone create` -Befehl erstellt. Hilfe zu diesem Befehl erhalten Sie mit `az network dns zone create -h`.

Im folgenden Beispiel wird in der Ressourcengruppe *MyResourceGroup* eine DNS-Zone namens *contoso.xyz* erstellt. Verwenden Sie das Beispiel, um eine DNS-Zone zu erstellen, und ersetzen Sie dabei die Werte durch Ihre eigenen.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Erstellen eines DNS-Eintrags

Verwenden Sie zum Erstellen eines DNS-Eintrags den Befehl `az network dns record-set [record type] add-record`. Hilfe zu A-Einträgen finden Sie unter `azure network dns record-set A add-record -h`.

Im folgenden Beispiel wird ein Eintrag mit dem relativen Namen „www“ in der DNS-Zone „contoso.xyz“ in der Ressourcengruppe „MyResourceGroup“ erstellt. Der vollqualifizierte Name des Eintragssatzes lautet „www.contoso.xyz“. Der Eintragstyp lautet „A“ mit der IP-Adresse „10.10.10.10“ und einer Standardgültigkeitsdauer (TTL) von 3.600 Sekunden (eine Stunde).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Anzeigen von Datensätzen

Listen Sie die DNS-Einträge in Ihrer Zone mit diesem Befehl auf:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testen der Namensauflösung

Sie besitzen nun eine DNS-Testzone mit einem A-Testeintrag und können die Namensauflösung mit dem Tool *nslookup* testen. 

**Testen Sie die DNS-Namensauflösung wie folgt:**

1. Führen Sie das folgende Cmdlet aus, um die Liste mit den Namenservern für Ihre Zone abzurufen:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Kopieren Sie einen der Namen der Namenserver aus der Ausgabe des vorherigen Schritts.

1. Öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Beispiel:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Es sollte ein Bildschirm angezeigt werden, der in etwa wie folgt aussieht:

   ![Screenshot, der ein Eingabeaufforderungsfenster mit einem Namenserver-Suchbefehl und Werten für Server, Adresse, Name und Adresse zeigt.](media/dns-getstarted-portal/nslookup.PNG)

Der Hostname **www\.contoso.xyz** wird gemäß Ihrer Konfiguration zu **10.10.10.10** aufgelöst. Mit diesem Ergebnis wird bestätigt, dass die Namensauflösung richtig funktioniert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn sie nicht mehr benötigt werden, können Sie alle in diesem Schnellstart erstellten Ressourcen löschen, indem Sie die Ressourcengruppe löschen:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun Ihre erste DNS-Zone und Ihren ersten -Eintrag mit der Azure CLI erstellt haben, können Sie Einträge für eine Web-App in einer benutzerdefinierten Domäne erstellen.

> [!div class="nextstepaction"]
> [Erstellen von DNS-Einträgen für eine Web-App in einer benutzerdefinierten Domäne](./dns-web-sites-custom-domain.md)
