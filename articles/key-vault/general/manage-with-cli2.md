---
title: Verwalten von Azure Key Vault mit der CLI – Azure Key Vault | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zum Automatisieren von häufigen Aufgaben in Key Vault mithilfe der Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: cb7e047d998342125a52af5ea3ae1e88fe88d313
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289903"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Verwalten von Key Vault mit der Azure CLI 

In diesem Artikel wird beschrieben, wie Sie Azure Key Vault mithilfe der Azure CLI verwenden.  Sie finden hier Informationen zu folgenden Schritten:

- Erstellen eines festgeschriebenen Containers (Tresors) in Azure
- Hinzufügen eines Schlüssels, Geheimnisses oder Zertifikats zum Schlüsseltresor
- Registrieren einer Anwendung mit Azure Active Directory
- Autorisieren der Verwendung eines Schlüssels oder Geheimnisses durch eine Anwendung
- Festlegen von erweiterten Zugriffsrichtlinien für Schlüsseltresore
- Verwenden von Hardwaresicherheitsmodulen (HSMs)
- Löschen des Schlüsseltresors und der zugeordneten Schlüssel und Geheimnisse
- Sonstige plattformübergreifende Microsoft Azure-Befehlszeilenschnittstelle-Befehle


Azure-Tresorschlüssel ist in den meisten Regionen verfügbar. Weitere Informationen finden Sie auf der Seite [Preisübersicht für Schlüsseltresor](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Diese Artikel enthält keine Anweisungen zum Schreiben der Azure-Anwendung, die einen der Schritte enthält, der zeigt, wie Sie eine Anwendung zum Verwenden eines Schlüssels oder Geheimnisses im Schlüsseltresor autorisieren.
>

Eine Übersicht über Azure Key Vault finden Sie unter [Was ist Azure Key Vault?](overview.md). Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Verwendung der Azure CLI-Befehle in diesem Artikel benötigen Sie Folgendes:

* Ein Abonnement für Microsoft Azure. Falls Sie über kein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial) registrieren.
* Azure-Befehlszeilenschnittstelle Version 2.0 oder höher. Unter [Install the Azure CLI (Installieren der Azure CLI)](/cli/azure/install-azure-cli) erhalten Sie Informationen zur Installation der neuesten Version.
* Eine Anwendung, die zur Verwendung des Schlüssels oder Kennworts konfiguriert wird, den bzw. das Sie in diesem Artikel erstellen. Eine Beispielanwendung erhalten Sie im [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45343). Anweisungen finden Sie in der enthaltenen Infodatei.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Hilfestellung für die plattformübergreifende Azure-Befehlszeilenschnittstelle

In diesem Artikel wird davon ausgegangen, dass Sie mit der Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung) vertraut sind.

Der Parameter --help oder -h kann angegeben werden, um Hilfetexte zu bestimmten Befehlen anzuzeigen. Alternativ kann auch das Format „azure help [Befehl] [Optionen]“ verwendet werden. Wenn Sie sich nicht ganz sicher sind, welche Parameter für einen Befehl angegeben werden müssen, rufen Sie die entsprechende Hilfe ab. Beispielsweise geben die folgenden Befehle die gleichen Informationen zurück:

```azurecli
az account set --help
az account set -h
```

Lesen Sie bitte auch die folgenden Artikel, um sich mit dem Azure Resource Manager in der plattformübergreifenden Azure CLI vertraut zu machen:

