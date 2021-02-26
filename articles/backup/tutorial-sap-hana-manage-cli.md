---
title: 'Tutorial: Verwalten von gesicherten SAP HANA-Datenbanken über die CLI'
description: In diesem Tutorial wird beschrieben, wie Sie gesicherte SAP HANA-Datenbanken, die auf einem virtuellen Azure-Computer ausgeführt werden, über die Azure CLI verwalten.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: cb552c5a336c3c55652936b87a668b54cfdeb41e
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507231"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Verwalten von SAP HANA-Datenbanken auf einem virtuellen Azure-Computer über die Azure CLI

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Dokumentation wird beschrieben, wie Sie eine gesicherte SAP HANA-Datenbank auf einem virtuellen Azure-Computer über die Azure CLI verwalten. Sie können diese Schritte auch über das [Azure-Portal](./sap-hana-db-manage.md) ausführen.

Verwenden Sie [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md), um die CLI-Befehle auszuführen.

In diesem Tutorial wird Folgendes vermittelt:

> [!div class="checklist"]
>
> * Überwachen von Sicherungs- und Wiederherstellungsaufträgen
> * Schützen neuer Datenbanken, die einer SAP HANA-Instanz hinzugefügt wurden
> * Ändern der Richtlinie
> * Schutz beenden
> * Schutz fortsetzen

Falls Sie die Anleitung unter [Sichern einer SAP HANA-Datenbank in Azure mit der CLI](tutorial-sap-hana-backup-cli.md) verwendet haben, um Ihre SAP HANA-Datenbank zu sichern, nutzen Sie die folgenden Ressourcen:

* eine Ressourcengruppe mit dem Namen *saphanaResourceGroup*
* einen Tresor mit dem Namen *saphanaVault*
* einen geschützten Container mit dem Namen *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* eine gesicherte Datenbank bzw. ein anderes Element mit dem Namen *saphanadatabase;hxe;hxe*
* Ressourcen in der Region *westus2*

Die Azure CLI vereinfacht die Verwaltung einer SAP HANA-Datenbank auf einer Azure-VM, die mit Azure Backup gesichert wird. In diesem Tutorial werden die einzelnen Verwaltungsvorgänge ausführlich beschrieben.

## <a name="monitor-backup-and-restore-jobs"></a>Überwachen von Sicherungs- und Wiederherstellungsaufträgen

