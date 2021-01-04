---
title: Aktivieren von Synapse-Arbeitsbereichsfunktionen
description: In diesem Dokument wird beschrieben, wie ein Benutzer die Funktionen eines Synapse-Arbeitsbereichs in einem vorhandenen dedizierten SQL-Pool (ehemals SQL DW) aktivieren kann.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: a1fbc6eede6c82020b765185602c672c1162fdf8
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548187"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Aktivieren von Synapse-Arbeitsbereichsfunktionen für einen dedizierten SQL-Pool (ehemals SQL DW)

Alle SQL Data Warehouse-Benutzer können jetzt über Synapse Studio und den Arbeitsbereich auf eine vorhandene Instanz eines dedizierten SQL-Pools (ehemals SQL DW) zugreifen und diese verwenden. Benutzer können Synapse Studio und den Arbeitsbereich verwenden, ohne dass dies Auswirkungen auf die Automatisierung, Verbindungen oder Tools hat. In diesem Artikel wird erläutert, wie ein vorhandener Azure Synapse Analytics-Benutzer die Funktionen eines Synapse-Arbeitsbereichs für einen vorhandenen dedizierten SQL-Pool (ehemals SQL DW) aktivieren kann. Benutzer können mit den neuen Funktionen, die jetzt über den Synapse-Arbeitsbereich und in Studio verfügbar sind, eine vorhandene Analyselösung erweitern.   

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie die Funktionen des Synapse-Arbeitsbereichs in Ihrem Data Warehouse aktivieren, müssen Sie über Folgendes verfügen:
- Rechte zum Erstellen und Verwalten der SQL-Ressourcen, die auf dem logischen SQL-Server gehostet werden.
- Rechte zum Erstellen von Azure Synapse-Ressourcen.
- Einen auf dem logischen Server bekannten Azure Active Directory-Administrator

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Aktivieren von Synapse-Arbeitsbereichsfunktionen für einen vorhandenen dedizierten SQL-Pool (ehemals SQL DW)

Die Funktionen von Synapse-Arbeitsbereichen können für jeden vorhandenen dedizierten SQL-Pool (ehemals SQL DW) in einer unterstützten Region aktiviert werden. Diese Funktion ist nur über das Azure-Portal verfügbar.

Führen Sie die folgenden Schritte aus, um einen Synapse-Arbeitsbereich für Ihr vorhandenes Data Warehouse zu erstellen.
1. Wählen Sie einen vorhandenen dedizierten SQL-Pool (ehemals SQL DW) aus, und öffnen Sie die Übersichtsseite.
2. Wählen Sie auf der oberen Menüleiste oder in der Meldung direkt darunter **Neuen Synapse-Arbeitsbereich** aus.
3. Wählen Sie nach dem Überprüfen der Liste mit den verfügbaren Data Warehouses, die über den neuen Synapse-Arbeitsbereich verfügbar gemacht werden, auf der Seite **Create new Azure Synapse workspace** (Neuen Azure Synapse-Arbeitsbereich erstellen) die Option **Fortsetzen** aus, um fortzufahren.
4. Auf der Seite „Grundlagen“ für den vorhandenen dedizierten SQL-Pool sollte der Abschnitt mit Details zum **Projekt** bereits mit demselben **Abonnement** und derselben **Ressourcengruppe** ausgefüllt sein, die unter dem logischen Server bereitgestellt werden. Außerdem ist unter **Informationen zum Arbeitsbereich** der **Arbeitsbereich** mit demselben Namen und derselben Region wie der logische SQL-Server ausgefüllt, um sicherzustellen, dass während des Bereitstellungsprozesses die Verbindung zwischen dem neuen Synapse-Arbeitsbereich und dem logischen Server hergestellt werden kann. Diese Verbindung muss nach der Bereitstellung aufrechterhalten werden, um den dauerhaften Zugriff auf die Instanzen des dedizierten SQL-Pools (ehemals SQL DW) über den Arbeitsbereich und Studio sicherzustellen.
5. Navigieren Sie zu Data Lake Storage Gen 2 auswählen.
6. Wählen Sie zunächst **Neu erstellen** oder eine vorhandene **Data Lake Storage Gen2**-Instanz und dann **Weiter: Netzwerk** aus.
7. Wählen Sie ein **SQL-Administratorkennwort** für die **serverlose Instanz** aus. Durch das Ändern dieses Kennworts werden die SQL-Anmeldeinformationen des logischen Servers nicht aktualisiert oder geändert. Wenn Sie ein von System definiertes Kennwort bevorzugen, lassen Sie diese Felder leer. Dieses Kennwort kann jederzeit im neuen Arbeitsbereich geändert werden. Der Administratorname muss mit dem in SQL Server identisch sein.
8. Wählen Sie die gewünschten **Netzwerkeinstellungen** und dann **Überprüfen + erstellen** aus, um die Bereitstellung des Arbeitsbereichs zu starten.
9. Wählen Sie **Goto resource** (Zu Ressource wechseln) aus, um den neuen Arbeitsbereich zu öffnen.

    > [!NOTE]
    > Alle Instanzen von dedizierten SQL-Pools (ehemals SQL DW), die auf dem logischen Server gehostet werden, sind über den neuen Arbeitsbereich verfügbar.

## <a name="post-provisioning-steps"></a>Nach der Bereitstellung auszuführende Schritte
Die folgenden Schritte müssen ausgeführt werden, um sicherzustellen, dass auf Ihre vorhandenen Instanzen des dedizierten SQL-Pools (ehemals SQL DW) über Synapse Studio zugegriffen werden kann.
1. Wählen Sie auf der Übersichtsseite für den Synapse-Arbeitsbereich **Verbundener Server** aus. Über **Verbundener Server** gelangen Sie zum verbundenen logischen SQL-Server, auf dem Ihre Data Warehouses gehostet werden. Wählen Sie im Menü „Grundlagen“ die Option **Verbundener Server** aus.
2. Öffnen Sie **Firewalls und virtuelle Netzwerke**, und stellen Sie sicher, dass Ihre Client-IP-Adresse oder ein vordefinierter IP-Adressbereich Zugriff auf den logischen Server hat.
3. Öffnen Sie **Active Directory-Administrator**, und stellen Sie sicher, dass auf dem logischen Server ein AAD-Administrator festgelegt wurde.
4. Wählen Sie eine Instanz des dedizierten SQL-Pools (ehemals SQL DW) aus, die auf dem logischen Server gehostet wird. Wählen Sie auf der Seite Übersicht **Synapse Studio starten** aus, oder navigieren Sie zu [Sign in to the Synapse Studio](https://web.azuresynapse.net) (Bei Synapse Studio anmelden), und melden Sie sich bei Ihrem Arbeitsbereich an.

5. Öffnen Sie den **Datenhub**, und erweitern Sie den dedizierten SQL-Pool im Objekt-Explorer. Damit überprüfen Sie, ob Sie auf Ihr Data Warehouse zugreifen und es abfragen können.

## <a name="next-steps"></a>Nächste Schritte
Machen Sie sich mit [Synapse-Arbeitsbereich und Studio](../get-started.md) vertraut.
