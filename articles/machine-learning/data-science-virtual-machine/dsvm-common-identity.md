---
title: Einrichten einer gemeinsamen Identität für Data Science Virtual Machine – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie gemeinsame Benutzerkonten erstellen, die auf mehreren Data Science Virtual Machines verwendet werden können. Sie können Azure Active Directory oder ein lokales Active Directory verwenden, um die Authentifizierung von Benutzern mit der Data Science Virtual Machine auszuführen.
keywords: Deep Learning, KI, Data Science-Tools, virtueller Data Science-Computer, räumliche Analysen, Data Science-Teamprozess
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0146ee6ee37c2eb9e98d831b54df2218d7de5b62
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502374"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Einrichten einer gemeinsamen Identität für Data Science Virtual Machine

Auf einem virtuellen Azure-Computer (VM) wie Data Science Virtual Machine (DSVM) erstellen Sie lokale Benutzerkonten bei der VM-Bereitstellung. Benutzer authentifizieren sich dann mit diesen Anmeldeinformationen bei der VM. Wenn Sie mehrere VMs haben, auf die Sie zugreifen müssen, kann dieser Ansatz bei der Verwaltung der Anmeldeinformationen schnell mühsam werden. Durch gemeinsame Benutzerkonten und deren Verwaltung mit einem standardbasierten Identitätsanbieter können Sie eine einzelne Gruppe von Anmeldeinformationen verwenden, um auf mehrere Ressourcen in Azure wie mehrere DSVM-Instanzen zuzugreifen. 

Active Directory ist ein beliebter Identitätsanbieter und wird als Dienst in Azure und lokal unterstützt. Sie können Benutzer einer eigenständigen DSVM-Instanz oder in einem DSVM-Cluster in einer Azure-VM-Skalierungsgruppe mit Azure Active Directory (Azure AD) oder dem lokalen Active Directory-Verzeichnis authentifizieren. Dazu müssen die DSVM-Instanzen einer Active Directory-Domäne beitreten. 

Wenn Sie bereits über Active Directory zum Verwalten von Identitäten verfügen, können Sie Active Directory als gemeinsamen Identitätsanbieter verwenden. Für den Fall, dass Sie nicht über Active Directory verfügen, können Sie eine verwaltete Active Directory-Instanz in Azure über einen Dienst namens [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS) ausführen. 

