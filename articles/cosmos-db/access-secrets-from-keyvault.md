---
title: Verwenden von Key Vault zum Speichern von und Zugreifen auf Azure Cosmos DB-Schlüssel
description: Verwenden Sie Azure Key Vault zum Speichern von und Zugreifen auf Azure Cosmos DB-Verbindungszeichenfolgen, -Schlüssel und -Endpunkte.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 157ccd284c25cb5c7275aa942823ade2a40795cc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239851"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Sichern von Azure Cosmos-Schlüsseln mit Azure Key Vault 

Wenn Sie Azure Cosmos DB für Ihre Anwendungen verwenden, können Sie über den Endpunkt und den Schlüssel in der Konfigurationsdatei der Anwendung auf die Datenbank, Sammlungen und Dokumente zugreifen.  Es ist jedoch nicht sicher, Schlüssel und die URL direkt in den Anwendungscode zu integrieren, da dieser im Klartextformat für alle Benutzer verfügbar ist. Sie müssen sicherstellen, dass der Endpunkt und die Schlüssel über einen sicheren Mechanismus verfügbar sind. An dieser Stelle unterstützt Sie Azure Key Vault Sie bei der sicheren Speicherung und Verwaltung von Anwendungsgeheimnissen.

Die folgenden Schritte sind erforderlich, Azure Cosmos DB-Zugriffsschlüssel in Key Vault zu speichern und daraus auszulesen:

* Erstellen eines Schlüsseltresors  
* Hinzufügen von Azure Cosmos DB-Zugriffsschlüsseln zu Key Vault  
* Erstellen einer Azure-Webanwendung  
* Registrieren der Anwendung und Gewähren von Berechtigungen zum Lesen von Key Vault  


## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.  
2. Wählen Sie **Ressource erstellen > Sicherheit > Key Vault** aus.  
3. Geben Sie im Abschnitt **Schlüsseltresor erstellen** folgende Informationen ein:  
   * **Name:** Geben Sie einen eindeutigen Namen für Ihren Schlüsseltresor an.  
   * **Abonnement:** Wählen Sie das zu verwendende Abonnement aus.  
   * Klicken Sie unter **Ressourcengruppe** auf **Neu erstellen**, und geben Sie einen Ressourcengruppennamen ein.  
   * Wählen Sie im Pulldownmenü „Standort“ einen Standort aus.  
   * Behalten Sie bei den anderen Optionen die Standardeinstellungen bei.  
4. Klicken Sie nach der Angabe der obigen Informationen auf **Erstellen**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Hinzufügen von Azure Cosmos DB-Zugriffsschlüsseln zu Key Vault
1. Navigieren Sie zum im vorherigen Schritt erstellten Key Vault, und öffnen Sie die Registerkarte **Geheimnisse**.  
2. Wählen Sie **+ Generieren/Importieren** aus. 

   * Wählen Sie als **Uploadoption** **Manuell** aus.
   * Geben Sie einen **Namen** für Ihr Geheimnis an.
   * Geben Sie die Verbindungszeichenfolge Ihres Cosmos DB-Kontos im Feld **Wert** ein. Wählen Sie **Erstellen** aus.

   ![Erstellen eines Geheimnisses](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Öffnen Sie das Geheimnis nach dem Erstellen und kopieren Sie die **Geheimnis-ID, die im folgenden Format vorliegt. Sie benötigen diese ID im nächsten Schritt. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Erstellen einer Azure-Webanwendung

1. Erstellen Sie eine Azure-Webanwendung, oder laden Sie die Anwendung aus dem [GitHub-Repository](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo) herunter. Es ist eine einfache MVC-Anwendung.  

2. Entzippen Sie die heruntergeladene Anwendung, und öffnen Sie die Datei **HomeController.cs**. Aktualisieren Sie die Geheimnisse-ID in der folgenden Zeile:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Speichern** Sie die Datei und **Erstellen** Sie die Lösung.  
4. Stellen Sie die Anwendung anschließend in Azure bereit. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen**. Erstellen Sie ein neues Anwendungsdienstprofil (benennen Sie die Anwendung mit „WebAppKeyVault1“), und wählen Sie **Veröffentlichen**.   

5. Wenn die Anwendung bereitgestellt ist, navigieren Sie im Azure-Portal zur von Ihnen bereitgestellten Webanwendung, und aktivieren Sie die **verwaltete Dienstidentität** dieser Anwendung.  

   ![Verwaltete Dienstidentität](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Wenn Sie die Anwendung jetzt ausführen, sehen Sie den folgenden Fehler, da Sie für diese Anwendung in Key Vault keinerlei Berechtigungen gewährt haben.

![Anwendung ohne Zugriff bereitgestellt](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrieren der Anwendung und Gewähren von Berechtigungen zum Lesen von Key Vault

In diesem Abschnitt registrieren Sie die Anwendung im Azure Active Directory und gewähren der Anwendung die Berechtigung, den Key Vault auszulesen. 

1. Navigieren Sie zum Azure-Portal, und öffnen Sie den **Key Vault**, den Sie im vorherigen Abschnitt erstellt haben.  

2. Öffnen Sie **Zugriffsrichtlinien**, wählen Sie **+Neue hinzufügen**, suchen Sie die von Ihnen bereitgestellte Webanwendung, wählen Sie die Berechtigungen aus, und wählen Sie **OK**.  

   ![Zugriffsrichtlinie hinzufügen](./media/access-secrets-from-keyvault/add-access-policy.png)

Wenn Sie die Anwendung jetzt ausführen, können Sie das Geheimnis aus Key Vault aus lesen.

![Anwendung mit Geheimnis bereitgestellt](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Auf ähnliche Weise können Sie einen Benutzer für den Zugriff auf den Key Vault hinzufügen. Sie müssen sich selbst zum Schlüsseltresor hinzufügen, indem Sie **Zugriffsrichtlinien** auswählen und alle Berechtigungen gewähren, die Sie zum Ausführen der Anwendung über Visual Studio benötigen. Wenn diese Anwendung auf dem Desktop ausgeführt wird, nimmt sie Ihre Identität an.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Konfigurieren einer Firewall für Azure Cosmos DB finden Sie im Artikel [Firewallunterstützung](firewall-support.md).
* Weitere Informationen zum Konfigurieren eines VNET-Dienstendpunkts finden Sie im Artikel [Sicherer Zugriff auf ein Azure Cosmos DB-Konto durch Verwenden eines Azure Virtual Network-Dienstendpunkts](vnet-service-endpoint.md).
