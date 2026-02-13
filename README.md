# PruebatecnicaISS: Arquitectura de Datos Corporativa en Power BI
Documentación de un informe de Power BI como prueba técnica para ISS.

## 🎯 Contexto
- La organización cuenta con múltiples áreas que requieren informes específicos.  
- Todas las áreas consumen datos de fuentes compartidas (ERP, CRM, sistemas internos).

## 📌 Descripción
Este proyecto tiene como objetivo centralizar las fuentes de datos de la organización y garantizar un modelo de datos confiable mediante la creación de un **dataset maestro** en Power BI Service. El dataset es consumido por distintas áreas de negocio (Comercial, Finanzas, Operaciones, Marketing y Atención al Cliente), cada una con sus propios informes en sus propias áres de trabajo, pero basados en un modelo común y gobernado.

## 🛠️ Solución
1. **Workspace central (“Datos Corporativos”)**  
   - Creación de un espacio único para gestionar dataflows y datasets.Se administran las versiones con Git o minimamente sharepoint y un flujo de aprobación donde los cambios queden documentados adecuadamente.

2. **Dataflows corporativos**  
   - Extracción y transformación de datos desde las fuentes.  
   - Limpieza, tipificación y estandarización de tablas.  
Aqui utilizaría parametros e incremental refresh para optimizar los tiempos de refresco.
Tambien es posible recurrir a un desarrollo de script en SQL en Oracle directamente para el desarrollo de una vista determinada en el caso de que el tamaño de los datos sea muy grande.
3. **Dataset maestro**  
   - Modelo estrella con tablas de hechos (Ventas) y dimensiones (Clientes, Productos, Provincias, objetivos).  
   - Definición de medidas corporativas en DAX (ej. Ventas, Margen).  
   - Optimización de rendimiento (eliminación de columnas innecesarias, formatos correctos, confirguración regional).

Origenes de datos
1- En este caso, se escoge conectarse a las tablas en formato csv pero se explica brevemente debajo cómo lo haría para conectarme a Oracle:
   En "Nuevo origen", selecciono "Oracle Database" y completo el servidor, el puerto, la instancia/servicio y las credenciales de acceso. Luego, el script de Oracle suministrado se ejecuta en esa instancia y se seleccionan las tablas y/o vistas ya desarrolladas para cargar en el Power Query.

2- En cuanto al archivo parquet, en Power BI Desktop se cargaron mediante el conector nativo de Parquet, ajustando tipos de datos y relacionando la columna provincia con la tabla clientes. En un entorno productivo, este archivo podría residir en un Data Lake y ser consumido directamente desde Power BI accediendo mediante URL y credenciales.

4. **Gobernanza y seguridad**  
   - Permisos **Build** otorgados a cada área para crear informes basados en el dataset. Se da acceso al area de trabajo (miembro) y luego "build" a nivel de dataset.
   - Roles de seguridad a nivel fila (RLS) para restringir acceso según provincia. Esto se configura en el Power Bi desktop pero luego se termina de configurar en el Power BI service. Para automatizarlo y facilitar el ABM de usuarios, conviene utilizar el username() o userprincipalname() y/o grupos de seguridad que tienen un campo relacionado con la ubicación.
Las excepciones se deben manejar en cada caso otorgando permisos a nivel de informe.

En cuanto a los usuarios de los informes, se recomienda el uso de apps que integren los informes necesarios donde se definan las audiencias que podrán ver uno o más informes en forma de solo lectura totalmente segura.

Los pasos serian:
1-creacion de workspace central administada por personal bi.
2-creacion de los dataflows conectandose a los origenes de datos en la forma mas limpia posible usando scripts en sql o python.
3-creacion de un dataset conectado al dataflow en el power bi desktop y se publica en el servicio en el workspace central.Se terminan de limpiar los datos,se crean las metricas adecuadas y se determinan las relaciones.Documentación de KPIs y diccionario de datos.  
4-se asignan los permisos adecuados en el workspace central y en el dataset (build).
5-cada area crea o solicita la creacion de su propia area de trabajo con los permisos adecuados. 
6-en Power BI desktop se crean un informe conectado al dataset principal en modo "live connection".Se crean las visuales necesarias para el analisis y la toma de decisiones.  Se crean los roles por provincia.
7-Se publica el informe de cada area en su respectiva area de trabajo.
8- una vez publicado en el servicio, se termina de configurar el RLS con usuarios definidos o grupos de usuario de Azure para que sólo vean los datos relativos de cada provincia.
9- Se crea la app para los usuarios finales definiendo audiencias y qué informes ve cada una dentro de cada área de trabajo.
   
5. **Consumo por áreas**  
   - Cada área se conecta al dataset maestro en modo *live connection*. La desventaja es que no se pueden hacer metricas a medida de cada area pero asegura que la calidad de los datos y que todos muestran "lo mismo".  
   - Crean informes en sus propios workspaces, garantizando consistencia en métricas.  

## 🔒 Gobernanza y versionado
-Uso de Power BI lineage view para visualizar dependencias entre datasets, informes y dashboards.
-Integración con un catálogo de datos (ej. Azure Purview, Collibra) para centralizar metadatos.
-Definición de estándares de nomenclatura y plantillas de documentación para asegurar consistencia.
-Automatización de metadatos mediante API de Power BI para mantener la documentación actualizada.

Se documentan:
-Fuentes de datos: origen, tipo de conexión, frecuencia de actualización.
-Transformaciones aplicadas: pasos en Power Query, cálculos en DAX.
-Definiciones de métricas: significado de KPIs y reglas de negocio.
-Responsables: contacto de soporte por dataset o informe.
-Dependencias: relación entre datasets compartidos e informes.

- **Roles definidos**:  
  - Equipo de BI administra dataset maestro.  
  - Áreas de negocio gestionan sus informes.  

- **Versionado**:  
  - Archivos `.pbix` almacenados en repositorio (SharePoint/Git).  
  - Uso de Tabular Editor y ALM Toolkit para control de cambios en el modelo tabular.  
  - Ambientes separados: Desarrollo → Pruebas → Producción. Se tiene un area de trabajo para cada ambiente y se va "promoviendo" a medida que se verifica.

## ✅ Resultados
- Consistencia en métricas entre todas las áreas.  
- Reducción de duplicación de modelos y esfuerzos.  
- Mayor control y seguridad en el acceso a datos.  
- Flexibilidad para que cada área cree informes propios sin perder alineación corporativa.
- La gobernanza asegura que los cambios sean controlados y documentados.  
- La documentación de KPIs evita interpretaciones distintas entre áreas.  



