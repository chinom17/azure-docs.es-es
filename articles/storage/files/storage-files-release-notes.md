---
title: "Notas de la versión de agente de Azure File Sync | Microsoft Docs"
description: "Notas de la versión de Azure File Sync"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: tamram
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 74f926743713bfd71eb524fdc2794cd7e187166e
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2018
---
# <a name="azure-file-sync-agent-release-notes"></a>Notas de la versión de agente de Azure File Sync
Azure File Sync (versión preliminar) permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

En este artículo se tratan las notas de las versiones compatibles del agente de Azure File Sync.

## <a name="supported-versions"></a>Versiones compatibles
Las siguientes versiones son compatibles con Azure File Sync:

| Número de versión del agente | Fecha de lanzamiento | Compatible hasta |
|----------------------|--------------|------------------|
| 2.0.11.0 | 2018-02-08 | Versión actual |
| 1.1.0.0 | 2017-09-26 | 2018-07-30 |

### <a name="azure-file-sync-agent-update-policy"></a>Directiva de actualización del agente de Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-1100"></a>Versión de agente 1.1.0.0
Las notas siguientes son para la versión de agente 1.1.0.0 que se lanzó en 9 de septiembre de 2017. Esta es la versión preliminar inicial de Azure File Sync.

### <a name="agent-installation-and-server-configuration"></a>Instalación del agente y configuración del servidor
Para más información sobre cómo instalar y configurar el agente de Azure File Sync con un servidor de Windows, consulte [Planeamiento de una implementación de Azure File Sync (versión preliminar)](storage-sync-files-planning.md) y [Cómo implementar Azure File Sync (versión preliminar)](storage-sync-files-deployment-guide.md).

- El paquete de instalación de agente (MSI) debe instalarse con permisos elevados (administrador).
- No se admite en las opciones de implementación de Windows Server Core ni Nano Server.
- Solo se admite en Windows Server 2016 y 2012 R2.
- El agente requiere al menos 2 GB de memoria física.

### <a name="interoperability"></a>Interoperabilidad
- Las aplicaciones antivirus, de copia de seguridad y de otro tipo que accedan a archivos organizados en niveles pueden provocar recuperaciones no deseadas, salvo que respeten el atributo sin conexión y pasen por alto la lectura del contenido de esos archivos. Para más información, consulte [Solución de problemas de Azure File Sync (versión preliminar)](storage-sync-files-troubleshoot.md).
- No use filtros de archivos del Administrador de recursos del servidor de archivos (u otros): pueden producir errores de sincronización sin fin si bloquean los archivos.
- La duplicación de un servidor registrado (clonación de máquinas virtuales incluida) puede producir resultados inesperados (en concreto, que la sincronización nunca converja).
- En el mismo volumen no se puede desduplicar datos y ordenar en niveles en la nube al mismo tiempo.
 
### <a name="sync-limitations"></a>Limitaciones de la sincronización
Los siguientes elementos no se sincronizan, pero el resto del sistema funcionará con normalidad:
- Rutas de acceso de más de 2048 caracteres
- Parte DACL de un descriptor de seguridad si es mayor que 2 K (esto solo es un problema si tiene más de unos 40 entradas de control de acceso en un elemento)
- Parte SACL del descriptor de seguridad (que se utiliza para la auditoría)
- Atributos ampliados
- Flujos de datos alternativos
- Puntos de repetición de análisis
- Vínculos físicos
- La compresión (si se establece en un archivo de servidor) no se conserva al sincronizarse los cambios con ese archivo desde otros puntos de conexión
- Los archivos cifrados con EFS (u otros cifrados con modo de usuario) que evitan que nuestro servicio lea los datos 
    
    > [!Note]  
    > Azure File Sync cifra siempre los datos en tránsito y los datos se pueden cifrar en reposo en Azure.
 
### <a name="server-endpoints"></a>Puntos de conexión del servidor
- Un punto de conexión de servidor solo puede crearse en un volumen NTFS. En este momento Azure File Sync no admite ReFS, FAT, FAT32 ni otros sistemas de archivos.
- Un punto de conexión de servidor puede no estar en el volumen del sistema (por ejemplo, C:\MyFolder no es una ruta de acceso aceptable, a menos que C:\MyFolder sea un punto de montaje).
- Los clústeres de conmutación por error solo son compatibles con discos en clúster, no con volúmenes compartidos de clúster (CSV).
- Los puntos de conexión de servidor no pueden anidarse, pero sí coexistir en el mismo volumen en paralelo.
- Eliminar un gran número de directorios de un servidor a la vez (más de 10 000) puede dar lugar a errores de sincronización: elimine los directorios en lotes de menos de 10 000 y asegúrese de que las operaciones de eliminación se han sincronizado correctamente antes de eliminar el siguiente lote.
- No se admite en la raíz de un volumen.
- No almacene un archivo de paginación de aplicación o sistema operativo en un punto de conexión de servidor.
 
### <a name="cloud-tiering"></a>Niveles de nube
- Para asegurarse de que se pueden recuperar los archivos correctamente, el sistema no puede ordenar por niveles automáticamente archivos nuevos o modificados durante 32 horas como máximo, incluidos los primeros niveles después de configurar un nuevo punto de conexión de servidor; proporcionamos un cmdlet de PowerShell para evaluar la ordenación por niveles de manera más eficaz sin esperar al proceso en segundo plano.
- Si se copia un archivo ordenado en niveles con Robocopy a otra ubicación, el resultante no se ordenará por niveles, pero se puede establecer el atributo sin conexión, porque Robocopy incluye incorrectamente ese atributo en las operaciones de copia.
- Al ver las propiedades de archivo desde un cliente de SMB, puede parecer que el atributo sin conexión se haya establecido incorrectamente debido a que SMB almacena en caché de los metadatos del archivo.