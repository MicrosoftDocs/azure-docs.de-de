---
title: Sicherheit in Azure Blockchain
description: Datenzugriff und Sicherheitskonzepte des Azure Blockchain-Diensts
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027656"
---
# <a name="azure-blockchain-service-security"></a>Sicherheit in Azure Blockchain

Azure Blockchain verwendet mehrere Azure-Funktionen, um die Sicherheit und Verfügbarkeit Ihrer Daten zu gewährleisten. Daten werden mithilfe Isolation, Verschlüsselung und Authentifizierung gesichert.

## <a name="isolation"></a>Isolation

Azure Blockchain-Ressourcen werden in einem privaten virtuellen Netzwerk isoliert. Jeder Transaktions- und Validierungsknoten ist ein virtueller Computer (VM). Virtuelle Computer in einem virtuellen Netzwerk können nicht direkt mit virtuellen Computern in einem anderen virtuellen Netzwerk kommunizieren. Durch die Isolation wird sichergestellt, dass die Kommunikation innerhalb des virtuellen Netzwerks privat bleibt. Weitere Informationen zur Isolation virtueller Azure-Netzwerke finden Sie unter [isolation in the Azure Public Cloud (Isolation in der öffentlichen Azure-Cloud)](../../security/azure-isolation.md#networking-isolation).

![VNET-Diagramm](./media/data-security/vnet.png)

## <a name="encryption"></a>Verschlüsselung

Benutzerdaten werden in Azure Storage gespeichert. Benutzerdaten werden im Sinne der Sicherheit und Vertraulichkeit während der Übertragung und im Ruhezustand verschlüsselt. Weitere Informationen finden Sie unter [Azure Storage-Sicherheitsleitfaden](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Authentication

Transaktionen können über einen RPC-Endpunkt an Blockchainknoten gesendet werden. Clients kommunizieren mit einem Transaktionsknoten mithilfe eines Reverseproxyservers, der die Benutzerauthentifizierung durchführt und Daten über SSL verschlüsselt.

![Authentifizierungsdiagramm](./media/data-security/authentication.png)

Es gibt drei Authentifizierungsmodi für den RPC-Zugriff.

### <a name="basic-authentication"></a>Standardauthentifizierung

Die Standardauthentifizierung verwendet einen HTTP-Authentifizierungsheader, der den Benutzernamen und das Kennwort enthält. Der Benutzername entspricht dem Namen des Blockchainknotens. Das Kennwort wird während der Bereitstellung eines Mitglieds oder eines Knotens festgelegt. Das Kennwort kann im Azure-Portal oder mithilfe der Befehlszeilenschnittstelle geändert werden.

### <a name="access-keys"></a>Zugriffsschlüssel

Zugriffsschlüssel verwenden eine zufällig generierte Zeichenfolge, die in der Endpunkt-URL enthalten ist. Zwei Zugriffsschlüssel aktivieren die Schlüsselrotation. Schlüssel können im Azure-Portal und mithilfe der Befehlszeilenschnittstelle neu generiert werden.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) verwendet einen anspruchsbasierten Authentifizierungsmechanismus, bei dem der Benutzer mithilfe der Azure AD-Benutzeranmeldeinformationen von Azure AD authentifiziert wird. Azure AD bietet eine cloudbasierte Identitätsverwaltung und ermöglicht Kunden das Verwenden einer einzigen Identität für ein gesamtes Unternehmen und den Zugriff auf Aufwendungen in der Cloud. Azure Blockchain kann in Azure AD integriert werden, wodurch der ID-Verbund, das einmalige Anmelden und die mehrstufige Authentifizierung aktiviert werden. Sie können Benutzern, Gruppen und Anwendungsrollen in Ihrer Organisation Zugriff auf Blockchainmitglieder und Knoten zuweisen.

Der Azure AD-Clientproxy ist verfügbar unter [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Der Clientproxy leitet den Benutzer auf die Azure AD-Anmeldeseite weiter und ruft bei erfolgreicher Authentifizierung ein Bearertoken ab. Anschließend verbindet der Benutzer eine Ethereum-Clientanwendung wie z. B. Geth oder Truffle mit dem Clientproxy-Endpunkt. Wenn eine Transaktion schließlich übermittelt wurde, fügt der Clientproxy das Bearertoken in den HTTP-Header ein, und der Reverseproxy überprüft das Token mithilfe eines OAuth-Protokolls.

## <a name="keys-and-ethereum-accounts"></a>Schlüssel und Ethereum-Konten

Bei der Bereitstellung eines Azure Blockchain-Mitglieds wird ein Ethereum-Konto sowie ein öffentliches und ein privates Schlüsselpaar generiert. Der private Schlüssel wird zum Senden von Transaktionen an die Blockchain verwendet. Das Ethereum-Konto entspricht den letzten 20 Bytes des Hashs des öffentlichen Schlüssels. Das Ethereum-Konto wird auch als Wallet bezeichnet.

Das private und das öffentliche Schlüsselpaar wird als Schlüsseldatei im JSON-Format gespeichert. Der private Schlüssel wird mithilfe des Kennworts verschlüsselt, das beim Erstellen des Blockchain-Ledgerdiensts eingegeben wurde.

Private Schlüssel werden zum digitalen Signieren von Transaktionen verwendet. In privaten Blockchains stellt ein von einem privaten Schlüssel signierter Smart Contract die Identität der Signatur dar. Um die Gültigkeit der Signatur zu überprüfen, kann der Empfänger den öffentlichen Schlüssel des Signaturgebers mit der von der Signatur berechneten Adresse vergleichen.

Konstellationsschlüssel werden zum eindeutigen Identifizieren von Quorum-Knoten verwendet. Constellation-Schlüssel werden zum Zeitpunkt der Knotenbereitstellung generiert und werden in privateFor-Parametern einer privaten Transaktion in Quorum angegeben.

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Azure Blockchain-Transaktionsknoten](configure-transaction-nodes.md)
