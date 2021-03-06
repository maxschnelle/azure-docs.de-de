---
title: 'Schnellstart: Massenladen von Daten mit einem dedizierten SQL-Pool'
description: Verwenden Sie Synapse Studio zum Massenladen von Daten in einen dedizierten SQL-Pool in Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 2a4740699d70601591645aa0d3183531a6687be6
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324938"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Schnellstart: Massenladen mit Synapse SQL

Der Massenladen-Assistent in Synapse Studio macht das Laden von Daten so einfach wie nie. Dieser Assistent führt Sie durch die Erstellung eines T-SQL-Skripts mit der [COPY-Anweisung](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) zum Massenladen von Daten. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Einstiegspunkte für den Massenladen-Assistenten

Das Massenladen von Daten unter Verwendung von dedizierten SQL-Pools ist jetzt ganz einfach und kann mit einem Rechtsklick auf die folgenden Bereiche in Synapse Studio durchgeführt werden:

- Eine Datei oder ein Ordner aus einem mit Ihrem Arbeitsbereich verknüpften Azure-Speicherkonto ![Rechtsklick auf eine Datei oder einen Ordner aus einem Speicherkonto](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Voraussetzungen

- Dieser Assistent generiert eine COPY-Anweisung, die AAD-Passthrough für die Authentifizierung verwendet. Ihr [Azure AD-Benutzer benötigt Zugriff](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) auf den Arbeitsbereich. Dazu muss ihm mindestens die Azure-Rolle „Mitwirkender an Storage-Blobdaten“ für das ADLS Gen2-Konto zugewiesen sein. 

- Falls Sie als Ziel für den Ladevorgang eine neue Tabelle erstellen, müssen Sie über die erforderlichen [Berechtigungen für die Verwendung der COPY-Anweisung](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) sowie über Berechtigungen für die Tabellenerstellung verfügen.

- Der verknüpfte Dienst, der dem ADLS Gen2-Konto zugeordnet ist, muss über **Zugriff auf die Datei**/**den Ordner** verfügen. Wenn für den verknüpften Dienst beispielsweise „Verwaltete Identität“ als Authentifizierungsmechanismus verwendet wird, muss die verwaltete Identität für den Arbeitsbereich mindestens über Speicherblob-Leseberechtigung für das Speicherkonto verfügen.

- Wenn für Ihren Arbeitsbereich ein VNET aktiviert ist, stellen Sie sicher, dass für die integrierte Laufzeit, die den verknüpften Diensten des ADLS Gen2-Kontos für den Speicherort der Quelldaten und der Fehlerdatei zugeordnet ist, die interaktive Erstellung aktiviert ist. Die interaktive Erstellung ist für die automatische Schemaerkennung sowie zum Anzeigen einer Vorschau der Quelldateiinhalte und zum Durchsuchen von ADLS Gen2-Speicherkonten innerhalb des Assistenten erforderlich.

### <a name="steps"></a>Schritte

1. Wählen Sie im Bereich für den Quellspeicherort das Speicherkonto und die Datei bzw. den Ordner aus, die bzw. den Sie als Quelle für den Ladevorgang verwenden möchten: ![Auswählen des Quellspeicherorts](./sql/media/bulk-load/bulk-load-source-location.png)

2. Wählen Sie die Dateiformateinstellungen einschließlich des Speicherkontos aus, in das abgelehnte Zeilen geschrieben werden sollen (Fehlerdatei). Aktuell werden nur CSV- und Parquet-Dateien unterstützt.

    ![Auswählen der Dateiformateinstellungen](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Sie können „Datenvorschau“ auswählen, um zu sehen, wie die Datei von der COPY-Anweisung analysiert wird, was beim Konfigurieren der Dateiformateinstellungen hilfreich ist. Wählen Sie nach jeder Änderung einer Dateiformateinstellung „Datenvorschau“ aus, um zu sehen, wie die Datei mit der aktualisierten Einstellung von der COPY-Anweisung analysiert wird: ![Anzeigen einer Datenvorschau](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

4. Wählen Sie den zum Laden verwendeten dedizierten SQL-Pool aus, und geben Sie an, ob es sich um einen Ladevorgang für eine bereits vorhandene oder für eine neue Tabelle handelt: ![Auswählen des Zielspeicherorts](./sql/media/bulk-load/bulk-load-target-location.png)

5. Wählen Sie die Option zum Konfigurieren der Spaltenzuordnung aus, und vergewissern Sie sich, dass die Spaltenzuordnung korrekt ist. Bei neuen Tabellen muss die Spaltenzuordnung unbedingt konfiguriert werden, um die Datentypen der Zielspalten zu aktualisieren: ![Konfigurieren der Spaltenzuordnung](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Wählen Sie die Option zum Öffnen des Skripts aus. Daraufhin wird ein T-SQL-Skript mit der COPY-Anweisung generiert, um Daten aus Ihrem Data Lake zu laden: ![Öffnen des SQL-Skripts](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Artikel zur COPY-Anweisung](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax), um weitere Informationen zu Kopierfunktionen zu erhalten.
- Lesen Sie den [Übersichtsartikel zum Laden von Daten](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt).
