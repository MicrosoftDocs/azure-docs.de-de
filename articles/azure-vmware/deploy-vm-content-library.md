---
title: Erstellen einer Inhaltsbibliothek zum Bereitstellen von VMs in Azure VMware Solution
description: Erstellen Sie eine Inhaltsbibliothek zum Bereitstellen einer VM in einer privaten Azure VMware Solution-Cloud.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9e27529ee355ece62f12cc2e6de491dddb5d3f28
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550537"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Erstellen einer Inhaltsbibliothek zum Bereitstellen von VMs in Azure VMware Solution

In einer Inhaltsbibliothek werden Inhalte in Form von Bibliothekselementen gespeichert und verwaltet. Ein einzelnes Bibliothekselement besteht aus einer oder mehreren Dateien, die Sie zum Bereitstellen virtueller Computer (VMs) verwenden. 

In diesem Artikel werden die Schritte zum Erstellen einer Inhaltsbibliothek erläutert.  Anschließend wird die Bereitstellung einer VM mithilfe eines ISO-Images aus der Inhaltsbibliothek erläutert.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial sind ein logisches NSX-T-Switchsegment und ein verwalteter DHCP-Dienst erforderlich.  Weitere Informationen dazu finden Sie im Artikel [Verwalten von DHCP in Azure VMware Solution](manage-dhcp.md).

## <a name="create-a-content-library"></a>Erstellen einer Inhaltsbibliothek

1. Wählen Sie auf dem lokalen vSphere-Client **Menü > Inhaltsbibliotheken** aus.

   ![Auswählen von „Menü“ -> „Inhaltsbibliotheken“](./media/content-library/vsphere-menu-content-libraries.png)

1. Wählen Sie die Schaltfläche **Hinzufügen** aus, um eine neue Inhaltsbibliothek zu erstellen.

   ![Wählen Sie die Schaltfläche „Hinzufügen“ aus, um eine neue Inhaltsbibliothek zu erstellen.](./media/content-library/create-new-content-library.png)

1. Geben Sie einen Namen an, und bestätigen Sie die IP-Adresse des vCenter-Servers. Wählen Sie dann **Weiter** aus.

   ![Geben Sie einen Namen und Hinweise Ihrer Wahl an, und wählen Sie „Weiter“ aus.](./media/content-library/new-content-library-step1.png)

1. Wählen Sie **Lokale Inhaltsbibliothek** und dann **Weiter** aus.

   ![In diesem Beispiel wird eine lokale Inhaltsbibliothek erstellt. Wählen Sie „Weiter“ aus.](./media/content-library/new-content-library-step2.png)

1. Wählen Sie den Datenspeicher zum Speichern der Inhaltsbibliothek und dann **Weiter** aus.

   ![Wählen Sie den gewünschten Datenspeicher zum Hosten der Inhaltsbibliothek und dann „Weiter“ aus.](./media/content-library/new-content-library-step3.png)

1. Überprüfen Sie die Einstellungen der Inhaltsbibliothek, und wählen Sie dann **Fertig stellen** aus.

   ![Überprüfen Sie die Einstellungen, und wählen Sie „Fertig stellen“ aus.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Hochladen eines ISO-Images in die Inhaltsbibliothek

Nachdem Sie die Inhaltsbibliothek erstellt haben, können Sie ein ISO-Image hinzufügen, um eine VM in einem privaten Cloudcluster bereitzustellen. 

1. Wählen Sie auf dem vSphere-Client **Menü > Inhaltsbibliotheken** aus.

1. Klicken Sie mit der rechten Maustaste auf die Inhaltsbibliothek, die Sie für das neue ISO-Image verwenden möchten, und wählen Sie **Element importieren** aus.

1. Importieren Sie ein Bibliothekselement für die Quelle mithilfe einer der folgenden Aktionen, und wählen Sie dann **Importieren** aus:
   1. Wählen Sie „URL“ aus, und geben Sie eine URL zum Herunterladen eines ISO-Images an.

   1. Wählen Sie die **Lokale Datei** aus, die vom lokalen System hochgeladen werden soll.

   > [!TIP]
   > Optional können Sie einen benutzerdefinierten Elementnamen und Hinweise für das Ziel definieren.

1. Öffnen Sie die Bibliothek, und wählen Sie die Registerkarte **Andere Typen** aus, um zu überprüfen, ob Ihr ISO-Image erfolgreich hochgeladen wurde.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Bereitstellen einer VM in einem privaten Cloudcluster

1. Wählen Sie auf dem vSphere-Client **Menü > Hosts und Cluster** aus.

1. Erweitern Sie die Struktur im linken Bereich, und wählen Sie einen Cluster aus.

1. Wählen Sie **Aktionen > Neuer virtueller Computer** aus.

1. Durchlaufen Sie den Assistenten, und ändern Sie die gewünschten Einstellungen.

1. Wählen Sie **Neues CD-/DVD-Laufwerk > Clientgerät > ISO-Datei der Inhaltsbibliothek** aus.

1. Wählen Sie das im vorherigen Abschnitt hochgeladene ISO-Image und dann **OK** aus.

1. Aktivieren Sie das Kontrollkästchen **Verbinden**, damit das ISO-Image beim Einschalten bereitgestellt wird.

1. Wählen Sie **Neues Netzwerk > Dropdownliste auswählen > Durchsuchen** aus.

1. Wählen Sie den **logischen Switch (Segment)** und dann **OK** aus.

1. Ändern Sie beliebige andere Hardwareeinstellungen, und wählen Sie **Weiter** aus.

1. Überprüfen Sie die Einstellungen, und wählen Sie **Fertig stellen** aus.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Erstellen einer Inhaltsbibliothek zum Bereitstellen von VMs in Azure VMware Solution vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- [Bereitstellen und Konfigurieren von VMware HCX](tutorial-deploy-vmware-hcx.md), um VM-Workloads zu Ihrer privaten Cloud zu migrieren
- [Lebenszyklusverwaltung von Azure VMware Solution-VMs](lifecycle-management-of-azure-vmware-solution-vms.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
