---
title: Importieren und Exportieren einer Domänenzonendatei – Azure CLI
titleSuffix: Azure DNS
description: Informationen zum Importieren und Exportieren einer DNS-Zonendatei in Azure DNS mithilfe der Azure-CLI
services: dns
author: rohinkoul
ms.service: dns
ms.date: 7/30/2020
ms.author: rohink
ms.topic: how-to
ms.openlocfilehash: e2b998432f6c4417da0242d86347ed43acb5071a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968229"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importieren und Exportieren einer DNS-Zonendatei mit der Azure-Befehlszeilenschnittstelle

In diesem Artikel wird das Importieren und Exportieren von DNS-Zonendateien für Azure DNS mit der Azure-Befehlszeilenschnittstelle erläutert.

## <a name="introduction-to-dns-zone-migration"></a>Einführung in die DNS-Zonenmigration

Eine DNS-Zonendatei ist eine Textdatei, die Details aller Domain Name System-Einträge (DNS) in der Zone enthält. Sie hat ein Standardformat, weshalb sie sich für das Übertragen von DNS-Einträgen zwischen DNS-Systemen eignet. Das Verwenden einer Zonendatei ist eine schnelle, zuverlässige und praktische Möglichkeit, eine DNS-Zone in und aus Azure DNS zu übertragen.

Für Azure DNS wird das Importieren und Exportieren von Zonendateien mit der Azure-Befehlszeilenschnittstelle (CLI) unterstützt. Das Importieren von Zonendateien mithilfe von Azure PowerShell oder dem Azure-Portal wird derzeit **nicht** unterstützt.

