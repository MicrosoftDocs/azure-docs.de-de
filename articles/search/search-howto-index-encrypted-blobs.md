---
title: Durchsuchen von verschlüsselten Azure Blob Storage-Inhalten
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie,wie Sie Text aus verschlüsselten Dokumenten in Azure Blob Storage mit Azure Cognitive Search indizieren und extrahieren.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530934"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Indizieren verschlüsselter Blobs mithilfe von Blobindexern und Skillsets in Azure Cognitive Search

In diesem Artikel wird beschrieben, wie mit [Azure Cognitive Search](search-what-is-azure-search.md) Dokumente indiziert werden, die zuvor in [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) mithilfe von [Azure Key Vault](../key-vault/general/overview.md) verschlüsselt wurden. Ein Indexer kann normalerweise keine Inhalte aus verschlüsselten Dateien extrahieren, da er keinen Zugriff auf den Verschlüsselungsschlüssel hat. Wenn Sie jedoch den benutzerdefinierten Skill [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) und anschließend den Skill [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) verwenden, können Sie einen kontrollierten Zugriff auf den Schlüssel zum Entschlüsseln der Dateien ermöglichen und den Inhalt extrahieren lassen. Dadurch erhalten Sie die Möglichkeit, diese Dokumente zu indizieren, ohne den Verschlüsselungsstatus Ihrer gespeicherten Dokumente zu kompromittieren.

Ausgehend von zuvor verschlüsselten ganzen Dokumenten (unstrukturierter Text) wie PDF, HTML, DOCX und PPTX in Azure Blob Storage werden in diesem Leitfaden Postman und die REST-APIs von Search verwendet, um die folgenden Aufgaben auszuführen:

> [!div class="checklist"]
> * Definieren Sie eine Pipeline, mit der die Dokumente entschlüsselt und Text aus den Dokumenten extrahiert wird.
> * Definieren Sie einen Index zum Speichern der Ausgabe.
> * Führen Sie die Pipeline aus, um den Index zu erstellen und zu laden.
> * Erkunden Sie Ergebnisse per Volltextsuche und umfangreicher Abfragesyntax.

