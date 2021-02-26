---
title: Konfigurieren einer IP-Firewall für Ihren Azure Cognitive Search-Dienst
titleSuffix: Azure Cognitive Search
description: Konfigurieren Sie IP-Steuerungsrichtlinien, um den Zugriff auf Ihren Azure Cognitive Search-Dienst einzuschränken.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 67fa53e565de70d3da0b90e36a968b68d54d667f
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573244"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Konfigurieren einer IP-Firewall für Azure Cognitive Search

Azure Cognitive Search unterstützt IP-Regeln zur Unterstützung eingehender Firewalls. Dieses Modell bietet eine zusätzliche Sicherheitsebene für Ihren Suchdienst, ähnlich den IP-Regeln, die Sie in einer Azure Virtual Network-Sicherheitsgruppe finden. Mit diesen IP-Regeln können Sie nun Ihren Suchdienst so konfigurieren, dass nur von einer genehmigten Gruppe von Computern und/oder Clouddiensten darauf zugegriffen werden kann. Für den Zugriff auf gespeicherte Daten in Ihrem Suchdienst über diese genehmigten Gruppen von Computern und Diensten muss der Aufrufer weiterhin ein gültiges Autorisierungstoken vorlegen.

> [!Important]
> IP-Regeln für Ihren Azure Cognitive Search-Dienst können mithilfe des Azure-Portals oder der [Verwaltungs-REST-API, Version 2020-03-13,](/rest/api/searchmanagement/) konfiguriert werden.

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Konfigurieren einer IP-Firewall über das Azure-Portal

Um die IP-Zugriffssteuerungsrichtlinie im Azure-Portal festzulegen, wechseln Sie zur Seite des Azure Cognitive Search-Diensts, und wählen Sie im Navigationsmenü **Netzwerk** aus. Die Endpunktnetzwerkkonnektivität muss **öffentlich** sein. Wenn Ihre Konnektivität auf **Privat** festgelegt ist, können Sie nur über einen privaten Endpunkt auf Ihren Suchdienst zugreifen.

![Screenshot: Konfigurieren der IP-Firewall im Azure-Portal](./media/service-configure-firewall/azure-portal-firewall.png)

Das Azure-Portal bietet die Möglichkeit, IP-Adressen und IP-Adressbereiche im CIDR-Format anzugeben. Ein Beispiel für die CIDR-Notation ist „8.8.8.0/24“, womit die IPs im Bereich zwischen 8.8.8.0 und 8.8.8.255 dargestellt werden.

> [!NOTE]
> Nach dem Aktivieren der IP-Zugriffssteuerungsrichtlinie für Ihren Azure Cognitive Search-Dienst werden alle Anforderungen an die Datenebene von Computern außerhalb der Liste der zulässigen IP-Adressbereiche abgelehnt. Werden IP-Regeln konfiguriert, werden einige Features des Azure-Portals deaktiviert. Sie können Informationen auf Dienstebene anzeigen und verwalten, aber der Portalzugriff auf Indexdaten und die verschiedenen Komponenten im Dienst sind aus Sicherheitsgründen eingeschränkt. Dazu zählen beispielsweise Index-, Indexer- und Skillsetdefinitionen. Als Alternative zum Portal können Sie die [VS Code-Erweiterung](https://aka.ms/vscode-search) verwenden, um mit den verschiedenen Komponenten im Dienst zu interagieren.

### <a name="requests-from-your-current-ip"></a>Anforderungen über Ihre aktuelle IP-Adresse

Um die Entwicklung zu vereinfachen, unterstützt das Azure-Portal Sie dabei, die IP-Adresse Ihres Clientcomputers zu ermitteln und der Zulassungsliste hinzuzufügen. So können auf Ihrem Computer ausgeführte Apps auf Ihren Azure Cognitive Search-Dienstzugreifen.

Das Portal erkennt Ihre Client-IP-Adresse automatisch. Es kann sich um die Client-IP-Adresse Ihres Computers oder Netzwerkgateways handeln. Sie müssen diese IP-Adresse entfernen, bevor Sie Ihre Workload in die Produktion überführen.

Um ihre aktuelle IP-Adresse zur Liste der IP-Adressen hinzuzufügen, aktivieren Sie **Client-IP-Adresse hinzufügen**. Klicken Sie dann auf **Speichern**.

![Screenshot: Konfigurieren der IP-Firewalleinstellungen zum Zulassen der aktuellen IP-Adresse](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Behandeln von Problemen mit einer IP-Zugriffssteuerungsrichtlinie

Sie haben folgende Optionen, um Probleme mit einer IP-Zugriffssteuerungsrichtlinie zu behandeln:

### <a name="azure-portal"></a>Azure-Portal

Durch das Aktivieren der IP-Zugriffssteuerungsrichtlinie für Ihren Azure Cognitive Search-Dienst werden alle Anforderungen von Computern außerhalb der Liste der zulässigen IP-Adressbereiche, einschließlich des Azure-Portals, abgelehnt.  Sie können Informationen auf Dienstebene anzeigen und verwalten, aber der Portalzugriff auf Indexdaten und die verschiedenen Komponenten im Dienst sind aus Sicherheitsgründen eingeschränkt. Dazu zählen beispielsweise Index-, Indexer- und Skillsetdefinitionen. 

### <a name="sdks"></a>SDKs

Beim Zugriff auf den Azure Cognitive Search-Dienst mithilfe des SDKs über Computer, die nicht in der Zulassungsliste enthalten sind, wird eine generische Antwort **403: Nicht zulässig** ohne weitere Details zurückgegeben. Überprüfen Sie die Liste der zugelassenen IP-Adressen für Ihr Konto, und stellen Sie sicher, dass die richtige Konfiguration für Ihren Suchdienst aktualisiert wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Zugreifen auf Ihren Suchdienst über Azure Private Link finden Sie im folgenden Artikel:

* [Erstellen eines privaten Endpunkts für sichere Verbindungen mit Azure Cognitive Search](service-create-private-endpoint.md)