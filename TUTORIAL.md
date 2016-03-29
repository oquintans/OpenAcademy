Building a module

Introducción
  Entramos en Netbeans, creamos un proyecto PHP from remote server, en url le pasamos la ip del servidor donde tenemos montado el odoo, ahora vamos a Manage.. y ahí añadimos una nueva conexión, host name la ip del servidor, user “root” y “password” castelao y de initial directory “/opt/odoo”, probamos la conexión, y si todo ha ido bien, le damos a Ok, y luego añadimos en upload Directory “/addons”, le damos a next y nos mostrará todas las carpetas descargables de addons, marcamos la nuestra, en este caso “openacademy” y le damos a finish, automáticamente nos descargara el módulo que creamos anteriormente con scaffold.
  Ahora podemos ir introduciendo utilidades al módulo mediante código y unas pequeñas modificaciones en el que nos traía de base.

Creación de módulo
  Usar el comando siguiente para crear un modulo vacio llamado Open Academy e instalarlo en odoo.
    Usar el comando odoo.py scaffold openacademy addons.
    Adaptar el manifest file a nuestro proyecto..

Definir un modelo
  Definir un nuevo modelo de datos llamado “Course” en el módulo openacademy. Cada curso ti un título y una descripción. Es obligatorio que los cursos tengan título.
  Editar el archivo openacademy/models.py para incluir una clase “Course”

Definir demonstration data
  Module data está declarado mediante data files, XML files con elementos <record>. Cada <record> element crea o actualiza un record de la base de datos..
  Crear demonstration data cumpliendo el modelo de “Courses” con unos cuantos cursos de demostración.
  Editar el archivo openacademy/demo.xml para incluir información.

Definir nuevas entradas de menú
  Definir nuevas entradas de menú para acceder a los cursos y sesiones dentro del submenú OpenAcademy. Un usuario debería ser capaz de:
    Mostrar una lista de todos los cursos.
    Crear/Modificar cursos.
  Crear openacademy/views/openacademy.xml con una acción y los menús activando dicha acción.
  Añadir a la lista de data en openacademy/__openerp__.py

Customizar form view usando XML
  Una vista (view) se declara como un record del módelo ir.ui.view. El tipo de vista vini indicado por el campo arch 
  Tree views, muestran los records en una tabla.El elemento raíz es <tree>
  Forms son usados para crear y editar records.Su elemento raiz es <form>.
  Creamos nuestra propia vista de los Cursos que muestra los nombres y las descripciones .

Notebooks
  En la vista de los Cursos,ponemos la descripción en otra etiqueta (notebook), haciendo más fácil añadir posteriormente otras etiquetas con más información.

Buscar cursos
  Search views customizan el campo de búsqueda asociado con el list view. Su elemento raíz es <search> y están compuestos por campos que definen los parámetros de búsqueda.

Crear modelo session
  Para el módulo  Open Academy, añadimos el modelo session , lo que nos permite ver las horas en las que se imparte cada curso y la gente que se puede apuntar.
  Crear un modelo para las sesiones. Cada sesión tiene un nombre, una fecha de inicio, una duración y un número de asientos. Añadir una acción y un menú para mostrarlos. Hacer el nuevo modelo visible mediante un menú..
    Crear clase Session en openacademy/models.py.
    Añadir acceso al objeto session en openacademy/view/openacademy.xml.
    
Relaciones Many2one 
  Many2one(other_model, ondelete='set null')
	  Es una relación simple a un objeto.
  Usando many2one, modificamos los modelos Course y Session para reflejar su relación con otros modelos.
    Cada curso tiene un usuario responsable; el valor del campo es un record del modelo built-in res.users.
    Cada sesión tiene un instructor; el valor del campo es un record del modelo built-in  res.partner.
    Cada sesión se relaciona con un curso; el valor del campo es un record del modelo openacademy.course y es necesario.
    Adaptamos la vista.
      Añadir los campos Many2one relevantes a los modelos.
      Añadirlos en las vistas.
      