Die Azure-CLI (Command Line Interface, Befehlszeilenschnittstelle) ist ein plattformübergreifendes Befehlszeilentool zum Verwalten von Azure-Diensten. Sie steht auf der [Azure-Downloadseite](https://azure.microsoft.com/downloads/)für Windows-, Mac- und Linux-Plattformen zur Verfügung. Die plattformübergreifende Unterstützung ist wichtig für das Importieren und Exportieren von Zonendateien, da die am häufigsten verwendete Namenserversoftware ([BIND](https://www.isc.org/downloads/bind/)) meist unter Linux ausgeführt wird.

## <a name="obtain-your-existing-dns-zone-file"></a>Abrufen Ihrer vorhandenen DNS-Zonendatei

Bevor Sie eine DNS-Zonendatei in Azure DNS importieren, müssen Sie eine Kopie der Zonendatei abrufen. Der Speicherort dieser Datei hängt davon ab, wo die DNS-Zone derzeit gehostet wird.

* Wenn Ihre DNS-Zone von einem Partnerdienst (z. B. einer Domänenregistrierungsstelle, einem dedizierten DNS-Hostinganbieter oder anderen Cloudanbieter) gehostet wird, sollte der Dienst die Möglichkeit zum Herunterladen der DNS-Zonendatei bieten.
* Falls Ihre DNS-Zone unter einem Windows-DNS gehostet wird, lautet der Standardordner für die Zonendateien **%systemroot%\system32\dns**. Der vollständige Pfad zu jeder Zonendatei wird auch in der DNS-Konsole auf der Registerkarte **Allgemein** angezeigt.
* Wenn Ihre DNS-Zone mithilfe von BIND gehostet wird, wird der Speicherort der Zonendatei für jede Zone in der BIND-Konfigurationsdatei **named.conf** angegeben.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importieren einer DNS-Zonendatei in Azure DNS

Beim Importieren einer Zonendatei wird eine neue Zone in Azure DNS erstellt, sofern noch keine vorhanden ist. Wenn die Zone bereits vorhanden ist, werden die Ressourceneintragssätze in der Zonendatei mit den vorhandenen Ressourceneintragssätzen zusammengeführt.

### <a name="merge-behavior"></a>Zusammenführungsverhalten

* Standardmäßig werden vorhandene und neue Ressourceneintragssätze zusammengeführt. Identische Datensätze innerhalb eines zusammengeführten Ressourceneintragssatzes werden dedupliziert.
* Wenn Ressourceneintragssätze zusammengeführt werden, wird die Gültigkeitsdauer (Time to live, TTL) von bereits vorhandenen Ressourceneintragssätzen verwendet.
* SOA-Parameter (Start of Authority, Autoritätsursprung) werden (mit Ausnahme von `host`) immer aus der importierten Zonendatei übernommen. Auch für den Namenserver-Ressourceneintragssatz an der Zonenspitze wird die Gültigkeitsdauer stets der importierten Zonendatei entnommen.
* Ein importierter CNAME-Eintrag ersetzt keinen vorhandenen CNAME-Eintrag mit demselben Namen.  
* Wenn ein Konflikt zwischen einem CNAME-Eintrag und einem weiteren Eintrag mit demselben Namen – aber einem anderen Typ – entsteht (unabhängig davon, welcher vorhanden oder neu ist), bleibt der vorhandene Eintrag erhalten. 

### <a name="additional-information-about-importing"></a>Weitere Informationen zum Importieren

Die folgenden Hinweise liefern weitere technische Details zum Zonenimportprozess.

* Die Direktive `$TTL` ist optional und wird unterstützt. Wird die Direktive `$TTL` nicht angegeben, werden Einträge ohne explizite Gültigkeitsdauer mit dem Standardwert der Gültigkeitsdauer von 3.600 Sekunden importiert. Wenn bei zwei Einträgen in demselben Ressourceneintragssatz eine unterschiedliche Gültigkeitsdauer angegeben ist, wird der niedrigere Wert verwendet.
* Die Direktive `$ORIGIN` ist optional und wird unterstützt. Wird `$ORIGIN` nicht festgelegt, ist der verwendete Standardwert der Zonenname gemäß Angabe in der Befehlszeile (plus abschließendem „.“).
* Die Direktiven `$INCLUDE` und `$GENERATE` werden nicht unterstützt.
* Diese Datensatztypen werden unterstützt: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV, TXT.
* Der SOA-Eintrag wird von Azure DNS automatisch erstellt, wenn eine Zone erstellt wird. Wenn Sie eine Zonendatei importieren, werden alle SOA-Parameter aus der Zonendatei entnommen, *mit Ausnahme* des Parameters `host`. Für diesen Parameter wird der Wert verwendet, der von Azure DNS bereitgestellt wird. Der Grund ist, dass dieser Parameter auf den von Azure DNS bereitgestellten primären Namenserver verweisen muss.
* Der Namenserver-Ressourceneintragssatz an der Zonenspitze wird beim Erstellen der Zone auch automatisch von Azure DNS erstellt. Nur die Gültigkeitsdauer dieses Ressourceneintragssatzes wird importiert. Diese Einträge enthalten die von Azure DNS bereitgestellten Namen der Namenserver. Die Eintragsdaten werden von den Werten in der importierten Zonendatei nicht überschrieben.
* Während der öffentlichen Vorschauphase unterstützt Azure DNS nur TXT-Einträge mit einer einzelnen Zeichenfolge. TXT-Einträge mit mehreren Zeichenfolgen werden verkettet und auf 255 Zeichen verkürzt.

### <a name="cli-format-and-values"></a>Format und Werte der Befehlszeilenschnittstelle

Das Format des Befehls der Azure-Befehlszeilenschnittstelle zum Importieren einer DNS-Zone lautet:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Werte:

* `<resource group>` ist der Name der Ressourcengruppe für die Zone in Azure DNS.
* `<zone name>` ist der Name der Zone.
* `<zone file name>` ist der Pfad/Name der zu importierenden Zonendatei.

Wenn Sie eine Zone mit diesem Namen in der Ressourcengruppe nicht vorhanden ist, wird sie für Sie erstellt. Wenn die Zone bereits vorhanden ist, werden die importierten Ressourceneintragssätze mit vorhandenen Ressourceneintragssätzen zusammengeführt. 

### <a name="step-1-import-a-zone-file"></a>Schritt 1: Importieren einer Zonendatei

So importieren Sie eine Zonendatei für die Zone **contoso.com**

1. Falls noch nicht vorhanden, müssen Sie eine Ressourcen-Manager-Ressourcengruppe erstellen.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. Um die Zone **contoso.com** aus der Datei **contoso.com.txt** in eine neue DNS-Zone in der Ressourcengruppe **myresourcegroup** zu importieren, führen Sie den Befehl `az network dns zone import` aus.<BR>Mit diesem Befehl wird die Zonendatei geladen und analysiert. Der Befehl führt eine Reihe von Befehlen für den Azure DNS-Dienst aus, um die Zone und alle Ressourceneintragssätze in der Zone zu erstellen. Außerdem werden mit dem Befehl im Konsolenfenster der Status sowie Fehler und Warnungen angegeben. Da Ressourceneintragssätze in Reihe erstellt werden, dauert der Import einer großen Zonendatei einige Minuten.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Schritt 2: Überprüfen der Zone

Um die DNS-Zone zu überprüfen, nachdem Sie die Datei importiert haben, können Sie eine der folgenden Methoden verwenden:

* Sie können die Einträge mit dem unten angegebenen Azure CLI-Befehl auflisten:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Sie können die Einträge mit dem Azure CLI-Befehl `az network dns record-set ns list` auflisten.
* Sie können `nslookup` verwenden, um die Namensauflösung für die Einträge zu überprüfen. Da die Zone nicht noch delegiert ist, müssen Sie die richtigen Azure DNS-Namenserver explizit angeben. Im folgenden Beispiel wird gezeigt, wie Sie die Namen der Namenserver abrufen, die der Zone zugewiesen sind. Außerdem wird im Beispiel veranschaulicht, wie Sie den „www“-Eintrag mit `nslookup`abfragen.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Schritt 3: Aktualisieren der DNS-Delegierung

Nachdem Sie sichergestellt haben, dass die Zone richtig importiert wurde, müssen Sie die DNS-Delegierung so aktualisieren, dass auf die Azure DNS-Namenserver verwiesen wird. Weitere Informationen finden Sie im Artikel [Delegieren von Domänen an Azure DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportieren einer DNS-Zonendatei aus Azure DNS

Das Format des Azure CLI-Befehls zum Exportieren einer DNS-Zone lautet:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Werte:

* `<resource group>` ist der Name der Ressourcengruppe für die Zone in Azure DNS.
* `<zone name>` ist der Name der Zone.
* `<zone file name>` ist der Pfad/Name der zu exportierenden Zonendatei.

Sie müssen sich – wie beim Zonenimport – zunächst anmelden, Ihr Abonnement auswählen und die Azure-Befehlszeilenschnittstelle so konfigurieren, dass der Ressourcen-Manager-Modus verwendet wird.

### <a name="to-export-a-zone-file"></a>So exportieren Sie eine Zonendatei

Um die vorhandene Azure-DNS-Zone **contoso.com** in die Ressourcengruppe **myresourcegroup** in der Datei **contoso.com.txt** (im aktuellen Ordner) zu exportieren, führen Sie `azure network dns zone export` aus. Mit diesem Befehl wird der Azure DNS-Dienst aufgefordert, die Ressourceneintragssätze in der Zone aufzuzählen und die Ergebnisse in eine mit BIND kompatible Zonendatei zu exportieren.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Ressourceneintragssätze und Einträge in Ihrer DNS-Zone verwalten](./dns-getstarted-cli.md).

* Erfahren Sie, wie Sie [Ihre Domäne an Azure DNS delegieren](dns-domain-delegation.md).