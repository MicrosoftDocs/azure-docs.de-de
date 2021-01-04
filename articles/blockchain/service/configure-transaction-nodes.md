---
title: Konfigurieren von Azure Blockchain-Transaktionsknoten
description: Erfahren Sie, wie Sie Azure Blockchain-Transaktionsknoten konfigurieren.
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 50f9b2aa0f5e1a1b1095b6c799ce0706b6e541de
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150870"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurieren von Azure Blockchain-Transaktionsknoten

Transaktionsknoten werden zum Senden von Blockchaintransaktionen an den Azure Blockchain-Dienst über einen öffentlichen Endpunkt verwendet. Der Standardtransaktionsknoten enthält den privaten Schlüssel des in der Blockchain registrierten Ethereum-Kontos und kann deshalb nicht gelöscht werden.

So zeigen Sie die Details zum Standardtransaktionsknoten an:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu Ihrem Azure Blockchain-Mitglied. Klicken Sie auf **Transaktionsknoten** .

    ![Standardtransaktionsknoten auswählen](./media/configure-transaction-nodes/nodes.png)

    Zu den in der Übersicht angezeigten Informationen gehören die öffentliche Endpunktadresse und der öffentliche Schlüssel.

## <a name="create-transaction-node"></a>Erstellen eines Transaktionsknotens

Sie können bis zu neun zusätzliche Transaktionsknoten für Ihr Blockchainmitglied erstellen. Sie können also über maximal 10 Transaktionsknoten verfügen. Durch Hinzufügen von Transaktionsknoten können Sie die Skalierbarkeit erhöhen oder Last verteilen. Sie können beispielsweise über einen Transaktionsknotenendpunkt für verschiedene Clientanwendungen verfügen.

So fügen Sie einen Transaktionsknoten hinzu:

1. Navigieren Sie im Azure-Portal zu Ihrem Azure Blockchain-Mitglied, und klicken Sie auf **Transaktionsknoten > Hinzufügen** .
1. Vervollständigen Sie die Einstellungen für den neuen Transaktionsknoten.

    ![Transaktionsknoten hinzufügen](./media/configure-transaction-nodes/add-node.png)

    | Einstellung | BESCHREIBUNG |
    |---------|-------------|
    | Name | Der Name des Transaktionsknotens. Der Name wird zum Erstellen der DNS-Adresse für den Transaktionsknotenendpunkt verwendet. Beispiel: `newnode-myblockchainmember.blockchain.azure.com`. Der Knotenname kann nicht mehr geändert werden, nachdem der Knoten erstellt wurde. |
    | Kennwort | Legen Sie ein sicheres Kennwort fest. Das Kennwort verwenden Sie, um auf den Transaktionsknotenendpunkt mit Standardauthentifizierung zuzugreifen.

1. Klicken Sie auf **Erstellen** .

    Die Bereitstellung eines neuen Transaktionsknotens beansprucht etwa 10 Minuten. Für weitere Transaktionsknoten fallen Kosten an. Weitere Informationen finden Sie unter [Azure Blockchain-Preise](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Endpunkte

Transaktionsknoten verfügen über einen eindeutigen DNS-Namen und öffentliche Endpunkte.

So zeigen Sie die Details zum Endpunkt eines Transaktionsknotens an:

1. Navigieren Sie im Azure-Portal zu einem Ihrer Transaktionsknoten Ihres Azure Blockchain-Mitglieds, und klicken Sie auf **Übersicht** .

    ![Screenshot: Übersicht über die Transaktionsknoten für ein Blockchainmitglied](./media/configure-transaction-nodes/endpoints.png)

Transaktionsknotenendpunkte sind sicher und erfordern Authentifizierung. Sie können eine Verbindung mit einem Transaktionsendpunkt mithilfe der Azure AD-Authentifizierung, HTTPS-Standardauthentifizierung und mithilfe eines Zugriffsschlüssels über HTTPS oder eines Websockets über TLS herstellen.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory-Zugriffssteuerung

Azure Blockchain-Transaktionsknotenendpunkte unterstützen die Azure Active Directory-Authentifizierung. Sie können Azure AD-Benutzern, -Gruppen und Dienstprinzipalen den Zugriff auf Ihren Endpunkt gewähren.

So gewähren Sie die Azure AD-Zugriffssteuerung Ihres Endpunkts:

1. Navigieren Sie im Azure-Portal zu Ihrem Azure Blockchain-Mitglied, und klicken Sie auf **Transaktionsknoten > Zugriffssteuerung (IAM) > Hinzufügen > Rollenzuweisung hinzufügen** .
1. Erstellen Sie eine neue Rollenzuweisung für einen Benutzer, eine Gruppe oder ein Dienstprinzipal (Anwendungsrollen).

    ![IAM-Rolle hinzufügen](./media/configure-transaction-nodes/add-role.png)

    | Einstellung | Aktion |
    |---------|-------------|
    | Role | Wählen Sie die Rolle **Besitzer** , **Mitwirkender** oder **Leser** aus.
    | Zugriff zuweisen zu | Wählen Sie **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus.
    | Select | Suchen Sie den Benutzer, die Gruppe oder den Dienstprinzipal, den bzw. die Sie hinzufügen möchten.

1. Klicken Sie auf **Speichern** , um die Rollenzuweisung hinzuzufügen.

Weitere Informationen zur Azure AD-Zugriffssteuerung finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).

