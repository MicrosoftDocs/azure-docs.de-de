---
title: Einrichten kundenseitig verwalteter Schlüssel in Azure Sentinel | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie kundenseitig verwaltete Schlüssel (Customer-Managed Keys, CMK) in Azure Sentinel einrichten.
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
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: bd85936c86656a8ae59a04ccdf53a62bd852368f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655271"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Einrichten kundenseitig verwalteter Schlüssel in Azure Sentinel

Dieser Artikel enthält Hintergrundinformationen und Schritte zum Konfigurieren eines kundenseitig verwalteten Schlüssels (Customer-Managed Key, CMK) für Azure Sentinel. Mit einem kundenseitig verwalteten Schlüssel können alle gespeicherten oder an Azure Sentinel gesendeten Daten in allen relevanten Speicherressourcen mit einem von Ihnen erstellten oder in Ihrem Besitz befindlichen Azure Key Vault-Schlüssel verschlüsselt werden.

> [!NOTE]
> - Die CMK-Funktion in Azure Sentinel steht nur für **neue Kunden** zur Verfügung.
>
> - Der Zugriff auf diese Funktion wird über die Azure-Featureregistrierung gesteuert.  Wenden Sie sich an azuresentinelCMK@microsoft.com, um Zugriff zu erhalten. Ausstehende Anfragen werden entsprechend verfügbaren Kapazitäten genehmigt.
>
> - Die CMK-Funktion ist nur für Kunden mit einem täglichen Sendevolumen von mindestens 1 TB verfügbar. Informationen zu zusätzlichen Preisen erhalten Sie, wenn Sie bei Microsoft die Bereitstellung von CMK in Ihrem Azure-Abonnement beantragen. Weitere Informationen zu den Preisen für Log Analytics finden Sie [hier](../azure-monitor/platform/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="how-cmk-works"></a>Funktionsweise von CMK 

Die Azure Sentinel-Lösung nutzt eine Reihe von Speicherressourcen für die Protokollerfassung und Features, einschließlich Log Analytics und anderer Ressourcen. Im Rahmen der Azure Sentinel-CMK-Konfiguration müssen auch die CMK-Einstellungen für die zugehörigen Speicherressourcen konfiguriert werden. In Log Analytics-fremden Speicherressourcen gespeicherte Daten werden ebenfalls verschlüsselt.

Weitere Informationen über [CMK](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-overview).

> [!NOTE]
> Wenn Sie CMK in Azure Sentinel aktivieren, werden Public Preview-Features ohne CMK-Unterstützung nicht aktiviert.

## <a name="enable-cmk"></a>Aktivieren von CMK 

Gehen Sie zum Bereitstellen von CMK wie folgt vor: 

1.  Erstellen Sie eine Azure Key Vault-Instanz, und speichern Sie einen Schlüssel.

2.  Aktivieren Sie CMK in Ihrem Log Analytics-Arbeitsbereich.

3.  Führen Sie eine Cosmos DB-Registrierung durch.

4.  Fügen Sie Ihrer Azure Key Vault-Instanz eine Zugriffsrichtlinie hinzu.

5.  Aktivieren Sie CMK in Azure Sentinel.

6.  Aktivieren Sie Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>SCHRITT 1: Erstellen einer Azure Key Vault-Instanz und Speichern eines Schlüssels

1.  [Erstellen Sie eine Azure Key Vault-Ressource](/azure-stack/user/azure-stack-key-vault-manage-portal), und generieren oder importieren Sie einen Schlüssel für die Datenverschlüsselung.
    > [!NOTE]
    >  Azure Key Vault muss als wiederherstellbar konfiguriert werden, um Ihren Schlüssel und den Zugriff zu schützen.

1.  [Aktivieren Sie die Wiederherstellungsoptionen:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Stellen Sie sicher, dass [Vorläufiges Löschen](../key-vault/general/soft-delete-overview.md) aktiviert ist.

    -   Aktivieren Sie den [Bereinigungsschutz](../key-vault/general/soft-delete-overview.md#purge-protection) zum Schutz vor erzwungenem Löschen des Geheimnisses/Tresors (auch nach vorläufigem Löschen).

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>SCHRITT 2: Aktivieren von CMK in Ihrem Log Analytics-Arbeitsbereich

Gehen Sie wie unter [Konfiguration kundenseitig verwalteter Schlüssel in Azure Monitor](../azure-monitor/platform/customer-managed-keys.md) beschrieben vor, um einen CMK-Arbeitsbereich zu erstellen, der in den folgenden Schritten als Azure Sentinel-Arbeitsbereich verwendet wird.

### <a name="step-3-register-for-cosmos-db"></a>SCHRITT 3: Registrieren für Cosmos DB

Azure Sentinel arbeitet mit Cosmos DB als zusätzliche Speicherressource. Führen Sie eine Cosmos DB-Registrierung durch.

[Registrieren Sie den Azure Cosmos DB-Ressourcenanbieter](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) für Ihr Azure-Abonnement gemäß der Cosmos DB-Anleitung.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>SCHRITT 4: Hinzufügen einer Zugriffsrichtlinie zu Ihrer Azure Key Vault-Instanz

Stellen Sie sicher, dass von Cosmos DB aus auf Ihre Azure Key Vault-Instanz zugegriffen werden kann. [Fügen Sie Ihrer Azure Key Vault-Instanz eine Zugriffsrichtlinie mit Azure Cosmos DB-Prinzipal hinzu](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance), wie in der Cosmos DB-Anleitung beschrieben.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>SCHRITT 5: Aktivieren von CMK in Azure Sentinel

Die CMK-Funktion von Azure Sentinel wird nur für neue Kunden bereitgestellt, denen direkt von der Azure-Produktgruppe Zugriff gewährt wurde. Wenden Sie sich an Ihre Ansprechpartner bei Microsoft, um vom Azure Sentinel-Team die Genehmigung zur Aktivierung von CMK in Ihrer Lösung zu erhalten.

Nachdem Sie die Genehmigung erhalten haben, werden Sie zur Angabe folgender Informationen aufgefordert, um die CMK-Funktion zu aktivieren.

-  Arbeitsbereichs-ID, für die Sie CMK aktivieren möchten

-  Key Vault-URL: Kopieren Sie den Schlüsselbezeichner des Schlüssels bis zum letzten Schrägstrich:  
    

    ![Schlüsselbezeichner](./media/customer-managed-keys/key-identifier.png)

    Das Azure Sentinel-Team aktiviert das CMK-Feature von Azure Sentinel für den angegebenen Arbeitsbereich.

-  Nachweis vom Azure Sentinel-Produktteam, dass die Verwendung des Features genehmigt wurde. Dies ist erforderlich, um den Vorgang fortsetzen zu können.

### <a name="step-6-enable-azure-sentinel"></a>SCHRITT 6: Aktivieren von Azure Sentinel


Aktivieren Sie Azure Sentinel im Azure-Portal für den Arbeitsbereich, für den Sie CMK einrichten. Weitere Informationen finden Sie unter [Schnellstart: Ausführen des Onboardings für Azure Sentinel](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Wiederruf oder Löschung von Schlüsselverschlüsselungsschlüsseln


Wenn ein Benutzer den Schlüsselverschlüsselungsschlüssel widerruft, indem er ihn entweder löscht oder den Zugriff für Azure Sentinel entfernt, berücksichtigt Azure Sentinel diese Änderung binnen einer Stunde und verhält sich so, als wären die Daten nicht mehr verfügbar. Daraufhin werden alle Vorgänge, für die beständige Speicherressourcen verwendet werden (beispielsweise Datenerfassung, beständige Konfigurationsänderungen und Incident-Erstellung), unterbunden. Zuvor gespeicherte Daten werden zwar nicht gelöscht, sind aber nicht mehr zugänglich. Daten, auf die nicht zugegriffen werden kann, unterliegen der Datenaufbewahrungsrichtlinie und werden gemäß dieser Richtlinie bereinigt.

Die Kontolöschung ist der einzige Vorgang, der nach dem Widerruf des Verschlüsselungsschlüssels noch möglich ist.

Wird der Zugriff nach dem Widerruf wiederhergestellt, stellt Azure Sentinel den Zugriff auf die Daten binnen einer Stunde wieder her.

Weitere Informationen zur Funktionsweise in Azure Monitor finden Sie unter [CMK-Widerruf (KEK)](../azure-monitor/platform/customer-managed-keys.md#key-revocation).

## <a name="key-encryption-key-rotation"></a>Rotation des Schlüsselverschlüsselungsschlüssels


Die Schlüsselrotation wird von Azure Sentinel und Log Analytics unterstützt. Wenn ein Benutzer eine Schlüsselrotation in Key Vault vornimmt, wird der neue Schlüssel binnen einer Stunde von Azure Sentinel unterstützt.

In Key Vault können Sie zur Schlüsselrotation eine neue Version des Schlüssels erstellen:

![Schlüsselrotation](./media/customer-managed-keys/key-rotation.png)

Sie können die vorherige Version des Schlüssels nach 24 Stunden deaktivieren oder nachdem in den Azure Key Vault-Überwachungsprotokollen keine Aktivität mit der alten Version mehr vorkommen.

Wenn Sie in Azure Sentinel und Log Analytics den gleichen Schlüssel verwenden, muss die Clusterressource in Log Analytics für die Schlüsselrotation explizit mit der neuen Azure Key Vault-Schlüsselversion aktualisiert werden. Weitere Informationen finden Sie unter [CMK-Rotation (KEK)](../azure-monitor/platform/customer-managed-keys.md#key-rotation).

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie gelernt, wie Sie einen kundenseitig verwalteten Schlüssel in Azure Sentinel einrichten. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.