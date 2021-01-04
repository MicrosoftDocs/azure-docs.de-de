---
title: LDAP-Synchronisierung in Ranger und Apache Ambari in Azure HDInsight
description: Hier werden die LDAP-Synchronisierung in Ranger und Ambari behandelt und allgemeine Richtlinien bereitgestellt.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0779ac261fbb4ee91bf63021bb0cc685a371c2b2
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234068"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-Synchronisierung in Ranger und Apache Ambari in Azure HDInsight

Cluster des HDInsight-Enterprise-Sicherheitspakets (ESP) verwenden Ranger für die Autorisierung. Mit Apache Ambari und Ranger werden Benutzer und Gruppen unabhängig voneinander synchronisiert, und beide funktionieren ein wenig anders. In diesem Artikel wird die LDAP-Synchronisierung in Ranger und Ambari behandelt.

## <a name="general-guidelines"></a>Allgemeine Richtlinien

* Stellen Sie Cluster immer mit einer oder mehreren Gruppen bereit.
* Wenn Sie weitere Gruppen im Cluster verwenden möchten, prüfen Sie, ob es sinnvoll ist, die Gruppenmitgliedschaften im Azure Active Directory (Azure AD) zu aktualisieren.
* Wenn Sie die Clustergruppen ändern möchten, können Sie die Synchronisierungsfilter mithilfe von Ambari ändern.
* Alle Änderungen der Gruppenmitgliedschaft in Azure AD spiegeln sich im Cluster in den nachfolgenden Synchronisierungen wider. Die Änderungen müssen zuerst mit Azure AD Domain Services (Azure AD DS) und dann mit den Clustern synchronisiert werden.
* HDInsight-Cluster verwenden Samba/Winbind, um die Gruppenmitgliedschaften auf die Clusterknoten zu projizieren.
* Gruppenmitglieder werden transitiv (alle Untergruppen und deren Mitglieder) sowohl mit Ambari als auch mit Ranger synchronisiert. 

## <a name="users-are-synced-separately"></a>Benutzer werden getrennt synchronisiert

 * Ambari und Ranger nutzen die Benutzerdatenbank nicht gemeinsam, da Sie zwei unterschiedlichen Zwecken dienen. 
   * Wenn ein Benutzer die Ambari-Benutzeroberfläche verwenden muss, muss er mit Ambari synchronisiert werden. 
   * Ist der Benutzer nicht mit Ambari synchronisiert, wird er von der Ambari-Benutzeroberfläche/API abgelehnt, doch andere Teile des Systems funktionieren (diese werden von Ranger oder Resource Manager und nicht von Ambari geschützt).
   * Um Benutzer oder Gruppen in Ranger-Richtlinien einzubeziehen, müssen die Prinzipale in Ranger explizit synchronisiert werden.

## <a name="ambari-user-sync-and-configuration"></a>Synchronisierung und Konfiguration von Ambari-Benutzern

Auf den Hauptknoten wird stündlich ein Cron-Auftrag (`/opt/startup_scripts/start_ambari_ldap_sync.py`) ausgeführt, um die Benutzersynchronisierung zu planen. Der Cron-Auftrag ruft die Ambari-REST-APIs zum Durchführen der Synchronisierung auf. Das Skript sendet eine Liste von Benutzern und Gruppen, die synchronisiert werden sollen (da die Benutzer möglicherweise nicht den angegebenen Gruppen angehören, werden beide einzeln angegeben). Ambari synchronisiert sAMAccountName als den Benutzernamen und alle Gruppenmitglieder transitiv.

