# PruebatecnicaISS: Arquitectura de Datos Corporativa en Power BI
Documentación de un informe de Power BI como prueba técnica para ISS.

## 🎯 Contexto
- La organización cuenta con múltiples áreas que requieren informes específicos.  
- Todas las áreas consumen datos de fuentes compartidas (ERP, CRM, sistemas internos).

## 📌 Descripción
Este proyecto tiene como objetivo centralizar las fuentes de datos de la organización y garantizar un modelo de datos confiable mediante la creación de un **dataset maestro** en Power BI Service. El dataset es consumido por distintas áreas de negocio (Comercial, Finanzas, Operaciones, Marketing y Atención al Cliente), cada una con sus propios informes en sus propias áres de trabajo, pero basados en un modelo común y gobernado.

## 🛠️ Solución
1. **Workspace central (“Datos Corporativos”)**  
   - Creación de un espacio único para gestionar dataflows y datasets.Se administran las versiones subiendo

2. **Dataflows corporativos**  
   - Extracción y transformación de datos desde las fuentes.  
   - Limpieza, tipificación y estandarización de tablas.  

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
   - Documentación de KPIs y diccionario de datos.  

5. **Consumo por áreas**  
   - Cada área se conecta al dataset maestro en modo *live connection*.  
   - Crean informes en sus propios workspaces, garantizando consistencia en métricas.  

## 🔒 Gobernanza y versionado
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



