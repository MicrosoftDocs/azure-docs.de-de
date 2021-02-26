---
title: 'Tutorial: Verwenden der Key Vault-Verweise von Azure App Configuration in einer Java Spring Boot-App | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Key Vault-Verweise von Azure App Configuration in einer Java Spring Boot-App verwenden.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 04d9c7a343570349851a206fd69fdda822f790a4
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981475"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Tutorial: Verwenden von Key Vault-Verweisen in einer Java Spring-App

In diesem Tutorial erfahren Sie, wie Sie den Azure App Configuration-Dienst zusammen mit Azure Key Vault verwenden. Die Dienste App Configuration und Key Vault ergänzen sich gegenseitig und werden bei den meisten Anwendungsbereitstellungen gemeinsam eingesetzt.

App Configuration unterstützt Sie beim parallelen Verwenden der Dienste, indem Schlüssel erstellt werden, mit denen auf in Key Vault gespeicherte Werte verwiesen wird. Wenn mit App Configuration Schlüssel dieser Art erstellt werden, werden nicht die eigentlichen Werte, sondern die URIs der Key Vault-Werte gespeichert.

Ihre Anwendung nutzt den App Configuration-Clientanbieter, um Key Vault-Verweise abzurufen, wie dies auch für alle anderen Schlüssel der Fall ist, die in App Configuration gespeichert sind. In diesem Fall sind die in App Configuration gespeicherten Werte URIs, mit denen auf die Werte in Key Vault verwiesen wird. Es sind keine Key Vault-Werte oder -Anmeldeinformationen. Da der Clientanbieter die Schlüssel als Key Vault-Verweise erkennt, wird Key Vault zum Abrufen der zugehörigen Werte verwendet.

Ihre Anwendung ist für die ordnungsgemäße Authentifizierung sowohl bei App Configuration als auch bei Key Vault zuständig. Die beiden Dienste kommunizieren nicht direkt.