* [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
* [Erste Schritte mit der Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Erstellen eines festgeschriebenen Containers (Tresors) in Azure

Tresore sind durch Hardwaresicherheitsmodule (HSMs) geschützte Container. Tresore zentralisieren die Speicherung von Anwendungsgeheimnissen und verringern so die Gefahr, dass Sicherheitsinformationen abhandenkommen. Darüber hinaus steuern und protokollieren Key Vault-Instanzen auch den Zugriff auf alle darin gespeicherten Daten. Azure Key Vault kann Anforderungen und Verlängerungen von TLS-Zertifikaten (Transport Layer Security) abwickeln und stellt Features bereit, die für eine zuverlässige Zertifikatlebenszyklus-Verwaltungslösung benötigt werden. In den nächsten Schritten erstellen Sie einen Tresor.

### <a name="connect-to-your-subscriptions"></a>Verbindungsherstellung mit Ihren Abonnements

Verwenden Sie den folgenden Befehl, um sich interaktiv anzumelden:

```azurecli
az login
```
Wenn Sie sich mit einem Organisationskonto anmelden möchten, können Sie Ihren Benutzernamen und Ihr Kennwort übergeben.

```azurecli
az login -u username@domain.com -p password
```

Wenn Sie über mehrere Abonnements verfügen und ein bestimmtes Abonnement verwenden müssen, geben Sie den folgenden Befehl ein, um die Abonnements für Ihr Konto anzuzeigen:

```azurecli
az account list
```

Geben Sie ein Abonnement mit dem Abonnementparameter an.

```azurecli
az account set --subscription <subscription name or ID>
```

Weitere Informationen über die Konfiguration der plattformübergreifenden Azure-Befehlszeilenschnittstelle finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Erstellen einer neuen Ressourcengruppe

Wenn Sie den Azure-Ressourcen-Manager verwenden, werden alle zugehörigen Ressourcen in einer Ressourcengruppe erstellt. Sie können in einer vorhandenen Ressourcengruppe einen Schlüsseltresor erstellen. Wenn Sie eine neue Ressourcengruppe verwenden möchten, können Sie eine neue Gruppe erstellen.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Der erste Parameter ist der Ressourcengruppenname und der zweite Parameter der Speicherort. Geben Sie zum Abrufen einer Liste aller möglichen Standorte Folgendes ein:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrieren des Schlüsseltresor-Ressourcenanbieters

 Beim Erstellen eines neuen Schlüsseltresors wird möglicherweise folgende Fehlermeldung angezeigt: „Das Abonnement ist nicht für die Verwendung des Namespace "Microsoft.KeyVault" registriert.“ Stellen Sie in diesem Fall sicher, dass der Key Vault-Ressourcenanbieter in Ihrem Abonnement registriert ist. Dieser Schritt muss einmal für jedes Abonnement ausgeführt werden.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Verwenden Sie den Befehl `az keyvault create` , um einen Schlüsseltresor zu erstellen. Das Skript verfügt über drei erforderliche Parameter: einen Ressourcengruppennamen, einen Schlüsseltresornamen und den geografischen Standort.

Um einen neuen Tresor mit dem Namen **ContosoKeyVault** in der Ressourcengruppe **ContosoResourceGroup** in der Region **Asien, Osten** zu erstellen, geben Sie Folgendes ein: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Die Ausgabe dieses Befehls zeigt die Eigenschaften des Schlüsseltresors, den Sie erstellt haben. Die zwei wichtigsten Eigenschaften sind diese:

* **name:** Im Beispiel lautet der Name „ContosoKeyVault“. Sie verwenden diesen Namen für andere Key Vault-Befehle.
* **vaultUri** : In diesem Beispiel lautet der URI https://contosokeyvault.vault.azure.net. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

Ihr Azure-Konto ist jetzt autorisiert, Vorgänge in diesem Schlüsseltresor durchzuführen. Derzeit ist noch keine andere Person autorisiert.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Hinzufügen eines Schlüssels, Geheimnisses oder Zertifikats zum Schlüsseltresor

Wenn Sie mit Azure Key Vault einen softwaregeschützten Schlüssel erstellen möchten, verwenden Sie hierzu den Befehl `az key create`.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Wenn bereits ein Schlüssel in einer PEM-Datei vorhanden ist, können Sie diesen in Azure Key Vault hochladen. Sie können den Schlüssel wahlweise mit Software oder dem HSM schützen. In diesem Beispiel wird dieser Schlüssel aus der PEM-Datei importiert und mit Software über das Kennwort „hVFkk965BuUv“ geschützt:

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Jetzt können Sie mittels seiner URI auf den Schlüssel verweisen, den Sie erstellt oder in  Azure-Schlüsseltresor hochgeladen haben. Mit **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** können Sie immer die aktuelle Version abrufen. Verwenden Sie zum Abrufen dieser Version „https://[Name-des-Schlüsseltresors].vault.azure.net/keys/[Schlüsselname]/[Eindeutige-Schlüssel-ID]“. Beispiel: **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** 

Fügen Sie dem Schlüsseltresor ein Geheimnis hinzu – in diesem Fall das Kennwort „SQLPassword“ mit dem Wert „hVFkk965BuUv“ für Azure Key Vault-Instanzen. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Verweisen Sie auf dieses Kennwort mithilfe des URI. Mit **https://ContosoVault.vault.azure.net/secrets/SQLPassword** können Sie immer die aktuelle Version und mit „https://[Name-des-Schlüsseltresors].vault.azure.net/secret/[Name-des-Geheimnisses]/[Eindeutige-Geheimnis-ID]“ diese Version abrufen. Beispiel: **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**

Importieren Sie mithilfe einer PEM- oder PFX-Datei ein Zertifikat in den Tresor.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Sehen Sie sich die von Ihnen erstellten Schlüssel, Geheimnisse oder Zertifikate an:

* Geben Sie Folgendes ein, um Ihre Schlüssel anzuzeigen: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Geben Sie Folgendes ein, um Ihre Geheimnisse anzuzeigen: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Geben Sie Folgendes ein, um Ihre Zertifikate anzuzeigen: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrieren einer Anwendung mit Azure Active Directory

Dieser Schritt wird üblicherweise durch einen Entwickler auf einem separaten Computer durchgeführt. Dieser Schritt ist nicht spezifisch für Azure Key Vault, wird der Vollständigkeit halber jedoch hier aufgeführt. Um die App-Registrierung abzuschließen, müssen sich Ihr Konto, der Tresor und die Anwendung im gleichen Azure-Verzeichnis befinden.

Anwendungen, die einen Schlüsseltresor verwenden, müssen sich mithilfe eines Azure Active Directory-Tokens authentifizieren.  Der Besitzer der Anwendung muss diese zuerst in Azure Active Directory registrieren. Zum Abschluss der Registrierung erhält der Anwendungsbesitzer die folgenden Werte:

- Eine **Anwendungs-ID** (auch als „AAD-Client-ID“ oder „appID“ bezeichnet)
- Einen **Authentifizierungsschlüssel** (auch bezeichnet als gemeinsames Geheimnis) 

Die Anwendung muss beide dieser Werte in Azure Active Directory vorlegen, um ein Token zu erhalten. Die Konfiguration des Tokenabrufs hängt von der Anwendung ab. Bei der [Key Vault-Beispielanwendung](https://www.microsoft.com/download/details.aspx?id=45343) legt der Anwendungsbesitzer diese Werte in der Datei „app.config“ fest.

Ausführliche Anweisungen zum Registrieren einer Anwendung in Azure Active Directory finden Sie in den Artikeln [Integrieren von Anwendungen in Azure Active Directory](../../active-directory/develop/quickstart-register-app.md), [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../../active-directory/develop/howto-create-service-principal-portal.md) und [Erstellen eines Azure-Dienstprinzipals mit der Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

Registrieren Sie eine Anwendung in Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autorisieren der Verwendung eines Schlüssels oder Geheimnisses durch eine Anwendung

Verwenden Sie den Befehl `az keyvault set-policy` , um die Anwendung zum Zugreifen auf den Schlüssel oder geheimen Schlüssel im Tresor zu autorisieren.

Wenn Ihr Tresorname beispielsweise „ContosoKeyVault“ lautet, die Anwendung die appID „8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed“ aufweist und Sie die Anwendung zum Entschlüsseln und Anmelden mit Schlüsseln in Ihrem Tresor autorisieren möchten, führen Sie Folgendes aus:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Um dieselbe Anwendung so zu autorisieren, dass sie Geheimnisse in Ihrem Tresor liest, geben Sie den folgenden Befehl ein:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Festlegen von erweiterten Zugriffsrichtlinien für Schlüsseltresore

Verwenden Sie [az keyvault update](/cli/azure/keyvault#az-keyvault-update), um erweiterte Richtlinien für den Schlüsseltresor zu aktivieren.

 Aktivieren von Key Vault für die Bereitstellung: Virtuelle Computer können Zertifikate aus dem Tresor abrufen, die als Geheimnisse gespeichert sind.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Aktivieren von Key Vault für die Datenträgerverschlüsselung: Dieser Befehl ist für die Verwendung des Tresors für Azure Disk Encryption erforderlich.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Aktivieren von Key Vault für die Vorlagenbereitstellung: Der Resource Manager kann Geheimnisse aus dem Tresor abrufen.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Verwenden von Hardwaresicherheitsmodulen (HSMs)

Zur Erhöhung der Sicherheit können Sie Schlüssel aus Hardwaresicherheitsmodulen (HSMs) importieren oder in diesen generieren. Diese Schlüssel verbleiben immer innerhalb der HSM-Grenzen. Die HSMs sind FIPS 140-2 Ebene 2 überprüft. Wenn diese Anforderung nicht auf Sie zutrifft, überspringen Sie diesen Abschnitt und wechseln zu „Löschen des Schlüsseltresors und zugeordneter Schlüssel und geheimer Schlüssel“.

Um diese HSM-geschützten Schlüssel zu erstellen, müssen Sie über ein Tresorabonnement mit Unterstützung von HSM-geschützten Schlüsseln verfügen.

Wenn Sie den Tresor erstellen, fügen Sie den Parameter "SKU" hinzu:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Sie können diesem Tresor softwaregeschützte Schlüssel (wie weiter oben gezeigt) und HSM-geschützte Schlüssel hinzufügen. Legen Sie den Parameter "Destination" auf "HSM" fest, um einen HSM-geschützten Schlüssel zu erstellen:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Sie können mit dem folgenden Befehl einen Schlüssel aus einer PEM-Datei auf Ihren Computer importieren. Dieser Befehl importiert den Schlüssel in HSMs im Schlüsseltresordienst:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Mit dem nächsten Befehl wird ein BYOK-Paket (Bring Your Own Key) importiert. Auf diese Weise können Sie Ihren Schlüssel im lokalen HSM generieren und ihn in HSMs im Schlüsseltresordienst übertragen, ohne dass der Schlüssel die HSM-Grenzen verlässt:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Ausführlichere Informationen zum Generieren dieses BYOK-Pakets finden Sie unter [Verwenden von HSM-geschützten Schlüsseln mit dem Azure-Schlüsseltresor](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Löschen des Schlüsseltresors und der zugeordneten Schlüssel und Geheimnisse

Wenn Sie den Schlüsseltresor und die zugehörigen Schlüssel und Geheimnisse nicht mehr benötigen, können Sie den Schlüsseltresor mit dem Befehl `az keyvault delete` löschen:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Alternativ können Sie eine gesamte Azure-Ressourcengruppe löschen, die den Schlüsseltresor und alle weiteren Ressourcen enthält, die Sie in diese Gruppe eingeschlossen haben:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Sonstige plattformübergreifende Microsoft Azure-Befehlszeilenschnittstelle-Befehle

Die folgenden weiteren Befehle sind möglicherweise ebenfalls für die Verwaltung von Azure Key Vault von Nutzen:

Dieser Befehl ruft eine tabellarische Anzeige aller Schlüssel und ausgewählten Eigenschaften ab:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Dieser Befehl zeigt eine vollständige Liste der Eigenschaften für den angegebenen Schlüssel an:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Dieser Befehl ruft eine tabellarische Anzeige der Namen aller geheimen Schlüssel und ausgewählten Eigenschaften ab:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Hier ist Beispiel, wie ein bestimmter Schlüssel entfernt werden kann:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Hier ist ein Beispiel, wie ein bestimmten geheimer Schlüssel entfernt werden kann:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Nächste Schritte

- Die vollständige Referenz zur Azure CLI für Key Vault-Befehle finden Sie in der [Referenz für die Schlüsseltresor-CLI](/cli/azure/keyvault).

- Eine Programmierreferenz finden Sie im [Entwicklerhandbuch für Azure Key Vault](developers-guide.md).

- Informationen zu Azure Key Vault und HSMs finden Sie unter [Gewusst wie: Generieren und Übertragen von HSM-geschützten Schlüsseln für den Azure-Schlüsseltresor](../keys/hsm-protected-keys.md).