Die Protokolle müssen sich in `/var/log/ambari-server/ambari-server.log` befinden. Weitere Informationen finden Sie im Artikel zum [Konfigurieren des Ambari-Protokolliergrads](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

In Data Lake-Clustern werden mit dem Hook nach der Benutzererstellung die Basisordner für die synchronisierten Benutzer erstellt und diese werden als Besitzer der Basisordner festgelegt. Wenn der Benutzer nicht ordnungsgemäß mit Ambari synchronisiert wird, kann es bei aktiven Aufträgen zu Fehlern kommen, da der Basisordner möglicherweise nicht ordnungsgemäß eingerichtet ist.

## <a name="ranger-user-sync-and-configuration"></a>Synchronisierung und Konfiguration von Ranger-Benutzern

Ranger verfügt über ein integriertes Synchronisierungsmodul, das stündlich zur Synchronisierung der Benutzer ausgeführt wird. Die Benutzerdatenbank wird nicht mit Ambari gemeinsam genutzt. HDInsight konfiguriert den Suchfilter so, dass der Administratorbenutzer, der Watchdog-Benutzer und die Mitglieder der Gruppe, die während der Clustererstellung angegeben wird, synchronisiert werden. Die Gruppenmitglieder werden transitiv synchronisiert:

1. Deaktivieren Sie die inkrementelle Synchronisierung.
1. Aktivieren Sie die Zuordnung zur Benutzergruppensynchronisierung.
1. Geben Sie den Suchfilter an, um die transitiven Gruppenmitglieder einzubeziehen.
1. Synchronisieren Sie das Attribut „sAMAccountName“ für Benutzer und das Namensattribut für Gruppen.

### <a name="group-or-incremental-sync"></a>Gruppen- oder inkrementelle Synchronisierung

Ranger unterstützt eine Option zur Gruppensynchronisierung, aber sie funktioniert als Schnittmenge mit dem Benutzerfilter, nicht als Vereinigung zwischen Gruppenmitgliedschaften und Benutzerfilter. Ein typischer Anwendungsfall für den Gruppensynchronisierungsfilter in Ranger ist Gruppenfilter: (dn=clusteradmingroup), Benutzerfilter: (city=seattle).

Die inkrementelle Synchronisierung funktioniert nur für die Benutzer, die bereits synchronisiert wurden (zum ersten Mal). Die inkrementelle Synchronisierung erfolgt nicht für neue Benutzer, die den Gruppen nach der ersten Synchronisierung hinzugefügt wurden.

### <a name="update-ranger-sync-filter"></a>Aktualisieren des Ranger-Synchronisierungsfilters

Der LDAP-Filter befindet sich auf der Ambari-Benutzeroberfläche im Abschnitt mit der Konfiguration der Ranger-Benutzersynchronisierung. Der vorhandene Filter hat die Form `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`. Stellen Sie sicher, dass Sie ein Prädikat am Ende hinzufügen, und testen Sie den Filter mithilfe des Suchbefehls `net ads`, mit ldp.exe oder etwas Ähnlichem.

## <a name="ranger-user-sync-logs"></a>Protokolle der Ranger-Benutzersynchronisierung

Die Ranger-Benutzersynchronisierung kann über einen der Hauptknoten erfolgen. Die Protokolle befinden sich in `/var/log/ranger/usersync/usersync.log`. Führen Sie die folgenden Schritte aus, um den Ausführlichkeitsgrad der Protokolle zu erhöhen:

1. Melden Sie sich bei Ambari an.
1. Wechseln Sie zum Abschnitt mit der Ranger-Konfiguration.
1. Wechseln Sie zum erweiterten Abschnitt **usersync-log4j**.
1. Ändern Sie die Ebene `log4j.rootLogger` in `DEBUG`. (Nach der Änderung sollte sie wie `log4j.rootLogger = DEBUG,logFile,FilterLog` aussehen).
1. Speichern Sie die Konfiguration, und starten Sie Ranger neu.

## <a name="known-issues-with-ranger-user-sync"></a>Bekannte Probleme bei der Synchronisierung von Ranger-Benutzern
* Wenn der Gruppenname Unicode-Zeichen enthält, kann die Ranger-Synchronisierung dieses Objekt nicht synchronisieren. Wenn ein Benutzer zu einer Gruppe gehört, die internationale Zeichen enthält, synchronisiert Ranger die Gruppenzugehörigkeit teilweise.
* Benutzername (sAMAccountName) und Gruppenname (Name) dürfen maximal 20 Zeichen lang sein. Wenn der Gruppenname länger ist, wird der Benutzer bei der Berechnung der Berechtigungen so behandelt, als gehöre er nicht zu der Gruppe.

## <a name="next-steps"></a>Nächste Schritte

* [Authentifizierungsprobleme in Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synchronisieren von Azure AD-Benutzern mit einem HDInsight-Cluster](../hdinsight-sync-aad-users-to-cluster.md)