In diesem Tutorial wird veranschaulicht, wie Sie Key Vault-Verweise in Ihrem Code implementieren. Dies baut auf der Web-App auf, die in den Schnellstartanleitungen vorgestellt wurde. Arbeiten Sie zunächst [Schnellstart: Erstellen einer Java Spring-App mit Azure App Configuration](./quickstart-java-spring-app.md) durch, bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist beispielsweise ein plattformübergreifender Code-Editor, der für die Betriebssysteme Windows, macOS und Linux verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines App Configuration-Schlüssels, der auf einen in Key Vault gespeicherten Wert verweist
> * Zugreifen auf den Wert dieses Schlüssels über eine Java Spring-Webanwendung

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
* Ein unterstütztes [Java Development Kit (JDK)](/java/azure/jdk) mit Version 8.
* [Apache Maven](https://maven.apache.org/download.cgi) Version 3.0 oder höher

## <a name="create-a-vault"></a>Erstellen eines Tresors

1. Wählen Sie oben links im Azure-Portal die Option **Ressource erstellen** aus:

    ![Screenshot: Option „Ressource erstellen“ im Azure-Portal](./media/quickstarts/search-services.png)
1. Geben Sie **Key Vault** in das Suchfeld ein.
1. Wählen Sie in der Ergebnisliste links **Key Vault** aus.
1. Wählen Sie unter **Schlüsseltresore** die Option **Hinzufügen** aus.
1. Geben Sie auf der rechten Seite unter **Schlüsseltresor erstellen** die folgenden Informationen ein:
    * Wählen Sie die Option **Abonnement** aus, um ein Abonnement auszuwählen.
    * Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
    * Unter **Schlüsseltresorname** müssen Sie einen eindeutigen Namen eingeben. Geben Sie für dieses Tutorial **Contoso-vault2** ein.
    * Wählen Sie in der Dropdownliste **Region** einen Ort aus.
1. Übernehmen Sie für die anderen Optionen unter **Schlüsseltresor erstellen** die Standardwerte.
1. Klicken Sie auf **Erstellen**.

An diesem Punkt ist nur Ihr Azure-Konto für den Zugriff auf diesen neuen Tresor autorisiert.

![Screenshot: Schlüsseltresor](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault

Zum Hinzufügen eines Geheimnisses zum Tresor müssen Sie lediglich einige zusätzliche Schritte ausführen. In diesem Fall fügen Sie eine Nachricht hinzu, die Sie verwenden können, um den Key Vault-Abruf zu testen. Die Nachricht hat den Namen **Nachricht**, und Sie speichern darin den Wert „Hallo von Key Vault“.

1. Wählen Sie auf den Key Vault-Eigenschaftenseiten die Option **Geheimnisse** aus.
1. Wählen Sie die Option **Generieren/Importieren** aus.
1. Geben Sie im Bereich **Geheimnis erstellen** die folgenden Werte ein:
    * **Uploadoptionen**: Geben Sie **Manuell** ein.
    * **Name**: Geben Sie **Nachricht** ein.
    * **Value**: Geben Sie **Hallo von Key Vault** ein.
1. Übernehmen Sie für die anderen Eigenschaften unter **Geheimnis erstellen** die Standardwerte.
1. Klicken Sie auf **Erstellen**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Hinzufügen eines Key Vault-Verweises zu App Configuration

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie die Option **Alle Ressourcen** und dann die Instanz des App Configuration-Speichers aus, die Sie in der Schnellstartanleitung erstellt haben.

1. Wählen Sie **Konfigurations-Explorer** aus.

1. Wählen Sie **+ Erstellen** > **Schlüsseltresorverweis** aus, und geben Sie dann die folgenden Werte an:
    * **Key**: Wählen Sie **/application/config.keyvaultmessage** aus.
    * **Bezeichnung:** Lassen Sie diesen Wert leer.
    * **Abonnement**, **Ressourcengruppe** und **Schlüsseltresor**: Geben Sie die Werte ein, die den Werten des im vorherigen Abschnitt erstellten Schlüsseltresors entsprechen.
    * **Geheimnis**: Wählen Sie das Geheimnis mit dem Namen **Nachricht** aus, das Sie im vorherigen Abschnitt erstellt haben.

## <a name="connect-to-key-vault"></a>Verbindung mit Key Vault herstellen

1. In diesem Tutorial verwenden Sie einen Dienstprinzipal für die Authentifizierung bei Key Vault. Verwenden Sie zum Erstellen dieses Dienstprinzipals den Azure CLI-Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac):

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Daraufhin wird eine Reihe von Schlüssel-Wert-Paaren zurückgegeben:

    ```json
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Führen Sie den folgenden Befehl aus, um dem Dienstprinzipal den Zugriff auf Ihren Schlüsseltresor zu erlauben:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Führen Sie den folgenden Befehl aus, um Ihre Objekt-ID abzurufen, und fügen Sie sie anschließend zu App Configuration hinzu.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Erstellen Sie die Umgebungsvariablen **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** und **AZURE_TENANT_ID**. Verwenden Sie dabei die Werte für den Dienstprinzipal, die im vorherigen Schritt angezeigt wurden. Führen Sie an der Befehlszeile die folgenden Befehle aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Diese Key Vault-Anmeldeinformationen werden nur innerhalb Ihrer Anwendung verwendet.  Ihre Anwendung authentifiziert sich mit diesen Anmeldeinformationen direkt (ohne Beteiligung des App Configuration-Diensts) bei Key Vault.  Key Vault sorgt für die Authentifizierung Ihrer Anwendung und Ihres App Configuration-Diensts, ohne Schlüssel weiterzugeben oder offenzulegen.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualisieren des Codes für die Verwendung eines Key Vault-Verweises

1. Erstellen Sie eine Umgebungsvariable namens **APP_CONFIGURATION_ENDPOINT**. Legen Sie ihren Wert auf den Endpunkt Ihres App Configuration-Speichers fest. Den Endpunkt finden Sie im Azure-Portal auf dem Blatt **Zugriffsschlüssel**. Starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird. 


1. Öffnen Sie die Datei *bootstrap.properties* im Ordner *Ressourcen*. Aktualisieren Sie diese Datei, sodass der Wert **APP_CONFIGURATION_ENDPOINT** verwendet wird. Entfernen Sie alle Verweise auf eine Verbindungszeichenfolge in dieser Datei. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Öffnen Sie *MessageProperties.java*. Fügen Sie eine neue Variable mit dem Namen *keyVaultMessage* hinzu:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Öffnen Sie *HelloController.java*. Aktualisieren Sie die *getMessage*-Methode, um die von Key Vault abgerufene Nachricht einzuschließen.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Erstellen Sie eine neue Datei mit dem Namen *AzureCredentials.java*, und fügen Sie den folgenden Code hinzu:

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Erstellen Sie eine neue Datei mit dem Namen *AppConfiguration.java*. Fügen Sie den folgenden Code hinzu:

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Erstellen Sie im Verzeichnis „META-INF“ Ihrer Ressourcen eine neue Datei namens *spring.factories*, und fügen Sie den folgenden Code hinzu:

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Erstellen Sie Ihre Spring Boot-Anwendung mit Maven, und führen Sie sie aus. Beispiel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Nachdem Ihre Anwendung ausgeführt wird, testen Sie sie mit *cURL*. Beispiel:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Es wird die Nachricht angezeigt, die Sie im App Configuration-Speicher eingegeben haben. Darüber hinaus wird die Nachricht angezeigt, die Sie in Key Vault eingegeben haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen App Configuration-Schlüssel erstellt, mit dem auf einen in Key Vault gespeicherten Wert verwiesen wird. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Featureflags in Ihrer Java Spring-Anwendung verwendet werden.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./quickstart-feature-flag-spring-boot.md)
