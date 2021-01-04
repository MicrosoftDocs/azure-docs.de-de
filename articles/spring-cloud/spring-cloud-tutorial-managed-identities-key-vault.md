---
title: 'Tutorial: Verwenden einer verwalteten Identität, um eine Verbindung zwischen Key Vault und einer Azure Spring Cloud-App herzustellen'
description: Einrichten einer verwalteten Identität, um eine Verbindung zwischen Key Vault und einer Azure Spring Cloud-App herzustellen
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fc44dd6cf91d687f47afadf1c3378956d838bc9d
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579503"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Tutorial: Verwenden einer verwalteten Identität, um eine Verbindung zwischen Key Vault und einer Azure Spring Cloud-App herzustellen

**Dieser Artikel gilt für:** ✔️ Java

Dieser Artikel zeigt, wie Sie eine verwaltete Identität für eine Azure Spring Cloud-App erstellen und damit auf Azure Key Vault zugreifen können.

Azure Key Vault ermöglicht die sichere Speicherung und präzise Steuerung des Zugriffs auf Token, Kennwörter, Zertifikate, API-Schlüssel und andere Geheimnisse für Ihre App. Sie können eine verwaltete Identität in Azure Active Directory (AAD) erstellen und sich bei jedem Dienst authentifizieren, der AAD-Authentifizierung unterstützt (einschließlich Key Vault), ohne dass Sie Anmeldeinformationen in Ihrem Code anzeigen müssen.

## <a name="prerequisites"></a>Voraussetzungen

