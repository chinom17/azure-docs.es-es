---
title: Uso de Pig en Hadoop con Escritorio remoto en HDInsight - Azure | Microsoft Docs
description: "Aprenda a utilizar el comando Pig para ejecutar instrucciones de Pig Latin desde una conexión de Escritorio remoto a un clúster de HDInsight basado en Windows."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 8b5e8e7f400a4494549c997e969a46ca90eb0ba5
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Ejecución de trabajos de Pig desde una conexión de Escritorio remoto
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Este documento ofrece un tutorial para que usar el comando Pig para ejecutar instrucciones de Pig Latin desde una conexión de Escritorio remoto a un clúster de HDInsight basados en Windows. Pig Latin le permite crear aplicaciones de MapReduce que describen las transformaciones de datos, en lugar de asignar y reducir las funciones.

> [!IMPORTANT]
> Remote Desktop solo está disponible para clústeres de HDInsight que usan Windows como sistema operativo. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Para HDInsight 3.4 o superior, consulte [Uso de Pig con HDInsight y SSH](apache-hadoop-use-pig-ssh.md) para más información sobre cómo ejecutar trabajos de Pig directamente en el clúster desde una línea de comandos.

## <a id="prereq"></a>Requisitos previos
Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight basado en Windows (Hadoop en HDInsight)
* Un equipo cliente con Windows 10, Windows 8 o Windows 7

## <a id="connect"></a>Conexión con el Escritorio remoto
Habilite el Escritorio remoto para el clúster de HDInsight y conéctese a él siguiendo las instrucciones dadas en [Conexión a los clústeres de HDInsight con RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Uso del comando Pig
1. Desde la sesión de Escritorio remoto, use el icono de **línea de comandos de Hadoop** del escritorio para iniciar la línea de comandos de Hadoop.
2. Use lo siguiente para iniciar el comando Pig:

        %pig_home%\bin\pig

    Aparecerá un símbolo del sistema de `grunt>` .
3. Introduzca la siguiente instrucción:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Este comando carga el contenido del archivo sample.log en LOGS. Puede ver el contenido del archivo mediante el siguiente comando:

        DUMP LOGS;
4. Transforme los datos aplicando una expresión regular para extraer solo el nivel de registro en cada registro mediante lo siguiente.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Puede usar **DUMP** para ver los datos después de la transformación. En este caso, `DUMP LEVELS;`.
5. Continúe aplicando transformaciones mediante las instrucciones siguientes. Utilice `DUMP` para ver el resultado de la transformación después de cada paso.

    <table>
    <tr>
    <th>Instrucción</th><th>Qué hace</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Quita las filas que contienen un valor nulo para el nivel de registro y almacena los resultados en FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Agrupa las filas por nivel de registro y almacena los resultados en GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Crea un nuevo conjunto de datos que contiene cada valor de registro único y cuántas veces se produce. Esto se almacena en FRECUENCIAS</td>
    </tr>
    <tr>
    <td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Ordena los niveles de registro por número (descendente) y los almacena en el resultado (RESULT)</td>
    </tr>
    </table>
6. También puede guardar los resultados de una transformación mediante la instrucción `STORE`. Por ejemplo, el siguiente comando guarda el valor `RESULT` en el directorio **/example/data/pigout** en el contenedor de almacenamiento predeterminado para el clúster:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Los datos se almacenan en el directorio especificado en los archivos denominados **part-nnnnn**. Si el directorio ya existe, recibirá un mensaje de error.
   >
   >
7. Para salir del aviso de grunt, introduzca la siguiente instrucción.

        QUIT;

### <a name="pig-latin-batch-files"></a>Archivos por lotes de Pig Latin
También puede usar el comando de Pig para ejecutar Pig Latin contenido en un archivo.

1. Después de salir del aviso de grunt, abra el **Bloc de notas** y cree un nuevo archivo denominado **pigbatch.pig** en el directorio **%PIG_HOME%**.
2. Escriba o pegue las siguientes líneas en el archivo **pigbatch.pig** y luego guárdelo cuando haya terminado.

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Utilice lo siguiente para ejecutar el archivo **pigbatch.pig** mediante el comando de Pig.

        pig %PIG_HOME%\pigbatch.pig

    Una vez completado el trabajo por lotes, debería ver el siguiente resultado, que debe ser el mismo que cuando ha utilizado `DUMP RESULT;` en los pasos anteriores.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Resumen
Como puede ver, el comando de Pig permite ejecutar interactivamente operaciones de MapReduce mediante Pig Latin, así como ejecutar trabajos de Pig Latin almacenados en un archivo por lotes.

## <a id="nextsteps"></a>Pasos siguientes
Para obtener información general sobre Pig en HDInsight, siga estos pasos:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)