Informationen zum Herstellen einer Verbindung mithilfe der Azure AD-Authentifizierung finden Sie unter [How to configure Azure Active Directory access (Konfigurieren des Azure Active Directory-Zugriffs)](configure-aad.md).

### <a name="basic-authentication"></a>Standardauthentifizierung

Für die HTTPS-Standardauthentifizierung werden die Anmeldeinformationen „Benutzername“ und „Kennwort“ im HTTPS-Header der Anforderung an den Endpunkt übergeben.

Sie können die Informationen zum Standardauthentifizierungsendpunkt eines Transaktionsknotens im Azure-Portal anzeigen. Navigieren Sie zu einem Ihrer Transaktionsknoten Ihres Azure Blockchain-Mitglieds, und klicken Sie in den Einstellungen auf **Standardauthentifizierung** .

![Standardauthentifizierung](./media/configure-transaction-nodes/basic.png)

Der Benutzername entspricht dem Namen Ihres Knotens und kann nicht geändert werden.

Ersetzen Sie \<password\> durch das Kennwort, das bei der Bereitstellung des Knotens festgelegt wurde, um die URL zu verwenden. Sie können das Kennwort aktualisieren, indem Sie auf **Kennwort zurücksetzen** klicken.

### <a name="access-keys"></a>Zugriffsschlüssel

Bei der Authentifizierung mit dem Zugriffsschlüssel ist der Zugriffsschlüssel in der Endpunkt-URL enthalten. Wenn der Transaktionsknoten bereitgestellt wird, werden zwei Zugriffsschlüssel generiert. Beide Zugriffsschlüssel können für die Authentifizierung verwendet werden. Wenn Sie über zwei Schlüssel verfügen, können Sie die Schlüssel ändern und rotieren.

Sie können die Informationen zum Zugriffsschlüssel eines Transaktionsknotens anzeigen und die Endpunktadressen kopieren, die die Zugriffsschlüssel enthalten. Navigieren Sie zu einem Ihrer Transaktionsknoten Ihres Azure Blockchain-Mitglieds, und klicken Sie in den Einstellungen auf **Zugriffsschlüssel** .

### <a name="firewall-rules"></a>Firewallregeln

Mithilfe von Firewallregeln können Sie die IP-Adressen einschränken, die versuchen können, sich für Ihren Transaktionsknoten zu authentifizieren.  Wenn keine Firewallregeln für Ihren Transaktionsknoten konfiguriert sind, kann niemand auf diesen zugreifen.  

Navigieren Sie zu einem Ihrer Transaktionsknoten Ihres Azure Blockchain-Mitglieds, und klicken Sie in den Einstellungen auf **Firewallregeln** , um die Firewallregeln eines Transaktionsknotens anzuzeigen.

Sie können Firewallregeln hinzufügen, indem Sie einen Regelnamen, eine Start-IP-Adresse und eine End-IP-Adresse in das Raster **Firewallregeln** eingeben.

![Firewallregeln](./media/configure-transaction-nodes/firewall-rules.png)

So aktivieren Sie Firewallregeln:

* **Einzelne IP-Adresse:** Konfigurieren Sie die gleiche IP-Adresse für die Start- und End-IP-Adressen.
* **IP-Adressbereich:** Konfigurieren Sie den Start- und End-IP-Adressbereich. Ein Bereich, der bei 10.221.34.0 beginnt und bei 10.221.34.255 endet, würde beispielsweise das gesamte Subnetz 10.221.34.xxx aktivieren.
* **Alle IP-Adressen zulassen:** Legen Sie 0.0.0.0 als Start-IP-Adresse und 255.255.255.255 als End-IP-Adresse fest.

## <a name="connection-strings"></a>Verbindungszeichenfolgen

Die Syntax für die Verbindungszeichenfolge Ihres Transaktionsknotens wird für die Standardauthentifizierung oder bei der Verwendung von Zugriffsschlüsseln bereitgestellt. Verbindungszeichenfolgen werden einschließlich der Zugriffsschlüssel für HTTPS und WebSockets bereitgestellt.

Sie können die Verbindungszeichenfolgen eines Transaktionsknotens anzeigen und die Endpunktadressen kopieren. Navigieren Sie zu einem Ihrer Transaktionsknoten Ihres Azure Blockchain-Mitglieds, und klicken Sie in den Einstellungen auf **Verbindungszeichenfolgen** .

![Verbindungszeichenfolgen](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Beispielcode

Der Beispielcode wird bereitgestellt, sodass Sie schnell eine Verbindung über Web3, Nethereum, Web3js und Truffle mit Ihrem Transaktionsknoten herstellen können.

Sie können den Beispielcode zum Herstellen einer Verbindung eines Transaktionsknotens anzeigen und kopieren, um ihn mit beliebten Entwicklertools zu verwenden. Navigieren Sie zu einem Ihrer Transaktionsknoten Ihres Azure Blockchain-Mitglieds, und klicken Sie in den Einstellungen auf **Beispielcode** .

Klicken Sie auf die Registerkarte „Web3“, „Nethereum“, „Truffle“ oder „Web3j“, um den Beispielcode anzuzeigen, den Sie verwenden möchten.

![Beispielcode](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Manage Azure Blockchain Service with Azure CLI (Verwalten des Azure Blockchain-Diensts mithilfe der Azure CLI)](manage-cli.md)