Relaciones one2many inversas
  One2many(other_model, related_field)
    Una relación virtual inversa a  Many2one. Una One2many se comporta como contenedor de records, accediendo a sus resultados en un grupo de records:
  Usando la one2many, modifica los modelos para reflejar la relación entre cursos y sesiones.
    Modificamos la clase Course.
    Añadir el campo en el form view del curso.
    
Relaciones many2many múltiples
  Many2many(other_model)
    Relaciones bidireccionales múltiples, cada record de un lado puede relacionarse con cualquier número de récords en el otro lado.Comportándose como un contenedor de records.
  Usando el campo relacional many2many,modifica el modelo Session para relacionar todas las session a un grupo de asistentes. Los asistentes serán representados por partner records,lo referenciamos en el modelo res.partner.
    Modifica la clase Session.
    Añadir el campo en el form view.
    
Modificar contenido existente
  Usando herencia de modelos, modifica el modelo Partner para añadir un campo instructor boolean, y un campo many2many que corresponda con la relación session-partner.
  Usando herencia de vistas, mostra estos campos en el form view de partner.
  Crear archivo openacademy/partner.py e importarlo en __init__.py
  Crear archivo openacademy/views/partner.xml y añadirlo a __openerp__.py

Dominios y campos relacionales
  Los dominios son valores que guardan condiciones en  records. Un dominio es una lista de los criterios utilizados para seleccionar un subconjunto de los registros de un modelo . Cada criterio incluye un nombre de campo , un operador y un valor .
  Cuando seleccionemos al instructor de una sesión, solo los instructores deberían ser visibles.

Dominios más complejos
  Crear un nuevo partner categorías Teacher / Level 1 y Teacher / Level 2. El instructor de una sesión puede ser tanto un instructor como un profesor (de cualquier nivel).
    Modificar el dominio del modelo Session.
    Modificar openacademy/view/partner.xml para tener acceso a la categoría Partner

Campos computados
  Para crear un campo computado, crea un campo y establece su atributo computado en el nombre de un método . El método computado debería simplemente establecer el valor del campo computado en cada record self.
  El valor de un campo computado por lo general depende de los valores de otros campos en el registro computarizada . El ORM espera que el desarrollador especificar esas dependencias en el método de cálculo con el funcion depends().
    Añadir el porcentaje de asientos cogidos en el modelo sesion.
    Mostrar el campo en el tree y form views
    Mostrar el campos como una barra de progreso
  Añadir un campo computado a Session
  Mostrar el campo en la Session view

Objetos Activos – Valores por defecto
  Definir el valor por defecto de start_date a hoy.
  Añadir el campo active en la clase Session, y asignar a las sesiones activas por defecto.

Warning
  El mecanismo de "onchange" proporciona una manera para la interfaz del cliente de actualizar un formulario cada vez que el usuario haya rellenado un valor en un campo, sin salvar nada en la base de datos.
  Añadir un onchange explícito para avisar de valores inválidos, como un número negativo de asientos, o más participantes que asientos.

Añadir Python constraints
  Python constraint está definido como un método decorado con constrains(), e invocado en un recordset. El decorador especifica qué campos están envueltos en la constraint, de modo que esta es automáticamente evaluada cuando uno de ellos es modificado. 
  Añade una constraint que compruebe que el instructor no esté presente entre los asistente de su propia sesión.

Añadir SQL constraints
  SQL constraints son definidas mediante el modelo atributo _sql_constraints.Este asigna tres strings(name, sql_definition, message), donde name es un nombre SQL válido, sql_definition es una expresión table_constraint, y message es el mensaje de error.
  Añadir los siguientes constraints:
    Comprobar que el nombre del curso y la descripción son diferentes.
    Hacer el nombre del curso único.
  Como hemos añadido una constraint para que el nombre del curso sea único, no es posible usar la función duplicar. (Form ‣ Duplicate) .
  Re-implementar nuestro propio método "copy" que nos permitirá duplicar el objeto Curso, cambiando el nombre original por "Copia de [original name]".
