---
title: Entpivotierungstransformation im Mappingdatenfluss
description: 'Azure Data Factory-Mappingdatenfluss: Entpivotierungstransformation'
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ea8881adf39a315df7746dbce14dedcbee18ccf6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521049"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Entpivotierungstransformation im Mappingdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie das Entpivotieren in einem ADF-Mappingdatenfluss, um ein nicht normalisiertes Dataset in eine stärker normalisierte Version zu ändern, indem Werte aus mehreren Spalten eines einzelnen Datensatzes in mehrere Datensätze mit den gleichen Werten in einer einzelnen Spalte erweitert werden.

![Entpivotierungstransformation](media/data-flow/unpivot1.png "Entpivotierungsoptionen 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Gruppierung aufheben nach

![Entpivotierungstransformation](media/data-flow/unpivot5.png "Entpivotierungsoptionen 2")

Legen Sie zuerst die Spalten fest, nach denen Sie die Gruppierung für Ihre Entpivotierungsaggregation aufheben möchten. Legen Sie eine oder mehrere Spalten für das Aufheben der Gruppierung mit dem Pluszeichen (+) neben der Spaltenliste fest.

## <a name="unpivot-key"></a>Entpivotierungsschlüssel

![Entpivotierungstransformation](media/data-flow/unpivot6.png "Entpivotierungsoptionen 3")

Der Entpivotierungsschlüssel ist die Spalte, die Azure Data Factory von Spalte in Zeile pivotiert. Standardmäßig wird jeder eindeutige Wert im Dataset für dieses Feld in eine Zeile pivotiert. Sie können jedoch die Werte aus dem Dataset optional eingeben, die in Zeilenwerte pivotiert werden sollen.

## <a name="unpivoted-columns"></a>Entpivotierte Spalten

![Entpivotierungstransformation](media/data-flow//unpivot7.png "Entpivotierungsoptionen 4")

Wählen Sie abschließend den Spaltennamen zum Speichern der Werte für entpivotierte Spalten aus, die in Zeilen transformiert werden.

(Optional) Zeilen mit NULL-Werten können verworfen werden.

Im oben stehenden Beispiel ist „SumCost“ der ausgewählte Spaltenname.

![Bild der Spalten „PO“ (Bestellung), „Vendor“ (Lieferant) und „Fruit“ (Obst) vor und nach einer Entpivotierungstransformation, bei der die Spalte „Fruit“ als Entpivotierungsschlüssel verwendet wird.](media/data-flow/unpivot3.png)

Wenn Sie die Spaltenanordnung auf „Normal“ festlegen, werden alle neuen entpivotierten Spalten aus einem einzelnen Wert gruppiert. Durch Festlegen der Spaltenanordnung auf „Lateral“ werden die neuen entpivotierten Spalten gruppiert, die aus einer vorhandenen Spalte generiert wurde.

![Entpivotierungstransformation](media/data-flow//unpivot7.png "Entpivotierungsoptionen 5")

Im letzten Resultset der entpivotierten Daten werden die Spaltensummen angezeigt, die jetzt in separaten Zeilenwerten entpivotiert sind.

## <a name="next-steps"></a>Nächste Schritte

Verwenden der [Pivotierungstransformation](data-flow-pivot.md) zum Pivotieren von Zeilen zu Spalten
