---
title: Erstellen von SQL Server auf einem virtuellen Windows-Computer im Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial wird gezeigt, wie Sie einen virtuellen Windows-Computer mit SQL Server 2017 im Azure-Portal erstellen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 335a63faa440e057c282f992b67b301289a7a4bb
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356955"
---
# <a name="quickstart-create-sql-server-2017-on-a-windows-virtual-machine-in-the-azure-portal"></a>Schnellstart: Erstellen von SQL Server 2017 auf einem virtuellen Windows-Computer im Azure-Portal

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Windows](sql-vm-create-portal-quickstart.md)
> * [Linux](../linux/sql-vm-create-portal-quickstart.md)

In dieser Schnellstartanleitung wird beschrieben, wie Sie im Azure-Portal einen virtuellen SQL Server-Computer (VM) erstellen.


  > [!TIP]
  > - Sie erfahren, wie Sie schnell einen virtuellen SQL-Computer bereitstellen und eine Verbindung damit herstellen. Weitere Informationen zu anderen Bereitstellungsoptionen für virtuelle SQL-Computer finden Sie im [Bereitstellungshandbuch für SQL Server auf einem virtuellen Windows-Computer im Azure-Portal](create-sql-vm-portal.md).
  > - Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.md) weitere Informationen.

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Einrichten eines Azure-Abonnements

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Auswählen eines SQL Server-VM-Images

1. Melden Sie sich mit Ihrem Konto beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü im Azure-Portal die Option **Azure SQL** aus. Wenn **Azure SQL** in der Liste nicht aufgeführt ist, wählen Sie **Alle Dienste** aus, und geben Sie dann *Azure SQL* ins Suchfeld ein.
1. Wählen Sie **+Hinzufügen** aus, um die Seite **SQL-Bereitstellungsoption auswählen** zu öffnen. Sie können weitere Informationen anzeigen, indem Sie auf der Kachel **SQL-VMs** die Option **Details anzeigen** auswählen.
1. Wählen Sie das Image **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** (Kostenlose SQL Server-Lizenz: SQL Server 2017 Developer unter Windows Server 2016) aus.

   ![Screenshot, der zeigt, wo Sie das Image „Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016“ (Kostenlose SQL Server-Lizenz: SQL Server 2017 Developer unter Windows Server 2016) auswählen](./media/sql-vm-create-portal-quickstart/select-sql-2017-vm-image.png)

1. Klicken Sie auf **Erstellen**.

   ![Fenster „Neue Suche“](./media/sql-vm-create-portal-quickstart/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Angeben grundlegender Informationen

Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen an:

1. Wählen Sie im Abschnitt **Projektdetails** Ihr Azure-Abonnement und anschließend **Neue erstellen** aus, um eine neue Ressourcengruppe zu erstellen. Geben Sie als Name _SQLVM-RG_ ein.

   ![Subscription](./media/sql-vm-create-portal-quickstart/basics-project-details.png)

1. Gehen Sie unter **Instanzendetails** wie folgt vor:
    1. Geben Sie unter **Name des virtuellen Computers** den Namen _SQLVM_ ein. 
    1. Wählen Sie unter **Region** einen Standort aus. 
    1. Übernehmen Sie für diese Schnellstartanleitung für **Verfügbarkeitsoptionen** die Einstellung _Keine Infrastrukturredundanz erforderlich_. Weitere Informationen zu Verfügbarkeitsoptionen finden Sie unter [Verfügbarkeit](../../../virtual-machines/availability.md). 
    1. Wählen Sie in der Liste unter **Image** die Option _Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016_ aus. 
    1. Wählen Sie unter **Größe** die Option **Größe ändern** aus, um die Größe des virtuellen Computers anzupassen, und wählen Sie das Angebot **Basic A2** aus. Bereinigen Sie unbedingt die Ressourcen, wenn Sie fertig sind, um unerwartete Gebühren zu vermeiden. 

   ![Instanzendetails](./media/sql-vm-create-portal-quickstart/basics-instance-details.png)

1. Geben Sie unter **Administratorkonto** einen Benutzernamen wie _azureuser_ sowie ein Kennwort an. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) erfüllen.

   ![Administratorkonto](./media/sql-vm-create-portal-quickstart/basics-administrator-account.png)