Die Dokumentation für [Azure AD](https://docs.microsoft.com/azure/active-directory/) bietet detaillierte [Anweisungen für die Verwaltung](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), einschließlich der Verbindung von Azure AD mit Ihrem lokalen Verzeichnis, sofern vorhanden. 

Dieser Artikel beschreibt die Schritte zum Einrichten eines vollständig verwalteten Active Directory-Domänendiensts in Azure mithilfe der Azure AD DS. Sie können Ihre DSVM-Instanzen dann in die verwaltete Active Directory-Domäne einbinden, um Benutzern den Zugriff auf einen Pool von DSVM-Instanzen (und andere Azure-Ressourcen) mit einem gemeinsamen Benutzerkonto und gemeinsamen Benutzeranmeldeinformationen zu ermöglichen. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Einrichten einer vollständig verwalteten Active Directory-Domäne in Azure

Azure AD DS vereinfacht die Verwaltung Ihrer Identitäten, indem Ihnen ein vollständig verwalteter Dienst in Azure bereitgestellt wird. In dieser Active Directory-Domäne verwalten Sie Benutzer und Gruppen. Die Schritte zum Einrichten einer von Azure gehosteten Active Directory-Domäne und der Benutzerkonten in Ihrem Verzeichnis lauten wie folgt:

1. Fügen Sie im Azure-Portal den Benutzer zu Active Directory hinzu: 

   a. Melden Sie sich beim [Azure Active Directory Admin Center](https://aad.portal.azure.com) über ein Konto an, das als globaler Administrator für das Verzeichnis konfiguriert ist.
    
   b. Wählen Sie **Azure Active Directory** und dann **Benutzer und Gruppen** aus.
    
   c. Wählen Sie unter **Benutzer und Gruppen** die Option **Alle Benutzer** und dann **Neuer Benutzer** aus.
   
      Der Bereich **Benutzer** wird geöffnet.
      
      ![Der Bereich „Benutzer“](./media/add-user.png)
    
   d. Geben Sie Details zum Benutzer ein, beispielsweise **Name** und **Benutzername**. Der Domänennamensteil des Benutzernamens muss entweder der anfängliche Standarddomänenname „[Domänenname].onmicrosoft.com“ oder ein überprüfter, nicht im Verbund konfigurierter [benutzerdefinierter Domänenname](../../active-directory/add-custom-domain.md) wie z.B. „contoso.com“ sein.
    
   e. Kopieren oder notieren Sie sich das generierte Benutzerkennwort, damit sie es nach Abschluss des Vorgangs dem Benutzer mitteilen können.
    
   f. Optional können Sie die Informationen unter **Profil**, **Gruppen** oder **Verzeichnisrolle** für den Benutzer ausfüllen. 
    
   g. Wählen Sie unter **Benutzer** die Option **Erstellen** aus.
    
   h. Sorgen Sie für eine sichere Übermittlung des generierten Kennworts an den neuen Benutzer, damit sich dieser anmelden kann.

1. Erstellen Sie eine Azure AD DS-Instanz. Befolgen Sie hierfür die Anweisungen im Artikel [Aktivieren der Azure Active Directory Domain Services mithilfe des Azure-Portals](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (Aufgabe 1 bis 5). Es ist wichtig, die vorhandenen Benutzerkennwörter in Active Directory zu aktualisierten, damit das Kennwort in den Azure AD DS synchronisiert wird. Es ist auch wichtig, das DNS zu den Azure AD DS hinzuzufügen, wie in Aufgabe 4 des Artikels beschrieben wird. 

1. Erstellen Sie ein separates DSVM-Subnetz im virtuellen Netzwerk, das in Aufgabe 2 des vorherigen Schritts erstellt wurde.
1. Erstellen Sie eine oder mehrere Data Science VM-Instanzen im DSVM-Subnetz. 
1. Befolgen Sie die [Anweisungen](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) zum Hinzufügen von DSVM zu Active Directory. 
1. Binden Sie als Nächstes eine Azure Files-Freigabe zum Hosten Ihres Stamm- oder Notebookverzeichnisses ein, damit Ihr Arbeitsbereich auf jedem Computer eingebunden werden kann. (Wenn Sie strenge Berechtigungen auf Dateiebene benötigen, muss das NFS auf einer oder mehreren VMs ausgeführt werden.)

   a. [Erstellen Sie eine Azure Files-Freigabe](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Stellen Sie sie auf der Linux-DSVM bereit. Wenn Sie auf die Schaltfläche **Verbinden** für die Azure Files-Freigabe in Ihrem Speicherkonto im Azure-Portal klicken, wird der Befehl angezeigt, der in der Bash-Shell in Linux-DSVM ausgeführt werden soll. Der Befehl sieht wie folgt aus:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Es wird davon ausgegangen, dass Sie Ihre Azure Files-Freigabe z.B. in „/data/workspace“ eingebunden haben. Erstellen Sie nun Verzeichnisse für jeden Benutzer in der Freigabe, wie „/data/workspace/user1“, „/data/workspace/user2“ etc. Erstellen Sie ein `notebooks`-Verzeichnis im Arbeitsbereich der einzelnen Benutzer. 
1. Erstellen Sie symbolische Verknüpfungen für `notebooks` in `$HOME/userx/notebooks/remote`.   

Jetzt sind die Benutzer in Ihrer Active Directory-Instanz enthalten, die in Azure gehostet wird. Mithilfe der Active Directory-Anmeldeinformationen können sich Benutzer bei einer beliebigen DSVM-Instanz (SSH oder JupyterHub) anmelden, die in die Azure AD DS eingebunden ist. Da sich der Benutzerarbeitsbereich in einer Azure Files-Freigabe befindet, haben Benutzer über jede DSVM-Instanz mithilfe von JupyterHub Zugriff auf ihre Notebooks und auf sonstige Arbeiten. 

Für die automatische Skalierung können Sie eine VM-Skalierungsgruppe verwenden, um einen Pool von VMs zu erstellen, die alle auf diese Weise zur Domäne hinzugefügt werden und bei denen der freigegebene Datenträger eingebunden ist. Benutzer können sich bei jedem verfügbaren Computer in der VM-Skalierungsgruppe anmelden und haben Zugriff auf den freigegebenen Datenträger, auf dem ihre Notebooks gespeichert sind. 

## <a name="next-steps"></a>Nächste Schritte

* [Sicheres Speichern von Anmeldeinformationen für den Zugriff auf Cloudressourcen](dsvm-secure-access-keys.md)



