#  Proyecto Base de Datos – Colegio (PostgreSQL + Docker)

## Autor primera parte Fabian David Torres Fandiño

##  1. Diseño Conceptual y Lógico

El modelo se basa en un sistema escolar que gestiona estudiantes, acudientes, profesores, asignaturas, cursos, matrículas y notas.  
El **diagrama entidad-relación (E-R)** refleja las relaciones entre estas entidades principales:

- **Acudiente** → tiene muchos estudiantes.  
- **Estudiante** → pertenece a un acudiente y puede estar matriculado en varios cursos.  
- **Profesor** → dicta varios cursos.  
- **Asignatura** → se enseña en varios cursos.  
- **Curso** → combina profesor, asignatura y año.  
- **Matrícula** → relaciona estudiantes con cursos.  
- **Nota** → pertenece a una matrícula.

📎 **Evidencia:** diagrama E-R incluido en el archivo  
![entidad relacion](diagrama%20entidad%20relacion%201.png)
![powershell](powershell.png)
![docker](base%20de%20postgres%20en%20docker.png)
![docker2](postgres%20docker.png)
---

##  2. Construcción Física

La base de datos fue implementada en **PostgreSQL** utilizando **Docker**.  
El contenedor se crea con el siguiente comando:

```bash
docker run --name postgres_colegio -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=admin123 -e POSTGRES_DB=colegio_simple -p 5432:5432 -d postgres
```

Posteriormente, se copia y ejecuta el script SQL dentro del contenedor:

```bash
docker cp "colegio_postgres.sql" postgres_colegio:/colegio_postgres.sql
docker exec -it postgres_colegio bash
psql -U admin -d colegio_simple -f /colegio_postgres.sql
```

 El script `colegio_postgres.sql` incluye:
- **DDL (CREATE, ALTER, DROP):** creación de tablas, claves primarias y foráneas, restricciones y reglas de negocio.  
- **Relaciones:** todas las referencias usan claves foráneas con `ON DELETE CASCADE` o `ON DELETE SET NULL`.  

Ejemplo de definición de tabla:
```sql
CREATE TABLE estudiante (
  id SERIAL PRIMARY KEY,
  nombre VARCHAR(100) NOT NULL,
  fecha_nacimiento DATE,
  acudiente_id INT REFERENCES acudiente(id) ON DELETE SET NULL
);
```

---

##  3. Carga de Datos de Ejemplo

El script también contiene **sentencias DML (INSERT)** para probar la estructura:

```sql
INSERT INTO acudiente (nombre, telefono) VALUES
('Laura Martínez', '3001234567'),
('Pedro Rojas', '3017654321');

INSERT INTO estudiante (nombre, fecha_nacimiento, acudiente_id) VALUES
('Juan Pérez', '2010-05-12', 1),
('Ana Rojas', '2011-09-22', 2);
```

 Esto permite validar que las relaciones entre tablas funcionan correctamente (por ejemplo, matrícula y notas vinculadas con claves foráneas).

---

##  4. Conceptos Técnicos Aplicados

- **Normalización:** hasta tercera forma normal (3FN) para evitar redundancias.  
- **Integridad referencial:** mediante claves foráneas y restricciones `ON DELETE`.  
- **Restricciones de dominio:** tipo `CHECK` en notas para limitar valores entre 0 y 100.  
- **Unicidad:** índices únicos en combinaciones como `(profesor_id, asignatura_id, anio)` y `(estudiante_id, curso_id)`.  
- **Dockerización:** despliegue aislado y reproducible del entorno PostgreSQL.

---

##  5. Evidencia del Proyecto

- `base de postgres en docker.png` → Docker Desktop con contenedor activo.  
- `postgres docker.png` → Configuración del contenedor.  
- `powershell.png` → Ejecución de comandos y carga del script.  
- `diagrama entidad relacion 1.png` → Diagrama E-R de la base de datos.

---

##  6. Conclusión

Este proyecto demuestra el proceso completo de diseño e implementación de una base de datos relacional en PostgreSQL usando Docker.  
Incluye diseño conceptual, construcción física, inserción de datos y documentación técnica.
