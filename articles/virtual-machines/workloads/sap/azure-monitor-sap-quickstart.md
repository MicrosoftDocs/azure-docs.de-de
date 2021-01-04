---
title: Bereitstellen von Azure Monitor für SAP-Lösungen mit dem Azure-Portal
description: Bereitstellen von Azure Monitor für SAP-Lösungen mit dem Azure-Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 08/17/2020
ms.reviewer: cynthn
ms.openlocfilehash: c3b3848e4e4f7b0445f882265dbe66bb10b48833
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968586"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Bereitstellen von Azure Monitor für SAP-Lösungen mit dem Azure-Portal

Ressourcen für Azure Monitor für SAP-Lösungen können über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal) erstellt werden. Diese Methode bietet eine browserbasierte Benutzeroberfläche zum Bereitstellen von Azure Monitor für SAP-Lösungen und zum Konfigurieren von Anbietern.

## <a name="sign-in-to-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-monitoring-resource"></a>Erstellen einer Überwachungsressource

1. Wählen Sie im **Azure Marketplace** das Angebot **Azure Monitor für SAP-Lösungen** aus.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Abbildung zum Auswählen des Angebots „Azure Monitor für SAP-Lösungen“ im Azure Marketplace" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Geben Sie auf der Registerkarte **Grundlagen** die erforderlichen Werte ein. Gegebenenfalls können Sie einen vorhandenen Log Analytics-Arbeitsbereich verwenden.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Anzeige der Konfigurationsoptionen im Azure-Portal" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Stellen Sie bei Auswahl eines virtuellen Netzwerks sicher, dass die Systeme, die Sie überwachen möchten, innerhalb des VNET erreichbar sind. 

   > [!IMPORTANT]
   > Wenn Sie **Freigeben** zur Datenfreigabe für Microsoft auswählen, können unsere Supportteams zusätzlichen Support bereitstellen.

## <a name="configure-providers"></a>Konfigurieren von Anbietern

### <a name="sap-hana-provider"></a>SAP HANA-Anbieter 

1. Wählen Sie die Registerkarte **Anbieter** aus, um die Anbieter hinzuzufügen, die Sie konfigurieren möchten. Sie können nacheinander mehrere Anbieter hinzufügen oder Anbieter nach der Bereitstellung der Überwachungsressource hinzufügen. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Anzeige der Registerkarte „Anbieter“ zum Hinzufügen weiterer Anbieter zu Azure Monitor für SAP-Lösungen" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Wählen Sie **Anbieter hinzufügen** und dann in der Dropdownliste den Eintrag **SAP HANA** aus. 

   > [!IMPORTANT]
   > Stellen Sie sicher, dass der SAP HANA-Anbieter für den SAP HANA-Knoten „Master“ konfiguriert ist.

3. Geben Sie die private IP-Adresse für den HANA-Server ein.

4. Geben Sie den Namen des Datenbankmandanten ein, den Sie verwenden möchten. Sie können einen beliebigen Mandanten auswählen, doch wird die Verwendung von **SYSTEMDB** empfohlen, da dieser umfassendere Überwachungsbereiche ermöglicht. 

5. Geben Sie die mit Ihrer HANA-Datenbank verknüpfte SQL-Portnummer ein. Die Portnummer sollte das Format **[3]**  +  **[Instanznr.]**  +  **[13]** aufweisen. Beispiel: 30013. 

6. Geben Sie den Datenbankbenutzernamen ein, den Sie verwenden möchten. Stellen Sie sicher, dass dem Datenbankbenutzer die Rollen für **Überwachung** und **Katalog lesen** zugewiesen sind. 

7. Wählen Sie abschließend die Option **Anbieter hinzufügen** aus. Fügen Sie nach Bedarf weitere Anbieter hinzu, oder wählen Sie **Überprüfen und erstellen** aus, um die Bereitstellung abzuschließen.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Abbildung der Konfigurationsoptionen beim Hinzufügen von Anbieterinformationen" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Anbieter für Hochverfügbarkeitscluster (Pacemaker)

1. Wählen Sie in der Dropdownliste den Eintrag **Hochverfügbarkeitscluster (Pacemaker)** aus. 

   > [!IMPORTANT]
   > Stellen Sie zum Konfigurieren des Anbieters für Hochverfügbarkeitscluster (Pacemaker) sicher, dass ha_cluster_provider in jedem Knoten installiert ist. Weitere Informationen finden Sie unter [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter#installation).

2. Geben Sie den Prometheus-Endpunkt in der Form http://IP:9664/metrics ein. 
 
3. Geben Sie die System-ID (SID), den Hostnamen und den Clusternamen ein.

4. Wählen Sie abschließend die Option **Anbieter hinzufügen** aus. Fügen Sie nach Bedarf weitere Anbieter hinzu, oder wählen Sie **Überprüfen und erstellen** aus, um die Bereitstellung abzuschließen.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Abbildung der Optionen im Zusammenhang mit dem Anbieter für Hochverfügbarkeitscluster (Pacemaker)" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server-Anbieter

1. Vor dem Hinzufügen des Microsoft SQL Server-Anbieters sollten Sie das folgende Skript in SQL Server Management Studio ausführen, um einen Benutzer mit den erforderlichen Berechtigungen zum Konfigurieren des Anbieters zu erstellen.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Wählen Sie **Anbieter hinzufügen** und dann in der Dropdownliste den Eintrag **Microsoft SQL Server** aus. 

3. Füllen Sie die Felder mit Informationen entsprechend Ihrer Microsoft SQL Server-Instanz aus. 

4. Wählen Sie abschließend die Option **Anbieter hinzufügen** aus. Fügen Sie nach Bedarf weitere Anbieter hinzu, oder wählen Sie **Überprüfen und erstellen** aus, um die Bereitstellung abzuschließen.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Abbildung der Informationen im Zusammenhang mit dem Hinzufügen des Microsoft SQL Server-Anbieters" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Azure Monitor für SAP-Lösungen](azure-monitor-overview.md)