Verwenden Sie das Cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list), um abgeschlossene oder derzeit ausgeführte Aufträge (Sicherung oder Wiederherstellung) zu überwachen. Mit der CLI haben Sie auch die Möglichkeit, [einen ausgeführten Auftrag anzuhalten](/cli/azure/backup/job#az-backup-job-stop) oder [den Abschluss eines Auftrags abzuwarten](/cli/azure/backup/job#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Die Ausgabe sieht in etwa wie folgt aus:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Ändern einer Richtlinie

Verwenden Sie das Cmdlet [az backup policy set](/cli/azure/backup/policy#az-backup-policy-set), um die Richtlinie zu ändern, die der SAP HANA-Sicherungskonfiguration zugrunde liegt. Mit dem Parameter „name“ in diesem Cmdlet wird auf das Sicherungselement verwiesen, für das wir die Richtlinie ändern möchten. In diesem Tutorial ersetzen wir die Richtlinie unserer SAP HANA-Datenbank *saphanadatabase;hxe;hxe* durch die neue Richtlinie *newsaphanaPolicy*. Neue Richtlinien können mit dem Cmdlet [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create) erstellt werden.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Die Ausgabe sollte wie folgt aussehen:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>Erstellen einer Richtlinie zur inkrementellen Sicherung

Führen Sie zum Erstellen einer Richtlinie zur inkrementellen Sicherung den Befehl [az backup policy create](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_create) mit den folgenden Parametern aus:

* **--backup-management-type**: Azure-Workload
* **--workload-type**: SAPHana
* **--name**: Name der Richtlinie
* **--policy**: JSON-Datei mit den entsprechenden Details zu Zeitplan und Aufbewahrung
* **--resource-group**: Ressourcengruppe des Tresors
* **--vault-name**: Name des Tresors

Beispiel:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

JSON-Beispielausgabe (sappolicy.json):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

Sie können den folgenden Abschnitt der Richtlinie ändern, um die gewünschte Sicherungshäufigkeit und die Aufbewahrungsdauer für inkrementelle Sicherungen anzugeben.

Beispiel:

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

Beispiel:

Wenn Sie inkrementelle Sicherungen nur am Samstag ausführen und für 60 Tage aufbewahren möchten, nehmen Sie die folgenden Änderungen an der Richtlinie vor:

* Ändern Sie den Zähler **retentionDuration** in 60 Tage.
* Geben Sie für **ScheduleRunDays** nur Samstag an.

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Schützen neuer Datenbanken, die einer SAP HANA-Instanz hinzugefügt wurden

Beim [Registrieren einer SAP HANA-Instanz bei einem Recovery Services-Tresor](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) werden alle Datenbanken dieser Instanz automatisch ermittelt.

Sie können aber das Cmdlet [az backup protectable-item initialize](/cli/azure/backup/protectable-item#az-backup-protectable-item-initialize) verwenden, wenn der SAP HANA-Instanz später neue Datenbanken hinzugefügt werden. Mit diesem Cmdlet werden die neu hinzugefügten Datenbanken ermittelt.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Verwenden Sie anschließend das Cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list), um alle Datenbanken aufzulisten, die für Ihre SAP HANA-Instanz ermittelt wurden. In dieser Liste sind aber nicht die Datenbanken enthalten, für die die Sicherung bereits konfiguriert wurde. Nachdem die zu sichernde Datenbank ermittelt wurde, helfen Ihnen die Informationen unter [Aktivieren der Sicherung für eine SAP HANA-Datenbank](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database) weiter.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Die Datenbank, die Sie sichern möchten, ist in der folgenden Liste enthalten:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Beenden des Schutzes für eine SAP HANA-Datenbank

Sie können den Schutz einer SAP HANA-Datenbank auf verschiedene Weise beenden:

* Beenden aller zukünftigen Sicherungsaufträge und Löschen aller Wiederherstellungspunkte
* Beenden aller zukünftigen Sicherungsaufträge und Beibehalten der Wiederherstellungspunkte.

Wenn Sie die Wiederherstellungspunkte beibehalten, sollten Sie Folgendes beachten:

* Alle Wiederherstellungspunkte werden unbegrenzt beibehalten, und die gesamte Bereinigung endet mit der Beendigung des Schutzes unter Beibehaltung der Daten.
* Ihnen werden die geschützte Instanz und der verbrauchte Speicher in Rechnung gestellt.
* Wenn Sie eine Datenquelle löschen, ohne die Sicherungen zu beenden, treten bei neuen Sicherungen Fehler auf.

Wir sehen uns die einzelnen Möglichkeiten zum Beenden des Schutzes nun genauer an.

### <a name="stop-protection-with-retain-data"></a>Beendigung des Schutzes mit Beibehaltung der Daten

Verwenden Sie das Cmdlet [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable), um den Schutz zu beenden und die Daten beizubehalten.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Die Ausgabe sollte wie folgt aussehen:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Verwenden Sie das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show), um den Status dieses Vorgangs zu überprüfen.

### <a name="stop-protection-without-retain-data"></a>Beendigung des Schutzes ohne Beibehaltung der Daten

Verwenden Sie das Cmdlet [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable), um den Schutz zu beenden, ohne die Daten beizubehalten.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Die Ausgabe sollte wie folgt aussehen:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Verwenden Sie das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show), um den Status dieses Vorgangs zu überprüfen.

## <a name="resume-protection"></a>Schutz fortsetzen

Wenn Sie den Schutz für die SAP HANA-Datenbank mit Beibehaltung der Daten beenden, können Sie ihn später wieder aktivieren bzw. fortsetzen. Sie können den Schutz nicht fortsetzen, wenn Sie die gesicherten Daten nicht beibehalten.

Verwenden Sie das Cmdlet [az backup protection resume](/cli/azure/backup/protection#az-backup-protection-resume), um den Schutz fortzusetzen.

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Die Ausgabe sollte wie folgt aussehen:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Verwenden Sie das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show), um den Status dieses Vorgangs zu überprüfen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Sichern einer auf einer Azure-VM ausgeführten SAP HANA-Datenbank über das Azure-Portal finden Sie unter [Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern](./backup-azure-sap-hana-database.md).

* Informationen zum Verwalten einer auf einer Azure-VM ausgeführten gesicherten SAP HANA-Datenbank über das Azure-Portal finden Sie unter [Verwalten und Überwachen gesicherter SAP HANA-Datenbanken](./sap-hana-db-manage.md).
