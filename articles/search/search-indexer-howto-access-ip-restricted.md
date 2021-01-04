---
title: Gewähren von Zugriff auf die Indexer-IP-Adressbereiche
titleSuffix: Azure Cognitive Search
description: Konfigurieren Sie IP-Firewallregeln, um den Datenzugriff durch einen Azure Cognitive Search-Indexer zuzulassen.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 12943d5d22a9e81c7e99522fa4728f4798549682
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499950"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Konfigurieren von IP-Firewallregeln zum Zulassen von Indexerverbindungen (Azure Cognitive Search)

IP-Firewallregeln für Azure-Ressourcen wie Speicherkonten, Cosmos DB-Konten und Azure SQL-Server erlauben für den Datenzugriff nur Datenverkehr, der aus bestimmten IP-Adressbereichen stammt.

In diesem Artikel wird beschrieben, wie die IP-Regeln über das Azure-Portal für ein Speicherkonto so konfiguriert werden, dass Indexer von Azure Cognitive Search auf die Daten sicher zugreifen können. Obwohl spezifisch für Azure Storage, funktioniert dieser Ansatz auch bei anderen Azure-Ressourcen, die IP-Firewallregeln zum Sichern des Zugriffs auf Daten verwenden.

> [!NOTE]
> IP-Firewallregeln für Speicherkonten sind nur wirksam, wenn sich das Speicherkonto und der Suchdienst in verschiedenen Regionen befinden. Wenn Ihr Setup dies nicht zulässt, empfehlen wir die Verwendung der [Option „Ausnahme für vertrauenswürdige Dienste“](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Abrufen der IP-Adresse des Suchdiensts

Rufen Sie den vollqualifizierten Domänennamen (FQDN) Ihres Suchdiensts ab. Dieser sieht wie `<search-service-name>.search.windows.net` aus. Sie können den FQDN ermitteln, indem Sie Ihren Suchdienst im Azure-Portal suchen.

   ![Abrufen des Dienst-FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "Abrufen des Dienst-FQDN")

Die IP-Adresse des Suchdiensts kann durch Ausführen von `nslookup` (oder von `ping`) des FQDN abgerufen werden. Im folgenden Beispiel würden Sie einer eingehenden Regel in der Azure Storage-Firewall „10.50.10.50“ hinzufügen.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Abrufen der IP-Adressbereiche für das Diensttag „AzureCognitiveSearch“

Zusätzliche IP-Adressen werden für Anforderungen verwendet, die aus der [mehrinstanzenfähigen Ausführungsumgebung](search-indexer-securing-resources.md#indexer-execution-environment) des Indexers stammen. Sie können diesen IP-Adressbereich aus dem Diensttag abrufen.

Die IP-Adressbereiche für das Diensttag `AzureCognitiveSearch` können entweder über die [Ermittlungs-API (Vorschau)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) oder die [herunterladbare JSON-Datei](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) abgerufen werden.

Für diese exemplarische Vorgehensweise sollte unter der Annahme, dass der Suchdienst der öffentliche Azure-Clouddienst ist, die [öffentliche Azure-JSON-Datei](https://www.microsoft.com/download/details.aspx?id=56519) heruntergeladen werden.

   ![Herunterladen der JSON-Datei](media\search-indexer-howto-secure-access\service-tag.png "Herunterladen der JSON-Datei")

Aus der JSON-Datei sind unter der Annahme, dass sich der Suchdienst in „USA, Westen-Mitte“ befindet, die IP-Adressen für die Ausführungsumgebung des Indexers für mehrere Mandanten nachfolgend aufgeführt.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Für /32-IP-Adressen lassen Sie das „/32“ weg (52.253.133.74/32 -> 52.253.133.74), andere können unverändert verwendet werden.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Hinzufügen von IP-Adressbereichen zu IP-Firewallregeln

Der einfachste Weg, IP-Adressbereiche zur Firewallregel eines Speicherkontos hinzuzufügen, ist über das Azure-Portal. Suchen Sie das Speicherkonto auf dem Portal, und navigieren Sie zur Registerkarte **Firewalls und virtuelle Netzwerke**.

   ![Firewall und virtuelle Netzwerke](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall und virtuelle Netzwerke")

Fügen Sie die drei zuvor abgerufenen IP-Adressen („1“ für die IP-Adresse des Suchdiensts, „2“ für das Diensttag `AzureCognitiveSearch`) in den Adressbereich ein, und wählen Sie **Speichern** aus.

   ![IP-Regeln der Firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "IP-Regeln der Firewall")

Die Firewallregeln benötigen 5–10 Minuten für die Aktualisierung. Danach sollten Indexer auf die Daten im Speicherkonto zugreifen können.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Azure Storage-Firewalls](../storage/common/storage-network-security.md)
- [Konfigurieren der IP-Firewall für Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Konfigurieren der IP-Firewall für Azure SQL Server](../azure-sql/database/firewall-configure.md)