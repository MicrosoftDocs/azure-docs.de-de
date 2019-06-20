---
title: Konfigurieren des Azure Active Directory-Zugriffs
description: Konfigurieren des Azure Blockchain-Diensts mit Azure Active Directory-Zugriff
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027658"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Konfigurieren des Azure Active Directory-Zugriffs

In diesem Artikel erfahren Sie, wie Sie mit Hilfe von Azure Active Directory (Azure AD) Benutzer-, Gruppen- oder Anwendungs-IDs Zugriff gewähren und eine Verbindung zu Azure Blockchain-Dienstknoten herstellen.

Azure AD bietet eine cloudbasierte Identitätsverwaltung und ermöglicht Ihnen das Verwenden einer einzigen Identität für ein gesamtes Unternehmen und den Zugriff auf Aufwendungen in Azure. Der Azure Blockchain-Dienst ist in Azure AD integriert und bietet verschiedene Vorteile, wie ID-Verbund, das einmalige Anmelden und die mehrstufige Authentifizierung.

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen eines Blockchainmitglieds über das Azure-Portal](create-member.md)

## <a name="grant-access"></a>Gewähren von Zugriff

Sie können den Zugriff sowohl auf der Mitgliederebene als auch auf der Knotenebene gewähren. Die Gewährung von Zugriffsrechten auf Mitgliederebene ermöglicht wiederum den Zugriff auf alle Knoten unter dem Mitglied.

### <a name="grant-member-level-access"></a>Gewähren von Zugriff auf Mitgliederebene

So gewähren Sie Zugriff auf Mitgliederebene.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Zugriffssteuerung (IAM) > Hinzufügen > Rollenzuweisung hinzufügen**.
1. Wählen Sie die Rolle **Knotenzugriff für Blockchainmitglied (Vorschauversion)** aus, und fügen Sie das Azure AD-ID-Objekt hinzu, auf das Sie Zugriff gewähren möchten. Ein Azure AD-ID-Objekt kann sein:

    | Ein Azure AD-Objekt | Beispiel |
    |-----------------|---------|
    | Ein Azure AD-Benutzer   | `frank@contoso.onmicrosoft.com` |
    | Eine Azure AD-Gruppe  | `sales@contoso.onmicrosoft.com` |
    | Anwendungs-ID  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Rollenzuweisung hinzufügen](./media/configure-aad/add-role-assignment.png)

1. Wählen Sie **Speichern** aus.

### <a name="grant-node-level-access"></a>Gewähren von Knotenzugriff

1. Sie können den Zugriff auf Knotenebene gewähren, indem Sie zur Knotensicherheit navigieren und auf den Knotennamen klicken, auf en Sie den Zugriff gewähren möchten.
1. Wählen Sie die Rolle „Knotenzugriff für Blockchainmitglied (Vorschauversion)“ aus, und fügen Sie das Azure AD-ID-Objekt hinzu, auf das Sie Zugriff gewähren möchten. 

## <a name="connect-using-azure-blockchain-connector"></a>Herstellen einer Verbindung mit dem Azure Blockchain-Connector

Laden Sie den [Azure Blockchain-Connector von GitHub](https://github.com/Microsoft/azure-blockchain-connector/) herunter oder klonen Sie ihn.

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Folgen Sie dann den Anweisungen im Abschnitt „Schnellstart“ in der **readme**-Daten, um den Connektor aus dem Quellcode zu erstellen.

### <a name="connect-using-an-azure-ad-user-account"></a>Herstellen einer Verbindung mit einem Azure AD-Konto

1. Führen Sie den folgenden Befehl aus, um die Authentifizierung mit einem Azure AD-Benutzerkonto durchzuführen. Ersetzen Sie \<myAADDirectory\> durch die Azure AD-Domäne. Beispiel: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD fordert Sie zur Eingabe von Anmeldeinformationen auf.
1. Melden Sie sich mit Ihrem Benutzernamen und Kennwort an.
1. Nach erfolgreicher Authentifizierung verbindet sich Ihr lokaler Proxy mit Ihrem Blockchainknoten. Sie können nun Ihren Geth-Client mit dem lokalen Endpunkt verbinden.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Herstellen einer Verbindung mit einer Anwendungs-ID

Viele Anwendungen authentifizieren sich bei Azure AD mithilfe einer Anwendungs-ID anstelle eines Azure AD-Benutzerkontos.

Um sich mit Ihrem Knoten über eine Anwendungs-ID zu verbinden, ersetzen Sie **aadauthcode** durch **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| tenant-id | Azure AD-Domäne, z.B. `yourdomain.onmicrosoft.com`
| client-id | Client-ID der in Azure AD registrierten Anwendung
| client-secret | Geheimer Clientschlüssel der in Azure AD registrierten Anwendung

Weitere Informationen zum Registrieren einer Anwendungs in Azure AD finden Sie unter [Vorgehensweise: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Herstellen einer Verbindung mit einem mobilen Gerät oder einen Textbrowser

Für ein mobiles Gerät oder einen textbasierten Browser, bei dem die Popupanzeige der Azure AD-Authentifizierung nicht möglich ist, generiert Azure AD einen Einmalpasscode. Sie können den Passcode kopieren und mit der Azure AD-Authentifizierung in einer anderen Umgebung fortfahren.

Um den Passcode zu generieren, ersetzen Sie **aadauthcode** durch **aaddevice**. Ersetzen Sie \<myAADDirectory\> durch die Azure AD-Domäne. Beispiel: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Datensicherheit im Azure Blockchain-Dienst finden Sie unter:

> [!div class="nextstepaction"]
> [Azure Blockchain-Dienstsicherheit](data-security.md)