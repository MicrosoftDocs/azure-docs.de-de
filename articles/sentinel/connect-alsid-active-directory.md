---
title: Verbinden von Alsid for Active Directory mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Alsid for Active Directory-Connector zum Pullen von Alsid-Protokollen in Azure Sentinel verwenden. Zeigen Sie Alsid-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566576"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Verbinden von Alsid for Active Directory (AD) mit Azure Sentinel

> [!IMPORTANT]
> Der Alsid for Active Directory-Connector befindet sich derzeit in der **Vorschauphase**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

In diesem Artikel wird erläutert, wie Sie Ihre Alsid for AD-Lösung mit Azure Sentinel verbinden. Mit dem Alsid for Active Directory-Datenconnector lassen sich Ihre Alsid for AD-Protokolle problemlos mit Azure Sentinel verbinden, sodass Sie die Daten dann in Arbeitsmappen anzeigen, zum Erstellen benutzerdefinierter Warnungen abfragen und zur Verbesserung der Untersuchung von Problemen verwenden können. Bei der Integration von Alsid for AD und Azure Sentinel wird ein Syslog-Server verwendet, auf dem der Log Analytics-Agent installiert ist. Außerdem wird ein benutzerdefinierter Protokollparser verwendet, der auf einer Kusto-Funktion basiert.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Schreibzugriff auf den Azure Sentinel-Arbeitsbereich.

- Ihre Alsid for AD-Lösung muss so konfiguriert sein, dass Protokolle über Syslog exportiert werden.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Senden von Alsid for AD-Protokollen an Azure Sentinel über den Syslog-Agent

Konfigurieren Sie Alsid for AD für das Weiterleiten von Syslog-Nachrichten an Ihren Azure Sentinel-Arbeitsbereich über den Syslog-Agent.

1. Wählen Sie im Azure Sentinel-Navigationsmenü **Datenconnectors** aus.

1. Wählen Sie im **Datenconnectors**-Katalog den Connector **Alsid for Active Directory (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Befolgen Sie die Anweisungen auf der Seite des **Alsid for Active Directory**-Connectors:

    1. Konfigurieren eines Syslog-Servers

        1. Falls Sie noch keinen Linux-Syslog-Server haben, an den Alsid for AD Protokolle senden kann, erstellen Sie einen. Azure Sentinel unterstützt die Daemons **rsyslog** und **syslog-ng**. 

        1. Konfigurieren Sie Ihren Syslog-Server so, dass Alsid for AD-Protokolle in einer separaten Datei ausgegeben werden.

    1. Konfigurieren von Alsid for AD zum Senden von Protokollen an Ihren Syslog-Server

        1. Wechseln Sie im **Alsid for AD**-Portal zu *System*, *Konfiguration* und dann *Syslog*. Dort können Sie eine neue Syslog-Warnung für den Syslog-Server erstellen. Verwenden Sie für den Remoteserver die IP-Adresse des Linux-Computers, auf dem Sie den Linux-Agent installiert haben.

        1. Überprüfen Sie, ob die Protokolle auf dem Server ordnungsgemäß in einer separaten Datei gesammelt werden. Dazu können Sie die Schaltfläche **Test the configuration** (Konfiguration testen) in der *Syslog*-Warnungskonfiguration in Alsid for AD verwenden.

    1. Installieren und Integrieren des Log Analytics-Agents für Linux

        - Wählen Sie eine Azure Linux-VM oder einen Nicht-Azure-Linux-Computer (physisch oder virtuell) aus. Folgen Sie den Links und Anweisungen auf dem Bildschirm. Weitere Informationen finden Sie unter [Konfigurieren Ihres Computers oder Ihrer Appliance unter Linux](connect-syslog.md#configure-your-linux-machine-or-appliance).

    1. Konfigurieren der Protokolle, die vom Log Analytics-Agent gesammelt werden sollen

        - Wählen Sie die Einrichtungen und Schweregrade in der Konfiguration der erweiterten Einstellungen für den Arbeitsbereich aus.

            1. Klicken Sie auf der Connectorseite auf den Link zum **Öffnen der Konfiguration der erweiterten Einstellungen Ihres Arbeitsbereichs**.

            1. Wählen Sie auf dem Bildschirm **Erweiterte Einstellungen** die Option **Daten** und dann **Benutzerdefinierte Protokolle** aus.

            1. Aktivieren Sie das Kontrollkästchen **Nachstehende Konfiguration auf meine Linux-Computer anwenden**, und klicken Sie auf **Hinzufügen**.

            1. Klicken Sie auf **Datei auswählen**, um eine Syslog-Beispieldatei für Alsid for AD vom Linux-Computer mit dem Syslog-Server hochzuladen, und klicken Sie auf **Weiter**.

            1. Überprüfen Sie, ob **Set record delimiter** (Datensatztrennzeichen festlegen) auf **Neue Zeile** festgelegt ist, und klicken Sie auf **Weiter**.

            1. Wählen Sie **Linux** aus, geben Sie den Dateipfad zur Syslog-Datei ein, klicken Sie auf **+** und dann auf **Weiter**.

            1. Geben Sie im Feld „Name“ *AlsidForADLog* vor dem Suffix „_CL“ ein, und klicken Sie dann auf **Fertig**.
    
Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine erfolgreiche Verbindung hergestellt wurde, werden die Daten in **Protokolle** unter **Benutzerdefinierte Protokolle** in der Tabelle *AlsidForADLog_CL* angezeigt.

Dieser Datenconnector ist von einem Parser abhängig, der auf einer Kusto-Funktion basiert und erwartungsgemäß funktionieren muss. Führen Sie die folgenden Schritte aus, um die Kusto-Funktion **afad_parser** einzurichten, die in Abfragen und Arbeitsmappen verwendet wird.

1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Protokolle** aus.

1. Kopieren Sie die folgende Abfrage, und fügen Sie sie in das Abfragefenster ein.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Klicken Sie auf die Dropdownliste **Speichern** und dann auf **Speichern**. Führen Sie im Bereich **Speichern** folgende Aktionen aus:

    1. Geben Sie unter **Name** die Zeichenfolge **afad_parser** ein.

    1. Wählen Sie unter **Speichern unter** die Option **Funktion** aus.

    1. Geben Sie unter **Funktionsalias** die Zeichenfolge **afad_parser** ein.

    1. Geben Sie **Functions** unter **Kategorie** ein.

    1. Klicken Sie auf **Speichern**.

    Die Aktivierung von Funktions-Apps dauert in der Regel zwischen 10 und 15 Minuten.

Nun sind Sie bereit, die Alsid for AD-Daten abzufragen, indem Sie `afad_parser` in der obersten Zeile des Abfragefensters eingeben.

Weitere hilfreiche Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie Alsid for AD mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