* [Registrierung für ein Azure-Abonnement](https://azure.microsoft.com/free/)
* [Installation der Azure CLI, Version 2.0.67 oder höher](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Installation von Maven 3.0 oder höher](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie eine Ressourcengruppe, die sowohl Key Vault als auch Spring Cloud enthält, mit dem Befehl [az group create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Einrichten Ihres Key Vault
Verwenden Sie den Befehl [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest&preserve-view=true#az-keyvault-create), um einen Key Vault zu erstellen:

> [!Important]
> Jeder Schlüsseltresor muss einen eindeutigen Namen haben. Ersetzen Sie in den folgenden Beispielen „<your-keyvault-name>“ durch den Namen Ihres Key Vaults.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Notieren Sie sich den zurückgegebenen `vaultUri`. Dieser hat das Format „https://<your-keyvault-name>.vault.azure.net“. Dieser Wert wird im folgenden Schritt verwendet.

Nun können Sie mithilfe des Befehls [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest&preserve-view=true#az-keyvault-secret-set) ein Geheimnis in Ihrem Key Vault platzieren:

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Erstellen eines Azure Spring Cloud-Diensts und einer -App
Erstellen Sie nach der Installation der entsprechenden Erweiterung eine Azure Spring Cloud-Instanz mit dem Azure CLI-Befehl `az spring-cloud create`. 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
Im folgenden Beispiel wird eine App namens `springapp` mit einer vom System zugewiesenen verwalteten Identität erstellt. Dies wird vom Parameter `--assign-identity` angefordert.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --is-public true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Notieren Sie sich die zurückgegebene `url`. Diese weist das Format „https://<your-app-name>.azuremicroservices.io“ auf. Dieser Wert wird im folgenden Schritt verwendet.


## <a name="grant-your-app-access-to-key-vault"></a>Gewähren des Zugriffs auf Key Vault für Ihre App
Verwenden Sie `az keyvault set-policy`, um ordnungsgemäßen Zugriff in Key Vault für Ihre App zu gewähren.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Verwenden Sie `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}`, um den Zugriff für Ihre App zu entfernen, nachdem die systemseitig zugewiesene verwaltete Identität deaktiviert wurde.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Erstellen einer Spring Boot-Beispiel-App mit Spring Boot Starter
Diese App besitzt Zugriff, um Geheimnisse aus Azure Key Vault abzurufen. Verwenden Sie die Starter-App: [Spring Boot StarterfürAzure Key Vault-Geheimnisse](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  Azure Key Vault wird als Instanz von **PropertySource** von Spring hinzugefügt.  Auf die in Azure Key Vault gespeicherten Geheimnisse kann bequem zugegriffen werden, und sie können wie jede externe Konfigurationseigenschaft wie Eigenschaften in Dateien verwendet werden. 

1. Generieren Sie ein Beispielprojekt aus start.spring.io mit Azure Key Vault Spring Starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Geben Sie Ihren Key Vault in Ihrer App an. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Um die verwaltete Identität für Azure Spring Cloud-Apps zu verwenden, fügen Sie src/main/resources/application.properties Eigenschaften mit dem folgenden Inhalt hinzu.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Die Schlüsseltresor-URL muss in `application.properties` wie oben gezeigt hinzugefügt werden. Andernfalls kann die Key Vault-URL während der Laufzeit nicht erfasst werden.

3. Fügen Sie src/main/java/com/example/demo/DemoApplication.java das Codebeispiel hinzu. Dies ruft die Verbindungszeichenfolge aus Key Vault ab. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Wenn Sie „pom.xml“ öffnen, wird die Abhängigkeit von `azure-keyvault-secrets-spring-boot-starter` angezeigt. Fügen Sie diese Abhängigkeit Ihrem Projekt in „pom.xml“ hinzu. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Erstellen Sie eine Paket aus Ihrer Beispiel-App. 

    ```azurecli
    mvn clean package
    ```

5. Nun können Sie Ihre App mit dem Azure CLI-Befehl `az spring-cloud app deploy` in Azure bereitstellen. 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Um Ihre App zu testen, greifen Sie auf den öffentlichen Endpunkt oder den Testendpunkt zu.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Folgende Meldung wird angezeigt: „Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;“ (Der geheime ConnectionString-Wert wurde erfolgreich aus Key Vault https://<Ihr-KeyVault-Name>.vault.azure.net/ abgerufen: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;).

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Erstellen einer Spring Boot-Beispiel-App mit dem Java SDK

In diesem Beispiel können Geheimnisse in Azure Key Vault festgelegt und aus Azure Key Vault abgerufen werden. Die [Azure Key Vault Secret-Clientbibliothek für Java](/java/api/overview/azure/security-keyvault-secrets-readme?preserve-view=true&view=azure-java-stablelibrary) stellt Azure Active Directory-Tokenauthentifizierungsunterstützung über das Azure SDK bereit. Sie bietet eine Reihe von **TokenCredential**-Implementierungen, die zum Erstellen von Azure SDK-Clients zur Unterstützung von AAD-Tokenauthentifizierung verwendet werden können.

Mit der Azure Key Vault Secret-Clientbibliothek können Sie den Zugriff auf Token, Kennwörter, API-Schlüssel und andere Geheimnisse sicher speichern und steuern. Die Bibliothek bietet Vorgänge zum Erstellen, Abrufen, Aktualisieren, Löschen, Bereinigen, Sichern, Wiederherstellen und Auflisten der Geheimnisse und ihrer Versionen an.

1. Klonen Sie ein Beispielprojekt. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Geben Sie Ihren Key Vault in Ihrer App an. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Um die verwaltete Identität für Azure Spring Cloud-Apps zu verwenden, fügen Sie *src/main/resources/application.properties* Eigenschaften mit dem folgenden Inhalt hinzu.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Binden Sie [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder?preserve-view=true&view=azure-java-stable) ein, um Token aus Azure Active Directory abzurufen, und [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder?preserve-view=true&view=azure-java-stable), um Geheimnisse aus Key Vault in Ihrem Code festzulegen oder abzurufen.

    Rufen Sie das Beispiel aus [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) des geklonten Beispielprojekts ab.

    Nehmen Sie auch `azure-identity` und `azure-security-keyvault-secrets` als Abhängigkeit in Ihre Datei „pom.xml“ auf. Rufen Sie das Beispiel aus [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) des geklonten Beispielprojekts ab. 

4. Erstellen Sie eine Paket aus Ihrer Beispiel-App. 

    ```azurecli
    mvn clean package
    ```

5. Stellen Sie die App nun mit dem Azure CLI-Befehl `az spring-cloud app deploy` in Azure bereit. 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Greifen Sie auf den öffentlichen Endpunkt oder den Testendpunkt zu, um Ihre App zu testen. 

    Rufen Sie zunächst den Wert Ihres Geheimnisses ab, den Sie in Azure Key Vault festgelegt haben. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Folgende Meldung wird angezeigt: „Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;“ (Der geheime ConnectionString-Wert wurde erfolgreich aus Key Vault https://<Ihr-KeyVault-Name>.vault.azure.net/ abgerufen: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;).

    Erstellen Sie jetzt ein Geheimnis, und rufen Sie es dann mit dem Java SDK ab. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Die Meldung „Successfully got the value of secret test from Key Vault https://<your-keyvault-name>.vault.azure.net: success“ (Der Wert des Geheimnistests wurde erfolgreich aus Key Vault https://<Ihr-KeyVault-Name>.vault.azure.net abgerufen: Erfolg) wird angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

* [Zugreifen auf das Speicherblob mit der verwalteten Identität in Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Aktivieren einer systemseitig zugewiesenen verwalteten Identität für eine Azure Spring Cloud-Anwendung](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Authentifizieren von Azure Spring Cloud mit Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md)
