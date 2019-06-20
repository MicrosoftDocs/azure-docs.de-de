---
title: Synchronisieren von Azure Active Directory-Benutzern in einen Cluster – Azure HDInsight
description: Synchronisieren Sie authentifizierte Benutzer von Azure Active Directory in einen Cluster.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 2be67c604bebbe9b4c4356e241d1480ca0778d4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688549"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronisieren von Azure Active Directory-Benutzern in einen HDInsight-Cluster

[HDInsight-Cluster mit Enterprise-Sicherheitspaket (Enterprise Security Package, ESP)](hdinsight-domain-joined-introduction.md) können eine strenge Authentifizierung für Azure AD-Benutzer sowie Richtlinien für die *rollenbasierte Zugriffssteuerung* (Role-Based Access Control, RBAC) verwenden. Wenn Sie Benutzer und Gruppen zu Azure AD hinzufügen, können Sie die Benutzer synchronisieren, die Zugriff auf Ihren Cluster benötigen.

## <a name="prerequisites"></a>Voraussetzungen

[Erstellen Sie einen HDInsight-Cluster mit Enterprise-Sicherheitspaket](hdinsight-domain-joined-configure.md), wenn das noch nicht erfolgt ist.

## <a name="add-new-azure-ad-users"></a>Hinzufügen neuer Azure AD-Benutzer

Um Ihre Hosts anzuzeigen, öffnen Sie die Ambari-Webbenutzeroberfläche. Jeder Knoten wird mit neuen Einstellungen für das unbeaufsichtigte Upgrade aktualisiert.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem Azure AD-Verzeichnis, dem Ihr ESP-Cluster zugeordnet ist.

2. Wählen Sie im linken Menü den Eintrag **Alle Benutzer** aus, und wählen Sie dann **Neuer Benutzer** aus.

    ![Bereich „Alle Benutzer“](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Füllen Sie das Formular für neue Benutzer aus. Wählen Sie Gruppen aus, die Sie zum Zuweisen von clusterbasierten Berechtigungen erstellt haben. In diesem Beispiel erstellen Sie eine Gruppe namens „HiveUsers“, der Sie neue Benutzer zuweisen können. Die [Beispielanweisungen](hdinsight-domain-joined-configure.md) zum Erstellen eines ESP-Clusters umfassen das Hinzufügen von zwei Gruppen: `HiveUsers` und `AAD DC Administrators`.

    ![Bereich „Neuer Benutzer“](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Klicken Sie auf **Erstellen**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Verwenden der Apache Ambari-REST-API zum Synchronisieren von Benutzern

Benutzergruppen, die während des Clustererstellungsprozesses angegeben werden, werden zu diesem Zeitpunkt synchronisiert. Die Benutzersynchronisierung erfolgt automatisch einmal pro Stunde. Um die Benutzer sofort zu synchronisieren oder um eine andere Gruppe als die während der Clustererstellung angegebenen Gruppen zu synchronisieren, verwenden Sie die Ambari-REST-API.

Die folgende Methode verwendet POST mit der Ambari-REST-API. Weitere Informationen finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Stellen Sie über SSH eine Verbindung mit Ihrem Cluster her](hdinsight-hadoop-linux-use-ssh-unix.md). Klicken Sie im Azure-Portal im Übersichtsbereich für Ihren Cluster auf die Schaltfläche **Secure Shell (SSH)** .

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Kopieren Sie den angezeigten `ssh`-Befehl, und fügen Sie ihn in Ihrem SSH-Client ein. Geben Sie bei Aufforderung das SSH-Benutzerkennwort ein.

3. Geben Sie nach der Authentifizierung den folgenden Befehl ein:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    Das Ergebnis sieht in etwa wie folgt aus:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Führen Sie einen neuen `curl`-Befehl aus, um den Synchronisierungsstatus anzuzeigen:

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    Das Ergebnis sieht in etwa wie folgt aus:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

5. Dieses Ergebnis zeigt, dass der Status **ABGESCHLOSSEN** lautet, ein neuer Benutzer erstellt wurde und dem Benutzer eine Mitgliedschaft zugewiesen wurde. In diesem Beispiel wird der Benutzer der synchronisierten LDAP-Gruppe „HiveUsers“ zugewiesen, da der Benutzer in Azure AD zu genau dieser Gruppe hinzugefügt wurde.

> [!NOTE]  
> Die vorherige Methode synchronisiert nur die Azure AD-Gruppen, die während der Clustererstellung in der Eigenschaft **Zugriff auf die Benutzergruppe** der Domäneneinstellungen angegeben wurden. Weitere Informationen finden Sie unter [Erstellen eines HDInsight-Clusters](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Überprüfen des neu hinzugefügten Azure AD-Benutzers

Öffnen Sie die [Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md), um zu überprüfen, ob der neue Azure AD-Benutzer hinzugefügt wurde. Sie können über **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** auf die Ambari-Webbenutzeroberfläche zugreifen. Geben Sie den Benutzernamen und das Kennwort des Clusteradministrators ein.

1. Wählen Sie im Ambari-Dashboard im Menü **Administrator** die Option **Ambari verwalten** aus.

    ![Ambari verwalten](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Wählen Sie in der Menügruppe **Benutzer- und Gruppenverwaltung** auf der linken Seite die Option **Benutzer** aus.

    ![Menüelement „Benutzer“](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Der neue Benutzer sollte in der Tabelle „Benutzer“ aufgeführt werden. Der Typ sollte auf `LDAP` festgelegt sein, nicht auf `Local`.

    ![Seite „Benutzer“](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Anmelden bei Ambari als neuer Benutzer

Wenn der neue Benutzer (oder ein anderer Domänenbenutzer) sich bei Ambari anmeldet, verwendet er seinen vollständigen Azure AD-Benutzernamen und die Domänenanmeldeinformationen.  Ambari zeigt einen Benutzeralias an, bei dem es sich um den Anzeigenamen des Benutzers in Azure AD handelt. Der neue Beispielbenutzer hat den Benutzernamen `hiveuser3@contoso.com`. In Ambari wird dieser neue Benutzer als `hiveuser3` angezeigt, der Benutzer muss sich jedoch als `hiveuser3@contoso.com` bei Ambari anmelden.

## <a name="see-also"></a>Weitere Informationen

* [Konfigurieren von Apache Hive-Richtlinien in HDInsight mit ESP](hdinsight-domain-joined-run-hive.md)
* [Verwalten von HDInsight-Clustern mit ESP](hdinsight-domain-joined-manage.md)
* [Autorisieren von Benutzern für Apache Ambari](hdinsight-authorize-users-to-ambari.md)