1. Wählen Sie unter **Regeln für eingehende Ports** die Option **Ausgewählte Ports zulassen** aus, und wählen Sie dann **RDP (3389)** aus der Dropdownliste aus. 

   ![Regeln für eingehende Ports](./media/sql-vm-create-portal-quickstart/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server-Einstellungen

Konfigurieren Sie auf der Registerkarte **SQL Server-Einstellungen** die folgenden Optionen:

1. Wählen Sie unter **Sicherheit + Netzwerkbetrieb** für **SQL-Konnektivität** die Option _Öffentlich (Internet)_ aus, und ändern Sie den Port in `1401`, um eine bekannte Portnummer im öffentlichen Szenario zu vermeiden. 
1. Wählen Sie unter **SQL-Authentifizierung** die Option **Aktivieren** aus. Für die SQL-Anmeldeinformationen werden der gleiche Benutzername und das gleiche Kennwort festgelegt, die Sie für den virtuellen Computer konfiguriert haben. Verwenden Sie die Standardeinstellung für [**Azure Key Vault-Integration**](azure-key-vault-integration-configure.md). **Speicherkonfiguration** ist für das SQL Server-VM-Basisimage nicht verfügbar. Weitere Informationen zu verfügbaren Optionen für andere Images finden Sie jedoch unter [Speicherkonfiguration](storage-configuration.md#new-vms).  

   ![Sicherheitseinstellungen für SQL Server](./media/sql-vm-create-portal-quickstart/sql-server-settings.png)


1. Ändern Sie ggf. weitere Einstellungen, und wählen Sie dann **Bewerten + erstellen** aus. 

   ![Bewerten + erstellen](./media/sql-vm-create-portal-quickstart/review-create.png)


## <a name="create-the-sql-server-vm"></a>Erstellen des virtuellen SQL Server-Computers

Überprüfen Sie auf der Registerkarte **Bewerten + erstellen** die Zusammenfassung, und wählen Sie **Erstellen** aus, um SQL Server, die Ressourcengruppe und die für diesen virtuellen Computer angegebenen Ressourcen zu erstellen.

Sie können die Bereitstellung über das Azure-Portal überwachen. Auf der Schaltfläche **Benachrichtigungen** oben auf der Seite wird der grundlegende Status der Bereitstellung angezeigt. Die Bereitstellung kann einige Minuten dauern. 

## <a name="connect-to-sql-server"></a>Verbindung mit SQL Server herstellen

1. Suchen Sie im Portal im Abschnitt **Übersicht** der Eigenschaften Ihres virtuellen Computers den Wert für **Öffentliche IP-Adresse** Ihres virtuellen SQL Server-Computers.

1. Öffnen Sie [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) auf einem anderen Computer mit Internetverbindung.


1. Bearbeiten Sie im Dialogfeld **Mit Server verbinden** oder **Verbindung mit Datenbank-Engine herstellen** den Wert **Servername**. Geben Sie die öffentliche IP-Adresse des virtuellen Computers ein. Fügen Sie dann ein Komma und den benutzerdefinierten Port (**1401**) hinzu, den Sie beim Konfigurieren des neuen virtuellen Computers angegeben haben. Beispiel: `11.22.33.444,1401`.

1. Wählen Sie im Feld **Authentifizierung** den Eintrag **SQL Server-Authentifizierung**.

1. Geben Sie im Feld **Anmelden** einen gültigen SQL-Anmeldenamen ein.

1. Geben Sie im Feld **Kennwort** das Kennwort für die Anmeldung ein.

1. Wählen Sie **Verbinden**.

    ![SSMS-Verbindung](./media/sql-vm-create-portal-quickstart/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Remoteanmeldung beim virtuellen Computer

Führen Sie die folgenden Schritte aus, um mithilfe von Remotedesktop eine Verbindung mit dem virtuellen SQL Server-Computer herzustellen:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Nachdem Sie eine Verbindung mit dem virtuellen SQL Server-Computer hergestellt haben, können Sie SQL Server Management Studio starten und mit Ihren Anmeldeinformationen für den lokalen Administrator eine Verbindung mit der Windows-Authentifizierung herstellen. Wenn Sie die SQL Server-Authentifizierung aktiviert haben, können Sie die Verbindung auch per SQL-Authentifizierung herstellen, indem Sie den während der Bereitstellung konfigurierten SQL-Benutzernamen und das Kennwort verwenden.

Durch den Zugriff auf den Computer können Sie die Computer- und SQL Server-Einstellungen je nach Ihren Anforderungen direkt ändern. Beispielsweise können Sie die Firewalleinstellungen konfigurieren oder die SQL Server-Konfigurationseinstellungen ändern.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn der virtuelle SQL-Computer nicht kontinuierlich ausgeführt werden muss, können Sie unnötige Kosten vermeiden, indem Sie ihn anhalten, wenn er nicht verwendet wird. Sie können darüber hinaus alle dem virtuellen Computer zugeordneten Ressourcen endgültig entfernen, indem Sie die zugeordnete Ressourcengruppe im Portal löschen. Dadurch wird auch der virtuelle Computer endgültig gelöscht. Seien Sie daher vorsichtig, wenn Sie diesen Befehl verwenden. Weitere Informationen finden Sie unter [Verwalten von Azure-Ressourcen über das Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen virtuellen SQL Server 2017-Computer im Azure-Portal erstellt. Weitere Informationen zum Migrieren Ihrer Daten zu dem neuen SQL Server-Computer finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](migrate-to-vm-from-sql-server.md)