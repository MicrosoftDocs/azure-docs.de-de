---
title: Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern
description: Erfahren Sie, wie Sie Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern verwenden können.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: hrasheed
ms.openlocfilehash: f381090e663923ec9f45fba03d0688c9879ab173
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427381"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern

Azure Data Lake Storage Gen2 ist ein Cloudspeicherdienst für Big Data-Analysen, der auf Azure Blob Storage basiert. Data Lake Storage Gen2 vereint die Funktionen von Azure Blob Storage und Azure Data Lake Storage Gen1. Der so entstandene Dienst bietet Funktionen von Azure Data Lake Storage Gen1, z.B. Dateisystemsemantik, Sicherheit auf Verzeichnis- und Dateiebene sowie Skalierbarkeit, und Funktionen von Azure Blob Storage wie einen kostengünstigen mehrstufigen Speicher, Hochverfügbarkeit und Funktionen zur Notfallwiederherstellung.

## <a name="data-lake-storage-gen2-availability"></a>Verfügbarkeit von Data Lake Storage Gen2

Data Lake Storage Gen2 ist als Speicheroption für fast alle Typen von Azure HDInsight-Clustern sowohl als Standard- als auch zusätzliches Speicherkonto verfügbar. HBase kann jedoch nur ein Data Lake Storage Gen2-Konto verwenden.

> [!Note]  
> Nachdem Sie Data Lake Storage Gen2 als **primären Speichertyp** ausgewählt haben, können Sie kein Data Lake Storage Gen1-Konto als zusätzlichen Speicher auswählen.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Erstellen eines Clusters mit Data Lake Storage Gen2 über das Azure-Portal

Konfigurieren Sie mithilfe der folgenden Schritte ein Data Lake Storage Gen2-Konto, um einen HDInsight-Cluster zu erstellen, der Data Lake Storage Gen2 als Speicher verwendet.

### <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität, falls noch keine vorhanden ist. Informationen hierzu finden Sie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Weitere Informationen zur Funktionsweise verwalteter Identitäten in Azure HDInsight finden Sie unter [Managed identities in Azure HDInsight (Verwaltete Identitäten in Azure HDInsight)](hdinsight-managed-identities.md).

![Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Erstellen eines Data Lake Storage Gen2-Kontos

Erstellen Sie ein Azure Data Lake Storage Gen2-Speicherkonto. Stellen Sie sicher, dass die Option **Hierarchischer Namespace** aktiviert ist. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Azure Data Lake Storage Gen2-Speicherkontos](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Screenshot zur Speicherkontoerstellung im Azure-Portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Festlegen von Berechtigungen für verwaltete Identitäten im Data Lake Storage Gen2-Konto

Weisen Sie die verwaltete Identität der Rolle **Besitzer von Speicherblobdaten** im Speicherkonto zu. Weitere Informationen finden Sie unter [Verwalten von Zugriffsrechten für Blob- und Warteschlangendaten von Azure mit RBAC (Vorschau)](../storage/common/storage-auth-aad-rbac.md).

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.
1. Wählen Sie Ihr Speicherkonto und dann **Zugriffssteuerung (IAM)** aus, um die Zugriffssteuerungseinstellungen für das Konto anzuzeigen. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die Liste mit den Rollenzuweisungen anzuzeigen.
    
    ![Screenshot mit Anzeige der Zugriffssteuerungseinstellungen für Speicher](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Klicken Sie auf die Schaltfläche **+ Rollenzuweisung hinzufügen**, um eine neue Rolle hinzuzufügen.
1. Wählen Sie im Fenster **Rollenzuweisung hinzufügen** die Rolle **Besitzer von Speicherblobdaten** aus. Wählen Sie dann das Abonnement aus, das über die verwaltete Identität und das Speicherkonto verfügt. Suchen Sie als Nächstes die vom Benutzer zugewiesene verwaltete Identität, die Sie zuvor erstellt haben. Wählen Sie abschließend die verwaltete Identität aus, sodass sie unter **Ausgewählte Mitglieder** aufgelistet wird.
    
    ![Screenshot: Zuweisen einer RBAC-Rolle](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Wählen Sie **Speichern** aus. Die vom Benutzer zugewiesene Identität, die Sie ausgewählt haben, wird jetzt unter der ausgewählten Rolle aufgelistet.
1. Nachdem das anfängliche Setup abgeschlossen ist, können Sie einen Cluster über das Portal erstellen. Der Cluster muss sich in der gleichen Azure-Region befinden wie das Speicherkonto. Wählen Sie im Abschnitt **Speicher** des Menüs zur Clustererstellung die folgenden Optionen aus:
        
    * Wählen Sie für **Primärer Speichertyp** **Azure Data Lake Storage Gen2** aus.
    * Suchen Sie unter **Speicherkonto auswählen** das neu erstellte Data Lake Storage Gen2-Speicherkonto, und wählen Sie es aus.
        
        ![Speichereinstellungen für das Verwenden von Data Lake Storage Gen2 mit Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Wählen Sie unter **Identität** das richtige Abonnement und die neu erstellte vom Benutzer zugewiesene verwaltete Identität aus.
        
        ![Identitätsstellungen für das Verwenden von Data Lake Storage Gen2 mit Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Um ein sekundäres Data Lake Storage Gen2-Konto hinzuzufügen, weisen Sie einfach auf der Speicherkontoebene die zuvor erstellte verwaltete Identität dem neuen Data Lake Storage Gen2-Speicherkonto zu, das Sie hinzufügen möchten. Beachten Sie, dass das Hinzufügen eines sekundären Data Lake Storage Gen2-Kontos über die Registerkarte „Zusätzliche Speicherkonten“ auf HDInsight nicht unterstützt wird. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Erstellen eines Clusters mit Data Lake Storage Gen2 über die Azure CLI

Sie können [hier eine Beispielvorlagendatei](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) und [hier eine Beispieldatei für Parameter](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json) herunterladen. Bevor Sie die Vorlage verwenden, ersetzen Sie die Zeichenfolge `<SUBSCRIPTION_ID>` durch die tatsächliche ID Ihres Azure-Abonnements. Ersetzen Sie außerdem die Zeichenfolge `<PASSWORD>` durch Ihr Kennwort, um das Kennwort, das Sie zur Anmeldung beim Cluster verwenden, und das SSH-Kennwort festzulegen.

Der Codeausschnitt führt die folgenden ersten Schritte durch:

1. Anmeldung bei Ihrem Azure-Konto
1. Festlegen des aktiven Abonnements, in dem die Erstellung durchgeführt wird
1. Erstellen einer neuen Ressourcengruppe mit dem Namen `hdinsight-deployment-rg` für die neuen Bereitstellungsaktivitäten
1. Erstellen einer benutzerseitig zugewiesenen verwalteten Identität mit dem Namen `test-hdinsight-msi`
1. Hinzufügen einer Erweiterung zur Azure CLI, um Features für Data Lake Storage Gen2 zu nutzen
1. Erstellen eines neuen Data Lake Storage Gen2-Kontos mit dem Namen `hdinsightadlsgen2` mithilfe des Flags `--hierarchical-namespace true`

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Melden Sie sich als Nächstes beim Portal an. Fügen Sie die neue benutzerseitig zugewiesene verwaltete Identität der Rolle **Mitwirkender an Storage-Blobdaten** für das Speicherkonto hinzu. Dies wird in Schritt 3 oben unter [Verwenden des Azure-Portals](hdinsight-hadoop-use-data-lake-storage-gen2.md) beschrieben.

Stellen Sie die Vorlage mit dem folgenden Codeausschnitt bereit, nachdem Sie die Rolle für die benutzerseitig zugewiesene verwaltete Identität zugewiesen haben.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Zugriffssteuerung für Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Welche Arten von Berechtigungen unterstützt Data Lake Storage Gen2?

Data Lake Storage Gen2 verwendet ein Zugriffssteuerungsmodell, das sowohl die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC) als auch POSIX-Zugriffssteuerungslisten (Access Control Lists, ACLs) unterstützt. Data Lake Storage Gen1 unterstützt Zugriffssteuerungslisten nur zum Steuern des Zugriffs auf Daten.

Für die RBAC in Azure werden Rollenzuweisungen verwendet, um Benutzern, Gruppen und Dienstprinzipalen Berechtigungen für Azure-Ressourcen effektiv zuzuweisen. In der Regel sind diese Azure-Ressourcen auf Ressourcen auf oberster Ebene (z.B. Azure Storage-Konten) beschränkt. Für Azure Storage und damit auch Data Lake Storage Gen2 wurde dieser Mechanismus auf die Dateisystemressource ausgeweitet.

 Weitere Informationen zu Dateiberechtigungen mit rollenbasierter Zugriffssteuerung finden Sie im Abschnitt „Rollenbasierte Zugriffssteuerung in Azure (RBAC)“ unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Weitere Informationen zu Dateiberechtigungen mit ACLs finden Sie im Abschnitt „Zugriffssteuerungslisten für Dateien und Verzeichnisse“ unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Wie steuere ich in Data Lake Storage Gen2 den Zugriff auf meine Daten?

In Data Lake Storage Gen2 steuern verwaltete Identitäten den Zugriff auf Dateien durch Ihren HDInsight-Cluster. Eine verwaltete Identität ist eine in Azure Active Directory (Azure AD) registrierte Identität, deren Anmeldeinformationen von Azure verwaltet werden. Mit verwalteten Identitäten müssen Sie keine Dienstprinzipale in Azure AD registrieren und keine Anmeldeinformationen wie z.B. Zertifikate verwalten.

Es gibt zwei Arten verwalteter Identitäten für Azure-Dienste: durch das System zugewiesene und durch den Benutzer zugewiesene Identitäten. HDInsight verwendet durch den Benutzer zugewiesene verwaltete Identitäten, um auf Data Lake Storage Gen2 zuzugreifen. Eine vom Benutzer zugewiesene verwaltete Identität wird als eigenständige Azure-Ressource erstellt. Azure erstellt eine Identität in dem Azure AD-Mandanten, der vom verwendeten Abonnement als vertrauenswürdig eingestuft wird. Nachdem die Identität erstellt wurde, kann sie einer oder mehreren Azure-Dienstinstanzen zugewiesen werden.

Der Lebenszyklus einer vom Benutzer zugewiesenen Identität wird getrennt vom Lebenszyklus der Azure-Dienstinstanzen verwaltet, denen sie zugewiesen ist. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Wie richte ich Berechtigungen für Azure AD-Benutzer zum Abfragen von Daten in Data Lake Storage Gen2 mit Hive oder anderen Diensten ein?

Verwenden Sie Azure AD-Sicherheitsgruppen als zugewiesenen Prinzipal in ACLs, um Benutzern Berechtigungen zum Abfragen von Daten zu erteilen. Weisen Sie einzelnen Benutzern oder Dienstprinzipalen nicht direkt Dateizugriffsberechtigungen zu. Wenn Sie Azure AD-Sicherheitsgruppen zum Steuern des Flusses von Berechtigungen verwenden, können Sie Benutzer oder Dienstprinzipale hinzufügen und entfernen, ohne erneut ACLs auf eine gesamte Verzeichnisstruktur anwenden zu müssen. Sie müssen die Benutzer einfach der entsprechenden Azure AD-Sicherheitsgruppe hinzufügen oder sie aus ihr entfernen. ACLs werden nicht vererbt, und die erneute Anwendung von ACLs erfordert daher eine Aktualisierung der ACL für jede Datei und jedes Unterverzeichnis.

## <a name="next-steps"></a>Nächste Schritte

* [Azure HDInsight integration with Data Lake Storage Gen2 preview – ACL and security update (Integration von Azure HDInsight in Data Lake Storage Gen2 (Vorschau) – ACL und Sicherheitsupdate)](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Einführung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