Sollten Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bei diesem Beispiel wird vorausgesetzt, dass Sie Ihre Dateien bereits in Azure Blob Storage hochgeladen und verschlüsselt haben. Eine Anleitung zum ersten Hochladen und Verschlüsseln von Dateien finden Sie in [diesem Tutorial](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

+ [Azure Storage (in englischer Sprache)](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) im selben Abonnement wie Azure Cognitive Search. **Vorläufiges Löschen** und **Löschschutz** müssen für den Schlüsseltresor aktiviert sein.
+ [Azure Cognitive Search](search-create-service-portal.md) in einem [abrechenbaren Tarif](search-sku-tier.md#tier-descriptions) (mindestens Basic in einer beliebigen Region)
+ [Azure-Funktion](https://azure.microsoft.com/services/functions/)
+ [Postman-Desktop-App](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1\. Erstellen von Diensten und Erfassen von Anmeldeinformationen

### <a name="set-up-the-custom-skill"></a>Einrichten des benutzerdefinierten Skills

In diesem Beispiel wird das Beispielprojekt [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) aus dem GitHub-Repository [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) verwendet. In diesem Abschnitt wird der Skill für eine Azure-Funktion zur Verwendung in einem Skillset bereitgestellt. Mit einem integrierten Bereitstellungsskript wird eine Azure Functions-Ressource erstellt, deren Name mit **psdbf-function-app-** beginnt. Zudem wird mit dem Skript der Skill geladen. Sie werden aufgefordert, ein Abonnement und eine Ressourcengruppe anzugeben. Geben Sie das Abonnement an, in dem sich Ihre Azure Key Vault-Instanz befindet.

Der Skill „DecryptBlobFile“ verwendet die URL und das SAS-Token für die einzelnen Blobs als Eingaben und gibt die heruntergeladene, entschlüsselte Datei mithilfe des von Azure Cognitive Search erwarteten Dateiverweisvertrags aus. „DecryptBlobFile“ benötigt zum Entschlüsseln den Verschlüsselungsschlüssel. Im Rahmen der Einrichtung erstellen Sie eine Zugriffsrichtlinie, mit der „DecryptBlobFile“ Funktionszugriff auf den Verschlüsselungsschlüssel in Azure Key Vault erteilt wird.

1. Klicken Sie auf die Schaltfläche **In Azure bereitstellen** auf der [Landing Page von „DecryptBlobFile“](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment). Daraufhin wird die bereitgestellte Resource Manager-Vorlage im Azure-Portal geöffnet.

1. Wählen Sie **das Abonnement aus, in dem sich Ihre Azure Key Vault-Instanz befindet** (diese Anleitung funktioniert nicht, wenn Sie ein anderes Abonnement auswählen), und wählen Sie entweder eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue (wenn Sie eine neue erstellen, müssen Sie auch eine Region für die Bereitstellung auswählen).

1. Wählen Sie **Überprüfen und erstellen** aus, stimmen Sie den Bedingungen zu, und klicken Sie dann auf **Erstellen**, um die Azure-Funktion bereitzustellen.

    ![ARM-Vorlage im Portal](media/indexing-encrypted-blob-files/arm-template.jpg "ARM-Vorlage im Portal")

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

1. Navigieren Sie im Portal zu Ihrer Azure Key Vault-Instanz. [Erstellen Sie in Azure Key Vault eine Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md), mit der dem benutzerdefinierten Skill Schlüsselzugriff erteilt wird.
 
    1. Wählen Sie unter **Einstellungen** die Option **Zugriffsrichtlinien** und dann **Zugriffsrichtlinie hinzufügen**.
     
       ![Hinzufügen einer Zugriffsrichtlinie in Key Vault](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Zugriffsrichtlinien in Key Vault")

    1. Wählen Sie unter **Anhand einer Vorlage konfigurieren** die Option **Azure Data Lake Storage oder Azure Storage** aus.

    1. Wählen Sie für den Prinzipal die Azure Functions-Instanz aus, die Sie bereitgestellt haben. Sie können anhand des Ressourcenpräfixes nach ihr suchen, das zum Erstellen der Instanz in Schritt 2 verwendet wurde und standardmäßig den Wert **psdbf-function-app** aufweist.

    1. Wählen Sie für die autorisierte Anwendungsoption nichts aus.
     
        ![Hinzufügen einer Zugriffsrichtlinienvorlage in Key Vault](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Zugriffsrichtlinienvorlage in Key Vault")

    1. Klicken Sie auf der Zugriffsrichtlinienseite auf **Speichern**, bevor Sie die Seite verlassen, um die Zugriffsrichtlinie hinzuzufügen.
     
         ![Speichern einer Zugriffsrichtlinie in Key Vault](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Speichern einer Zugriffsrichtlinie in Key Vault")

1. Navigieren Sie zur Funktion **psdbf-function-app** im Portal, und notieren Sie sich die folgenden Eigenschaften, da Sie sie später im Rahmen der Anleitung benötigen:

    1. Die URL der Funktion, die Sie unter **Zusammenfassung** auf der Hauptseite für die Funktion finden
    
        ![Funktions-URL](media/indexing-encrypted-blob-files/function-uri.jpg "Hier finden Sie die Azure Functions-URL")

    1. Der Schlüsselcode des Hosts, den Sie finden, indem Sie zu **App-Schlüssel** navigieren, klicken, um den **Standardschlüssel** anzuzeigen, und den Wert kopieren
     
        ![Schlüsselcode des Funktionshosts](media/indexing-encrypted-blob-files/function-host-key.jpg "Hier finden Sie den Azure Functions-Hostschlüsselcode")

### <a name="cognitive-services"></a>Cognitive Services

Die KI-Anreicherung und Skillsetausführung basieren auf Cognitive Services wie Textanalyse und maschinelles Sehen für die Verarbeitung von natürlicher Sprache und Bildern. Bei der Erstellung eines echten Prototyps oder Projekts würden Sie an dieser Stelle Cognitive Services bereitstellen (in der gleichen Region wie Azure Cognitive Search), um eine Verknüpfung mit Indizierungsvorgängen zu ermöglichen.

In dieser Übung können Sie die Ressourcenbereitstellung allerdings überspringen, da Azure Cognitive Search im Hintergrund eine Verbindung mit Cognitive Services herstellen kann und 20 kostenlose Transaktionen pro Indexerausführung ermöglicht. Nach der Verarbeitung von 20 Dokumenten tritt beim Indexer ein Fehler auf, wenn kein Cognitive Services-Schlüssel an das Skillset angefügt ist. Planen Sie bei umfangreicheren Projekten die Bereitstellung von Cognitive Services im S0-Tarif (nutzungsbasierte Bezahlung). Weitere Informationen finden Sie unter [Anfügen einer Cognitive Services-Ressource an eine Qualifikationsgruppe in Azure Search](cognitive-search-attach-cognitive-services.md). Zum Ausführen eines Skillsets mit mehr als 20 Dokumenten ist auch dann ein Cognitive Services-Schlüssel erforderlich, wenn keiner der ausgewählten kognitiven Skills eine Verbindung mit Cognitive Services herstellt (wie etwa beim bereitgestellten Skillset, wenn keine Skills hinzugefügt werden).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Die letzte Komponente ist Azure Cognitive Search, die Sie [im Portal erstellen](search-create-service-portal.md) können. Im Rahmen dieser Anleitung können Sie den Free-Tarif verwenden. 

Nehmen Sie sich wie bei der Azure-Funktion die Zeit, den Administratorschlüssel abzurufen. Wenn Sie später mit der Strukturierung von Anforderungen beginnen, müssen Sie den Endpunkt und den Administrator-API-Schlüssel für die Authentifizierung der jeweiligen Anforderung angeben.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Abrufen eines Administrator-API-Schlüssels und einer URL für Azure Cognitive Search

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts den Namen Ihres Suchdiensts ab. Sie können den Dienstnamen anhand der Endpunkt-URL überprüfen. Wenn Ihre Endpunkt-URL z.B. `https://mydemo.search.windows.net` lautet, ist der Name des Diensts `mydemo`.

2. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   ![Abrufen des Dienstnamens sowie der Administrator- und Abfrageschlüssel](media/search-get-started-javascript/service-name-and-keys.png)

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel im Header erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung sendet, und dem Dienst, der sie verarbeitet.

## <a name="2---set-up-postman"></a>2\. Einrichten von Postman

Führen Sie die Installation und Einrichtung von Postman durch.

### <a name="download-and-install-postman"></a>Herunterladen und Installieren von Postman

1. Laden Sie den [Quellcode für die Postman-Sammlung](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) herunter.
1. Wählen Sie **Datei** > **Importieren** aus, um den Quellcode in Postman zu importieren.
1. Wählen Sie die Registerkarte **Sammlungen** und dann die Schaltfläche **...** (Auslassungszeichen) aus.
1. Wählen Sie **Bearbeiten** aus. 
   
   ![Postman-App mit Navigation](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Navigieren zum Bearbeitungsmenü in Postman")
1. Wählen Sie im Dialogfeld **Edit** (Bearbeiten) die Registerkarte **Variables** (Variablen) aus. 

Auf der Registerkarte **Variables** (Variablen) können Sie Werte hinzufügen, die von Postman jeweils eingefügt werden, wenn eine bestimmte Variable in doppelten Klammern erkannt wird. Beispielsweise ersetzt Postman das Symbol `{{admin-key}}` durch den aktuellen Wert, den Sie für `admin-key` festlegen. Postman führt die Ersetzung in URLs, Headern, im Anforderungstext usw. durch. 

Rufen Sie den Wert für `admin-key` ab, indem Sie den zuvor notierten Azure Cognitive Search-API-Schlüssel für Administratoren verwenden. Legen Sie `search-service-name` auf den Namen des Azure Cognitive Search-Diensts fest, den Sie verwenden. Legen Sie `storage-connection-string` mit dem Wert auf der Registerkarte **Zugriffsschlüssel** Ihres Speicherkontos fest, und legen Sie `storage-container-name` auf den Namen des Blobcontainers in diesem Speicherkonto fest, in dem die verschlüsselten Dateien gespeichert sind. Legen Sie `function-uri` auf die zuvor notierte Azure Functions-URL fest, und legen Sie `function-code` auf den zuvor notierten Azure Functions-Hostschlüsselcode fest. Sie können für die anderen Werte die Standardwerte übernehmen.

![Postman-App, Registerkarte mit den Variablen](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Fenster mit den Postman-Variablen")

| Variable    | Ursprung |
|-------------|-----------------|
| `admin-key` | Auf der Seite **Schlüssel** des Azure Cognitive Search-Diensts  |
| `search-service-name` | Der Name des Azure Cognitive Search-Diensts. Die URL ist `https://{{search-service-name}}.search.windows.net`. |
| `storage-connection-string` | Wählen Sie im Speicherkonto auf der Registerkarte **Zugriffsschlüssel** **key1** > **Verbindungszeichenfolge** aus. |
| `storage-container-name` | Der Name des Blobcontainers, der die verschlüsselten Dateien enthält, die indiziert werden sollen. |
| `function-uri` |  In der Azure-Funktion unter **Zusammenfassung** auf der Hauptseite |
| `function-code` | In der Azure-Funktion, indem Sie zu **App-Schlüssel** navigieren, klicken, um den **Standardschlüssel** anzuzeigen, und den Wert kopieren |
| `api-version` | Übernehmen Sie **2020-06-30**. |
| `datasource-name` | Übernehmen Sie **encrypted-blobs-ds**. |
| `index-name` | Übernehmen Sie **encrypted-blobs-idx**. |
| `skillset-name` | Übernehmen Sie **encrypted-blobs-ss**. |
| `indexer-name` | Übernehmen Sie **encrypted-blobs-ixr**. |

### <a name="review-the-request-collection-in-postman"></a>Überprüfen der Auflistung von Anforderungen in Postman

Beim Ausführen dieser Anleitung müssen Sie vier HTTP-Anforderungen ausgeben:

- **PUT-Anforderung zum Erstellen des Index**: Dieser Index enthält die Daten, die von Azure Cognitive Search verwendet und zurückgegeben werden.
- **POST-Anforderung zum Erstellen der Datenquelle**: Diese Datenquelle verbindet Ihren Azure Cognitive Search-Dienst mit Ihrem Speicherkonto und somit mit verschlüsselten Blobdateien. 
- **PUT-Anforderung zum Erstellen des Skillsets**: Das Skillset gibt die benutzerdefinierte Skilldefinition für die Azure-Funktion an, die die Blobdateidaten entschlüsselt, und ein [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) zum Extrahieren des Texts aus den einzelnen Dokumenten nach der Entschlüsselung.
- **PUT-Anforderung zum Erstellen des Indexers**: Beim Ausführen des Indexers werden die Daten gelesen, das Skillset wird angewendet, und die Ergebnisse werden gespeichert. Diese Anforderung muss zuletzt ausgeführt werden.

Der [Quellcode](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) enthält eine Postman-Sammlung, in der die vier Anforderungen sowie einige nützliche Anschlussanforderungen enthalten sind. Wählen Sie zum Ausgeben der Anforderungen in Postman die jeweilige Registerkarte für die Anforderung und jeweils **Senden** aus.

## <a name="3---monitor-indexing"></a>3\. Überwachen der Indizierung

Indizierung und Anreicherung beginnen, sobald Sie die Anforderung für die Indexererstellung übermitteln. Die Indizierung kann je nach der Anzahl der Dokumente in Ihrem Speicherkonto einige Zeit in Anspruch nehmen. Wenn Sie feststellen möchten, ob der Indexer noch ausgeführt wird, verwenden Sie die im Rahmen der Postman-Sammlung bereitgestellte Anforderung **Indexerstatus abrufen**. Anhand der Antwort können Sie erkennen, ob der Indexer ausgeführt wird, oder Sie können Fehler- und Warnungsinformationen anzeigen.  

Wenn Sie den Free-Tarif verwenden, sollte folgende Meldung angezeigt werden: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"`. Diese Meldung wird angezeigt, da die Blobindizierung im Free-Tarif [auf 32.000 extrahierbare Zeichen beschränkt](search-limits-quotas-capacity.md#indexer-limits) ist. Bei höheren Tarifen wird diese Meldung für das Dataset nicht angezeigt. 

## <a name="4---search"></a>4\. Suchen

Nachdem Sie den Indexer ausgeführt haben, können Sie einige Abfragen ausführen, um zu überprüfen, ob die Daten erfolgreich entschlüsselt und indiziert wurden. Navigieren Sie im Portal zu Ihrem Azure Cognitive Search-Dienst, und verwenden Sie den [Such-Explorer](search-explorer.md), um für die indizierten Daten Abfragen auszuführen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die verschlüsselten Dateien erfolgreich indiziert haben, können Sie [in dieser Pipeline Wiederholungen durchführen, indem Sie weitere kognitive Skills hinzufügen](cognitive-search-defining-skillset.md). Dadurch können Sie Ihre Daten anreichern und zusätzliche Einblicke in Ihre Daten gewinnen.

Wenn Sie doppelt verschlüsselte Daten verwenden, sollten Sie sich über die in Azure Cognitive Search verfügbaren Indexverschlüsselungsfunktionen informieren. Der Indexer benötigt für die Indizierung zwar entschlüsselte Daten. Sobald der Index vorhanden ist, können diese jedoch mit einem kundenseitig verwalteten Schlüssel verschlüsselt werden. Dadurch wird sichergestellt, dass ruhende Daten immer verschlüsselt sind. Weitere Informationen finden Sie unter [Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung in Azure Cognitive Search](search-security-manage-encryption-keys.md).