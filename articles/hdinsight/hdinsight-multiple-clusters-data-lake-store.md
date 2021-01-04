---
title: Mehrere HDInsight-Cluster und ein Azure Data Lake Storage-Konto
description: Erfahren Sie, wie Sie mehr als ein HDInsight-Cluster mit einem einzigen Data Lake Storage-Konto verwenden können
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: df28374d0f124ceb46d2f97d55218d428275deca
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533086"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Verwenden von mehreren HDInsight-Clustern mit einem Azure Data Lake Storage-Konto

Ab HDInsight Version 3.5 können Sie HDInsight-Cluster mit Azure Data Lake Storage-Konten als Standarddateisystem erstellen.
Data Lake Storage unterstützt unbegrenzten Speicher, wodurch es nicht nur für das Hosten von großen Datenmengen, sondern auch für das Hosten von HDInsight Clustern ideal ist, die ein gemeinsames Data Lake Storage-Konto haben. Anweisungen zum Erstellen eines HDInsight-Clusters mit Data Lake Storage als Speicher finden Sie unter [Schnellstart: Einrichten von Clustern in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

Dieser Artikel bietet Empfehlungen für Data Lake Storage-Administratoren bezüglich des Einrichtens eines einzelnen und eines geteilten Data Lake Storage-Kontos, das über mehrere **aktive** HDInsight-Cluster hinweg verwendet werden kann. Diese Empfehlungen gelten für das Hosten von mehreren sicheren sowie nicht-sicheren Apache Hadoop-Clustern in einem geteilten Data Lake Storage-Konto.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage-ACLs auf Datei- und Ordnerebene

Im weiteren Verlauf dieses Artikels wird davon ausgegangen, dass Sie über ausreichendes Wissen zu Azure Data Lake Storage-ACLs auf Datei- und auf Ordnerebene verfügen. Ausführliche Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Einrichten von mehreren HDInsight-Clustern mit Data Lake Storage

Anhand einer Hierarchie mit zwei Ebenen werden die Empfehlungen für mehrere HDInsight-Cluster mit einem Data Lake Storage-Konto erklärt. Stellen Sie sich vor, dass Sie ein Data Lake Storage-Konto mit der Ordnerstruktur **/clusters/finance** haben. Mit dieser Struktur können alle für die Organisation „Finanzen“ erforderlichen Cluster „/clusters/finance“ als Speicherort verwenden. Wenn zukünftig eine andere Organisation, z.B. „Marketing“, ein HDInsight-Cluster mit einem Data Lake Storage-Konto erstellen möchte, kann sie „/clusters/marketing“ erstellen. Verwenden Sie für den Moment **/clusters/finance** .

Der Data Lake Storage-Administrator muss die entsprechenden Berechtigungen (wie in der Tabelle beschrieben) zuweisen, damit diese Ordnerstruktur effektiv von HDInsight-Clustern verwendet werden kann. Die in der Tabelle aufgelisteten Berechtigungen entsprechen einer Zugriffs-ACL, und nicht einer Standard-ACL.

|Ordner  |Berechtigungen  |zuständige Benutzer  |zuständige Gruppe  | Benannter Benutzer | Berechtigungen für benannte Benutzer | Benannte Gruppe | Berechtigungen für benannte Gruppen |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Dienstprinzipal |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |admin |admin |Dienstprinzipal |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP  |Dienstprinzipal |rwx  |-  |-     |

In der Tabelle

- ist **admin** der Ersteller und Administrator des Data Lake Storage-Kontos.
- **Dienstprinzipal** ist der Dienstprinzipal von Azure Active Directory (AAD), der mit dem Konto verknüpft ist.
- **FINGRP** ist eine Benutzergruppe, die in AAD erstellt wurde, die Benutzer der Organisation „Finanzen“ enthält.

Anweisungen zum Erstellen einer AAD-Anwendung (die auch einen Dienstprinzipal erstellt) finden Sie unter [Create an AAD application (Erstellen einer AAD-Anwendung)](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Anweisungen zum Erstellen einer Benutzergruppe in AAD finden Sie unter [Verwalten von Gruppen in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Zu berücksichtigende Punkte:

- Die Ordnerstruktur mit zwei Ebenen ( **/clusters/finance/** ) muss mit entsprechenden Berechtigungen vom Data Lake Storage-Administrator erstellt und bereitgestellt werden, **bevor** das Speicherkonto für Cluster verwendet wird. Diese Struktur wird nicht automatisch beim Erstellen von Clustern erstellt.
- Im oben stehenden Beispiel wird empfohlen, die Gruppe von **/clusters/finance** auf **FINGRP** festzulegen und **r-x** Zugriff auf „FINGRP“ und auf die gesamte Ordnerhierarchie vom Stamm aus zu gewähren. So wird sichergestellt, dass die Member von FINGRP vom Stamm aus durch die Ordnerstruktur navigieren können.
- Wenn verschiedene AAD-Dienstprinzipale Cluster unter **/clusters/finance** erstellen können, stellt das Sticky Bit (wenn es auf den Ordner **Finanzen** festgelegt ist) sicher, dass Ordner, die von einem Dienstprinzipal erstellt wurden, nicht von dem anderen gelöscht werden können.
- Sobald die Ordnerstruktur und Berechtigungen eingerichtet wurden, erstellt der HDInsight-Clustererstellungsprozess einen clusterspezifischen Speicherort unter **/clusters/finance/** . Der Speicher für einen Cluster mit dem Namen „fincluster01“ könnte z.B. **/clusters/finance/fincluster01** sein. Diese Tabelle zeigt die Zuständigkeit und die Berechtigungen für die vom HDInsight-Cluster erstellten Ordner.

    |Ordner  |Berechtigungen  |zuständige Benutzer  |zuständige Gruppe  | Benannter Benutzer | Berechtigungen für benannte Benutzer | Benannte Gruppe | Berechtigungen für benannte Gruppen |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Dienstprinzipal |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Empfehlungen für Auftragsein- und ausgabedaten

Es wird empfohlen, dass die Eingabedaten und die Ausgaben eines Auftrags in einem Ordner außerhalb von **/clusters** gespeichert werden. So wird sichergestellt, dass die Auftragseingaben und -ausgaben auch weiterhin zur Verfügung stehen, selbst wenn der clusterspezifische Ordner gelöscht wird, um Speicherplatz frei zu machen. Stellen Sie in einem derartigen Fall sicher, dass die Ordnerhierarchie für das Speichern der Ein- und Ausgaben über den entsprechenden Zugriffsgrad für den Dienstprinzipal verfügt.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Grenzwert von Clustern mit einem einzigen Speicherkonto

Der Grenzwert der Anzahl von Clustern, die sich ein einziges Data Lake Storage-Konto teilen, hängt von der Workload ab, die auf den Clustern ausgeführt wird. Wenn Sie zu viele Cluster haben, oder sich sehr hohe Workloads auf den Clustern befinden, die ein gemeinsames Speicherkonto haben, kann es passieren, dass der ein- und ausgehende Datenverkehr gedrosselt wird.

## <a name="support-for-default-acls"></a>Unterstützung für Standard-ACLs

Beim Erstellen eines Dienstprinzipals mit Zugriff für benannte Benutzer (wie in oben stehender Tabelle gezeigt), wird empfohlen, den benannten Benutzer **nicht** mit einer Standard-ACL hinzuzufügen. Wenn Sie den Zugriff für benannte Benutzer mit Standard-ACLs bereitstellen, werden 770 Berechtigungen für zuständige Benutzer, zuständige Gruppe usw. hinzugefügt. Während dieser Standardwert 770 keine Berechtigungen von zuständigen Benutzern (7) oder zuständigen Gruppen (7) entfernt, entfernt er alle Berechtigungen aller anderen Benutzer (0). Dadurch kommt es zu einem bekannten Problem mit einem bestimmten Anwendungsfalls, das ausführlicher in Abschnitt [Bekannte Probleme und Umgehungen](#known-issues-and-workarounds) besprochen wird.

## <a name="known-issues-and-workarounds"></a>Bekannte Probleme und Umgehungen

In diesem Abschnitt werden die bekannten Probleme beim Verwenden von HDInsight mit Data Lake Storage und deren Umgehungen aufgelistet.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Öffentlich sichtbare lokalisierte Apache Hadoop-YARN-Ressourcen

Wenn ein neues Azure Data Lake Storage-Konto erstellt wird, wird das Stammverzeichnis automatisch mit auf 770 festgelegten Berechtigungen für die Zugriffs-ACL bereitgestellt. Der für den Stammordner zuständige Benutzer wird auf den Benutzer festgelegt, der das Konto erstellt hat (der Data Lake Storage-Administrator). Die zuständige Gruppe wird auf die primäre Gruppe des Benutzer festgelegt, der das Konto erstellt hat. „Andere“ erhalten keinen Zugriff.

Es ist bekannt, dass diese Einstellungen einen bestimmten Anwendungsfall von HDInsight beeinträchtigen. Dieser Fall wurde in [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247) erfasst. Autragsübermittlungen schlagen möglicherweise mit einer Fehlermeldung wie dieser fehl:

```output
Resource XXXX is not publicly accessible and as such cannot be part of the public cache.
```

Wie im bereits verlinkten JIRA von YARN angegeben, überprüft der Lokalisierer beim Lokalisieren von öffentlichen Ressourcen, dass die angeforderten Ressourcen auch tatsächlich öffentlich sind, indem er ihre Berechtigungen auf dem Remote-Dateisystem prüft. Jede lokale Ressource, die diese Bedingung nicht erfüllt, wird bei der Lokalisierung abgelehnt. Das Prüfen der Berechtigungen beinhaltet auch den Lesezugriff auf die Datei für „Andere“. Dieses Szenario ist nicht sofort einsatzbereit, wenn Sie HDInsight-Cluster in Azure Data Lake hosten, da Azure Data Lake den Zugriff auf „Andere“ auf der Ebene des Stammordner verweigert.

#### <a name="workaround"></a>Problemumgehung

Legen Sie die Lese- und Ausführberechtigungen für **andere** über die Hierarchie fest, beispielsweise über **/** , **/clusters** und **/clusters/finance** , wie in oben stehender Tabelle gezeigt.

## <a name="see-also"></a>Weitere Informationen

- [Schnellstart: Set up clusters in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) (Schnellstart: Einrichten von Clustern in HDInsight)
- [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](hdinsight-hadoop-use-data-lake-storage-gen2.md)