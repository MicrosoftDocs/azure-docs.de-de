---
title: Empfang einer E-Mail bei geändertem Key Vault-Status des Geheimnisses
description: Leitfaden zur Verwendung von Logic Apps zum Reagieren auf geänderte Key Vault-Geheimnisse
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 9c522d870a25b3df34ab6a0cf1c1e944a6462685
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013988"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Verwenden von Logic Apps zum Empfangen einer E-Mail bei Statusänderungen von Key Vault-Geheimnissen

In diesem Leitfaden wird beschrieben, wie Sie mit [Azure Logic Apps](../../logic-apps/index.yml) auf Azure Key Vault-Ereignisse reagieren, die über [Azure Event Grid](../../event-grid/index.yml) empfangen werden. Am Ende verfügen Sie über eine Azure-Logik-App, für die das Senden einer E-Mail-Benachrichtigung bei jeder Erstellung eines Geheimnisses in Azure Key Vault eingerichtet ist.

Eine Übersicht über die Integration von Azure Key Vault/Azure Event Grid finden Sie unter [Überwachen von Key Vault mit Azure Event Grid (Vorschau)](event-grid-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein E-Mail-Konto von einem beliebigen von Azure Logic Apps unterstützten E-Mail-Anbieter (z. B. Office 365 Outlook). Mit diesem E-Mail-Konto werden die Ereignisbenachrichtigungen gesendet. Eine vollständige Liste der unterstützten Logic App-Connectors finden Sie in der [Übersicht über Connectors](/connectors).
- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Ein Schlüsseltresor in Ihrem Azure-Abonnement. Sie können ganz schnell einen neuen Schlüsseltresor erstellen. Eine entsprechende Anleitung finden Sie unter [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe der Azure CLI](../secrets/quick-create-cli.md).
- Eine registrierte Event Grid-Instanz als Ressourcenanbieter (siehe [Azure-Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md))

## <a name="create-a-logic-app-via-event-grid"></a>Erstellen einer Logik-App per Event Grid

Erstellen Sie zunächst mit dem Event Grid-Handler eine Logik-App, und abonnieren Sie die Azure Key Vault-Ereignisse vom Typ „SecretNewVersionCreated“.

Gehen Sie wie folgt vor, um ein Azure Event Grid-Abonnement zu erstellen:

1. Navigieren Sie im Azure-Portal zu Ihrer Key Vault-Instanz, wählen Sie **Ereignisse > Erste Schritte** aus, und klicken Sie auf **Logik-Apps**.

    
    ![Key Vault: Seite „Ereignisse“](../media/eventgrid-logicapps-kvsubs.png)

1. Überprüfen Sie unter **Logik-App-Designer** die Verbindung, und klicken Sie auf **Weiter**. 
 
    ![Logik-App-Designer: Verbindung](../media/eventgrid-logicappdesigner1.png)

1. Gehen Sie im Fenster **Bei Eintritt eines Ressourcenereignisses** wie folgt vor:
    - Übernehmen Sie für **Abonnement** und **Ressourcenname** die Standardeinstellungen.
    - Wählen Sie unter **Ressourcentyp** die Option **Microsoft.KeyVault.vaults** aus.
    - Wählen Sie unter **Ereignistypelement: 1** die Option **Microsoft.KeyVault.SecretNewVersionCreated** aus.

    ![Logik-App-Designer: Ereignishandler](../media/eventgrid-logicappdesigner2.png)

1. Wählen Sie **+ Neuer Schritt** aus. Daraufhin wird ein Fenster zum Auswählen einer Aktion geöffnet.
1. Suchen Sie nach **E-Mail**. Suchen Sie abhängig von Ihrem E-Mail-Anbieter nach dem entsprechenden Connector, und wählen Sie diesen aus. In diesem Tutorial wird **Office 365 Outlook** verwendet. Die Schritte für andere E-Mail-Anbieter sind ähnlich.
1. Wählen Sie die Aktion **E-Mail senden (V2)** aus.

   ![Logik-App-Designer: Senden einer E-Mail](../media/eventgrid-logicappdesigner3.png)

1. Erstellen Sie Ihre E-Mail-Vorlage:
    - **An:** Geben Sie die E-Mail-Adresse an, die die E-Mail-Benachrichtigungen empfangen soll. Verwenden Sie für dieses Tutorial ein E-Mail-Konto, auf das Sie zu Testzwecken zugreifen können.
    - **Betreff** und **Text**: Schreiben Sie den Text für Ihre E-Mail. Wählen Sie JSON-Eigenschaften aus dem Auswahlwerkzeug aus, um dynamischen Inhalt basierend auf Ereignisdaten einzuschließen. Sie können die Daten des Ereignisses mit `@{triggerBody()?['Data']}` abrufen.

    Hier ist ein Beispiel für das Aussehen Ihrer E-Mail-Vorlage angegeben:

    ![Logik-App-Designer: E-Mail-Text](../media/eventgrid-logicappdesigner4.png)

8. Klicken Sie auf **Speichern unter**.
9. Geben Sie unter **Name** einen Namen für die neue Logik-App ein, und klicken Sie auf **Erstellen**.
    
    ![Logik-App-Designer: Erstellen](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testen und Überprüfen

1.  Navigieren Sie im Azure-Portal zu Ihrer Key Vault-Instanz, und wählen Sie **Ereignisse > Ereignisabonnements** aus.  Überprüfen Sie, ob ein neues Abonnement erstellt wurde.
    
    ![Logik-App-Designer: Testen und Überprüfen](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Navigieren Sie zu Ihrer Key Vault-Instanz, und wählen Sie **Geheimnisse** und dann **+ Generieren/Importieren** aus. Erstellen Sie zu Testzwecken ein neues Geheimnis, geben Sie dem Schlüssel einen Namen, und übernehmen Sie für die restlichen Parameter die Standardeinstellungen.

    ![Key Vault: Geheimnis erstellen](../media/eventgrid-logicapps-kv-create-secret.png)

1. Geben Sie im Fenster **Geheimnis erstellen** einen beliebigen Namen und Wert ein, und wählen Sie **Erstellen** aus.

Nachdem das Geheimnis erstellt wurde, wird eine E-Mail an die konfigurierten Adressen gesendet.

## <a name="next-steps"></a>Nächste Schritte

- Übersicht: [Überwachen von Key Vault mit Azure Event Grid](event-grid-overview.md)
- Gewusst wie: [Gewusst wie: Empfangen von und Reagieren auf Key Vault-Benachrichtigungen mit Azure Event Grid (Vorschau)](event-grid-tutorial.md)
- [Azure Event Grid-Ereignisschema für Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Informieren Sie sich ausführlicher über [Azure Event Grid](../../event-grid/index.yml).
- Weitere Informationen zum [Logic Apps-Feature von Azure App Service](../../logic-apps/index.yml)