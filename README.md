# Taller de Active Record: Guía básica

Objetivo del taller: Introducir a los participantes en el uso de Active Record, la capa de abstracción de base de datos de Ruby on Rails, para interactuar con bases de datos de manera sencilla y eficiente.

> Duración: 1 horas

> Requisitos previos: Conocimientos básicos de Ruby y de bases de datos relacionales.

## Materiales necesarios:

- Computadoras con Ruby y Rails instalados
- Base de datos SQLite configurada en el proyecto Rails

## Introducción a Active Record (15 minutos)

### 1. ¿Qué es Active Record?
  - Active Record es el patrón de diseño que Ruby on Rails utiliza para interactuar con bases de datos relacionales.
  - Se encarga de mapear las tablas de la base de datos a clases de Ruby y viceversa, facilitando así la manipulación de los datos.
  - Active Record es parte del patrón Modelo-Vista-Controlador (MVC) en Ruby on Rails, donde el modelo representa la capa de acceso a datos.

  #### 1.1 Componentes principales
  - Modelos: Representan las tablas de la base de datos como clases de Ruby, con métodos para realizar operaciones CRUD.
  - Migraciones: Permiten realizar cambios en la estructura de la base de datos de manera controlada y reversible.
  - Consultas: Active Record proporciona métodos para realizar consultas a la base de datos de forma sencilla y segura.

  #### 1.2 Historia y evolución de Active Record en Rails
  - Active Record fue introducido por primera vez en Rails 0.5 en el año 2004.
  - Ha evolucionado para convertirse en una parte central y poderosa de Rails, simplificando drásticamente el manejo de la base de datos.
### 2. ¿Por qué usar Active Record?
  - Simplifica la interacción con la base de datos al eliminar la necesidad de escribir consultas SQL manualmente.
  - Abstrae la complejidad de las operaciones de base de datos, como la gestión de transacciones y la validación de datos.
  - Permite definir relaciones entre modelos de manera sencilla y realizar consultas avanzadas de forma intuitiva.
  - Proporciona métodos y convenciones que hacen que el código sea más legible y mantenible.
- Ofrece características avanzadas como validaciones, asociaciones y consultas complejas.
### 3. Convenciones de nomenclatura en Active Record
  - En Ruby on Rails, la estructura de un proyecto típico sigue una convención sobre configuración, lo que significa que Rails asume una estructura predeterminada para los archivos y carpetas, lo que facilita la organización y el desarrollo de la aplicación. A continuación, se describe la estructura básica de un proyecto Rails destacando las carpetas y archivos relevantes para Active Record:

    1. **app:** Esta carpeta contiene el código principal de la aplicación. Algunas carpetas y archivos relevantes son:
        - **models:** Aquí se colocan los archivos de los modelos de Active Record. Cada modelo suele estar en un archivo separado en el que se define la clase que representa a la tabla de la base de datos.
    2. **db:** Contiene los archivos relacionados con la base de datos de la aplicación. Algunos archivos y carpetas importantes son:
        - **migrate:** Aquí se almacenan los archivos de migración de base de datos. Las migraciones se utilizan para modificar la estructura de la base de datos de manera controlada.

        - **schema.rb:** Este archivo contiene la definición actual de la base de datos en formato Ruby. Es generado automáticamente por Rails a partir de las migraciones y se utiliza para configurar la base de datos en un nuevo entorno.
    3. **config:** Contiene archivos de configuración para la aplicación. Algunos archivos relevantes para Active Record son:

        - **database.yml:** Este archivo se utiliza para configurar la conexión a la base de datos, incluyendo el tipo de base de datos, el nombre, el usuario y la contraseña.
        - **Initializers:** Puedes crear tus propios `initializers` en la carpeta `config/initializers` para configurar aspectos específicos de Active Record o de tu aplicación en general. Por ejemplo, podrías tener un initializer para establecer el nivel de logging de Active Record o para cargar extensiones específicas de Active Record.


  - Active Record sigue el principio de "convención sobre configuración", lo que significa que asume ciertas configuraciones por defecto basadas en convenciones, lo que reduce la cantidad de código necesario para configurar la base de datos.
    > Por ejemplo, si tienes una clase User en Ruby, Active Record asume automáticamente que la tabla correspondiente en la base de datos se llama users.

  - Active Record espera que las columnas de una tabla sigan ciertas convenciones de nombres para funcionar sin necesidad de configuración adicional.
    > Por ejemplo, una columna de tipo datetime para almacenar la fecha y hora de creación de un registro debe llamarse created_at, y una columna de tipo datetime para almacenar la fecha y hora de la última actualización de un registro debe llamarse updated_at.
  - Active Record pluraliza automáticamente los nombres de las tablas y singulariza los nombres de los modelos cuando es necesario.
  - Active Record asume por defecto que cada tabla tiene una columna llamada id como clave primaria. Si tu tabla no sigue esta convención, puedes configurar la columna de clave primaria manualmente en el modelo.
  - Active Record utiliza convenciones para determinar las relaciones entre modelos.
      > Por ejemplo, si tienes un modelo Post que belongs_to :user, Active Record asumirá que la tabla posts tiene una columna llamada user_id que se utiliza como clave externa para la relación con la tabla users.
  - Las migraciones en Rails siguen una convención de nombres basada en números de versión para mantener un orden. Cada migración se nombra con un número de versión seguido de un guion bajo y un nombre descriptivo, por ejemplo, `20240328120000_create_users_table.rb.`
  - Cuando se define una asociación many-to-many entre dos modelos, Active Record utiliza una convención para el nombre de la tabla intermedia.
      > Por ejemplo, si tienes una relación has_and_belongs_to_many :users en un modelo Group, Active Record buscará una tabla llamada groups_users para almacenar la relación.

## Crear un nuevo proyecto Rails.

### 1. Configurar la base de datos SQLite en el proyecto.
`rails new active_record_workshop`
```yml
  default: &default
    adapter: sqlite3
    pool: 5
    timeout: 5000

  development:
    <<: *default  
    database: db/development.sqlite3
    
  test:
    <<: *default
    database: db/test.sqlite3

  production:
    <<: *default
    database: db/production.sqlite3
```

- Es posible agrupar los comandos que sean redundantes en un solo grupo que sera llamado cuando sea requerido.
- Nunca deben colocar las variables de forma explicita en el archivo yml
### 2. Configurar la base de datos SQLite en el proyecto.
  `rails new active_record_workshop -d postgresql`

  ```yml
    default: &default
      adapter: postgresql
      encoding: unicode
      # Usando Variables de ambiente
      pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
      # Usando Credentials
      username: <%= Rails.application.credentials.database[:username] %>
      password: <%= Rails.application.credentials.database[:password] %>
      # Usando secrets
      host: <%= Rails.application.secrets.database.host %>
      port: 5432

    development:
      <<: *default
      database: active_record_workshop_development

    test:
      <<: *default
      database: active_record_workshop_test
          
    production:
      <<: *default
      database: active_record_workshop_production
      password: <%= ENV["ACTIVE_RECORD_WORKSHOP_DATABASE_PASSWORD"] %>
  ```
  - Es posible utilizar comandos de ruby dentro del archivo yml para cargar informacion ya sea desde las variables de ambiente, el archivo credentials o secrets para versiones mas antiguas de ruby.

  - No se recomienda utilizar SQLite para ambientes de produccion.
## Modelos
### 1. Que es un modelo
Un modelo en Active Record es una clase de Ruby que representa una tabla en la base de datos. Los modelos en Active Record contienen lógica de negocio y métodos para interactuar con los datos en la base de datos.

### 2. Crear un modelo en rails
  Utilizamos el comando rails generate model seguido del nombre del modelo y los nombres de las columnas para crear un nuevo modelo en Rails. Por ejemplo, para crear un modelo de usuario con los campos name y email, usaríamos el siguiente comando:

  ```bash
    rails generate model Product name:string price:integer
  ```
  ```bash
        invoke  active_record
        create    db/migrate/20240328160122_create_products.rb
        create    app/models/product.rb
        invoke    test_unit
        create      test/models/product_test.rb
        create      test/fixtures/products.yml
  ```

  Un modelo en Active Record hereda de la clase ApplicationRecord, que a su vez hereda de ActiveRecord::Base. Suelen tener métodos para validar los datos antes de guardarlos en la base de datos y para definir relaciones con otros modelos.

  En la tabla de models ubicada dentro de nuestra carpeta app, los archivos corresponden a modelos y son cargados por el sistema automaticamente, el nombre del archivo debe estar en snake case (`nombre_de_modelo.rb` separado por raya baja).

  Por otra parte dentro de nuestro codigo, se utiliza camel case (`NombreDeModelo` las letras juntas, cada palabra comienza en mayuscula). Los unicos casos donde no utilizaremos camel case es al referirnos a una asociacion, sea para establecerla o para consultarla.

  Las asociaciones `has_many` y `has_and_belongs_to_many` se deben pluralizar, automaticamente active record interpreta a que modelo se esta refiriendo, tener mucho cuidado con los nombres en pluralizados en ingles ya que una falta ortografica puede ocacionar errores.

  ```rb
    class Product < ApplicationRecord
      belongs_to :category
      has_many :order_items #observar que en este caso no usamos camel case y tambien pluralizamos.
    end
  ```

#### Enum en los modelos
Los enumeradores (enums) en los modelos de Rails te permiten definir atributos que representan estados o valores específicos de una manera más legible y fácil de usar en tu código. Para definir un enumerador en un modelo, utiliza el método enum seguido del nombre del atributo y un hash que mapea los valores de los enumeradores a enteros en la base de datos.

```ruby
  class User < ApplicationRecord
    enum role: { admin: 0, user: 1, guest: 2 }
  end
```

Una vez que has definido un enumerador, puedes acceder a él a través de métodos generados automáticamente por Rails. Por ejemplo, puedes utilizar User.roles para obtener una lista de todos los valores del enumerador, y User.role para obtener el valor actual del atributo role de un usuario específico. Puedes utilizar los valores del enumerador en consultas ActiveRecord de la misma manera que cualquier otro atributo. Por ejemplo, puedes buscar todos los usuarios con el rol de administrador utilizando User.where(role: :admin).

```ruby
  User.roles
  user = User.first
  user.role
```


### 3. Crear una migración para agregar una tabla a la base de datos.
  Podemos notar que al correr el comando, se crearon varios archivos, de los cuales los mas relevantes son el modelo y la migracion. Ya hemos visto el brevemente el modelo y ahora hablaremos de la migracion.

  Un archivo de migración  es un document Ruby que contiene instrucciones para modificar la estructura de la base de datos. Estos archivos se encuentran en la carpeta db/migrate de tu aplicación Rails y se utilizan para realizar cambios en la base de datos de manera controlada y reversible.

  ```rb
  class CreateProducts < ActiveRecord::Migration[6.0]
    def change
      create_table :products do |t|
        t.string :name
        t.integer :price

        t.timestamps
      end
    end
  end
  ```
  En este ejemplo, la migración crea una tabla products con dos columnas (name y price) y las columnas de timestamp (created_at y updated_at) que se utilizan para registrar la fecha y hora de creación y actualización de un registro, respectivamente. El id tambien es agregado de forma automatica.

  Finalmente, en este primer ejemplo se debe correr el comando para hacer efectivos estos cambios en la base de datos. Active Record conserva las migraciones como un control de versiones de los cambios en la estructura de la base de datos. Para esto utilizamos el comando: `rails db:migrate`.

  Tambien es posible revertir los cambios usando el comando `rails db:rollback`.

## Interactuar con modelos
### Leer registros de la base de datos.
Active Record utiliza consultas parametrizadas para prevenir la inyección de SQL. Siempre que sea posible, utiliza métodos de consulta de Active Record en lugar de interpolación de cadenas para construir consultas SQL.

```ruby
  User.where("email = ?", params[:email])
```

1. **find**
  - El método find se utiliza para recuperar un único registro de la base de datos por su ID.
    
  - Si el registro con el ID especificado no existe, find arrojará una excepción ActiveRecord::RecordNotFound.
    
  - Sintaxis:    
    ```ruby
      user = User.find(1)  # Busca un usuario con ID 1
    ```
  - Si se utiliza find para buscar múltiples registros en un bucle, puede resultar en múltiples consultas individuales a la base de datos, lo que puede ser menos eficiente que una sola consulta utilizando where.

  - *find* no permite aplicar condiciones adicionales a la consulta más allá del ID del registro.
  - *find* tiene otras sintaxis posibles que concatenan el resultado con otra operacion, como son: 
      ```ruby
        # Este método se utiliza para buscar un único registro que cumpla con las   condiciones especificadas. A diferencia de find, find_by no arroja una excepción si no se encuentra ningún registro, simplemente devuelve nil.
        user = User.find_by(name: 'John')
        user = User.find_by_name('John')

        # En caso de que no exista el registro, sera creado
        user = User.find_or_create_by(name: 'John')

        # En caso de que no exista el registro, se inicializara con los valores pero no sera guardado en la base de datos.
        user = User.find_or_initialize_by(name: 'John')
      ```
2. **where**
  - El método where se utiliza para realizar consultas más generales y obtener una colección de registros que cumplan con ciertas condiciones.
  - *where* devuelve una relación ActiveRecord que puede contener cero, uno o varios registros que cumplan con las condiciones especificadas.
  - Sintaxis:
      ```ruby
        users = User.where(name: 'John')  # Busca usuarios con nombre 'John'
      ```
  - Si se utiliza where para buscar un único registro, es posible que se genere una consulta SQL más compleja que utilice índices o escaneo de tabla, lo que podría ser menos eficiente que una consulta directa por ID.
3. *exists?*: Este método se utiliza para verificar si existen registros que cumplan con las condiciones especificadas. Retorna true si al menos un registro es encontrado, de lo contrario retorna false.
    ```ruby
      exists = User.exists?(name: 'John')
    ```
4. *first:* Retorna el primer registro que cumple con las condiciones especificadas.
    ```ruby
      first_user = User.where(active: true).first
    ```
5. *last:* Retorna el último registro que cumple con las condiciones especificadas.
    ```ruby
      last_user = User.where(active: true).last
    ```
6. *take:* Retorna un registro arbitrario que cumple con las condiciones especificadas. Es útil cuando solo necesitas obtener un registro sin importar cuál sea.
    ```ruby
      random_user = User.where(active: true).take
    ```
7. find_in_batches: realiza una busqueda similar al where, pero este requiere utilizar un bloque de codigo con las acciones que se realizaran. Tiene algunos parametros especiales como: batch_size, start, finish, error_on_ignore, order. Ejemplo:
    ```ruby
      User.find_in_batches(batch_size: 1000) do |users|
        users.each do |user|
          # Procesar el usuario
        end
      end
    ```
8. *pluck:* Este método se utiliza para obtener un array de valores de una columna específica en lugar de obtener objetos ActiveRecord completos. Esto puede ser útil cuando solo necesitas una lista de valores específica.
    ```ruby
      names = User.where(active: true).pluck(:name)
    ```
9. *select*: Este método se utiliza para seleccionar un subconjunto de columnas de la tabla en lugar de seleccionar todas las columnas. Esto puede ser útil para reducir la cantidad de datos recuperados de la base de datos.
    ```ruby
      users = User.select(:id, :name)
    ```
10. *distinct*: Este método se utiliza para eliminar duplicados de los resultados de una consulta. Puede ser útil cuando necesitas asegurarte de obtener resultados únicos.
    ```ruby
      unique_names = User.select(:name).distinct
    ```
11. *group*: Este método se utiliza para agrupar los resultados de una consulta según los valores de una columna específica. Puede ser útil para realizar operaciones de agregación, como contar o sumar valores.
    ```ruby
      user_counts = User.group(:role).count
    ```
12. *joins*: Este método se utiliza para realizar una operación de JOIN entre dos tablas. Puede ser útil para combinar información de diferentes tablas en una sola consulta.
    ```ruby
      users = User.joins(:posts).where('posts.published' => true)
    ```
13. *includes*: Este método se utiliza para cargar asociaciones de manera eficiente para evitar el problema del N+1. Puede ser útil cuando necesitas acceder a datos de asociaciones de manera eficiente.
    ```ruby
      users = User.includes(:posts)
    ```
14. *eager_load*: se utiliza para cargar asociaciones de manera anticipada junto con los registros principales en una sola consulta a la base de datos.
    La diferencia principal entre eager_load y includes es cómo se realiza la carga de datos. eager_load utiliza un LEFT OUTER JOIN para cargar los registros principales y las asociaciones en una sola consulta, lo que significa que todos los registros se cargarán, incluso si algunas asociaciones no tienen datos correspondientes. Por otro lado, includes realiza dos consultas separadas: una para los registros principales y otra para las asociaciones, utilizando una estrategia conocida como "lazy loading".
    ```ruby
      users = User.eager_load(:posts).where('posts.published' => true)
    ```

#### 1. Consulta básica:
  ```ruby
    # Obtener todos los usuarios
    users = User.all

    # Obtener solo los nombres de los usuarios
    user_names = User.pluck(:name)

    # Contar el número total de usuarios
    user_count = User.count

    # Obtener un usuario aleatorio
    random_user = User.order('RANDOM()').first
  ```
#### 2. Consulta con condiciones:
  ```ruby
    # Obtener usuarios con un nombre específico
    users = User.where(name: 'John')

    # Obtener usuarios que tienen un valor nulo en la columna email
    users = User.where(email: nil)

    # Obtener usuarios que tienen un valor no nulo en la columna email
    users = User.where.not(email: nil)
  ```

  ```ruby
    # Obtener un solo registro
    user = User.find(1) # Indicando el numero de id.
  ```
#### 3. Consulta con ordenamiento:
  ```ruby
    # Obtener usuarios ordenados por nombre de forma ascendente
    users = User.order(name: :asc)
  ```
#### 4. Consulta con limite y desplazamiento:
  ```ruby
    # Obtener los primeros 5 usuarios después de omitir los primeros 2
    users = User.limit(5).offset(2)

    # Obtener el número de posts por usuario
    user_post_counts = Post.group(:user_id).count
  ```

#### 5. Consulta con inclusion de asociaciones:
  ```ruby
    # Obtener todos los posts con los comentarios asociados precargados
    posts = Post.includes(:comments)
  ```
#### 6. Consulta con condicionales:
  ```ruby
    # Obtener usuarios con un nombre específico y un email específico
    users = User.where(name: 'John', email: 'john@example.com')
  ```
#### 7. Consulta con condicionales complejas
  ```ruby
    # Obtener usuarios cuyo nombre contiene 'John'
    users = User.where('name LIKE ?', '%John%')

    # Obtener usuarios con un nombre que comience con 'J' o 'M'
    users = User.where('name LIKE ? OR name LIKE ?', 'J%', 'M%')

    # Obtener usuarios cuyo ID está en una lista de valores
    users = User.where(id: [1, 2, 3])

    # Obtener usuarios cuya fecha de creación está entre dos fechas
    users = User.where(created_at: (Date.today - 7.days)..Date.today)

    # Obtener usuarios cuyo nombre comienza con una vocal
    users = User.where('name SIMILAR TO ?', '[AEIOU]%')

    # Obtener usuarios creados después de una fecha específica
    users = User.where('created_at > ?', Date.parse('2022-01-01'))
  ```

  - Active Record proporciona métodos de consulta que siguen una convención de nombres clara y consistente.
    > Por ejemplo, el método find_by_email busca un registro por su dirección de correo electrónico, where se utiliza para agregar condiciones a una consulta, y order se utiliza para ordenar los resultados.
#### 8. Los problemas de n+1

  N+1 es un problema común en Active Record que ocurre cuando se realizan consultas adicionales a la base de datos dentro de un bucle, lo que puede resultar en un gran número de consultas innecesarias y afectar negativamente el rendimiento de la aplicación. Aquí hay un ejemplo para ilustrar el problema:

  Supongamos que tienes un modelo Post que tiene una asociación belongs_to :user y quieres mostrar una lista de posts junto con el nombre de usuario de cada post. Si haces esto de la siguiente manera:

  ```ruby
    @posts = Post.all
    @posts.each do |post|
      puts "#{post.title} - #{post.user.name}"
    end
  ```

  Esto generará una consulta para obtener todos los posts (Post.all), pero luego dentro del bucle each, se generará una consulta adicional para obtener el nombre de usuario (post.user.name) para cada post. Si tienes 100 posts, esto resultará en 101 consultas a la base de datos: una para obtener todos los posts y 100 adicionales para obtener los nombres de usuario de cada post.

  Para evitar este problema, puedes utilizar el método includes para cargar las asociaciones necesarias de antemano y evitar las consultas adicionales dentro del bucle. Por ejemplo:

  ```ruby
    @posts = Post.includes(:user).all
    @posts.each do |post|
      puts "#{post.title} - #{post.user.name}"
    end
  ```

  Esto cargará los usuarios asociados a todos los posts en una sola consulta, evitando así el problema del N+1. Es importante identificar y corregir estos problemas en tu código para mejorar el rendimiento de tu aplicación Rails.
### Scopes
  Los scopes en Active Record son métodos de clase que devuelven una relación ActiveRecord y que se pueden encadenar con otros métodos de consulta. Se utilizan para definir consultas reutilizables que pueden ser aplicadas a modelos en diferentes partes de la aplicación.

  Para crear un scope, se utiliza el método scope dentro de la clase del modelo. Por ejemplo, para definir un scope que devuelve todos los usuarios activos, puedes hacer lo siguiente:

  ```ruby
    class User < ApplicationRecord
      scope :active, -> { where(active: true) }
    end
  ```

  Los scopes se pueden encadenar para construir consultas más complejas. Por ejemplo, para obtener todos los usuarios activos y ordenarlos por nombre, puedes hacer lo siguiente:

  ```ruby
    users = User.active.order(:name)
  ```

  Los scopes pueden aceptar parámetros para hacerlos más flexibles. Por ejemplo, para definir un scope que devuelve usuarios activos o inactivos según un parámetro, puedes hacer lo siguiente:

  ```ruby
    class User < ApplicationRecord
      scope :status, -> (active) { where(active: active) }
    end
  ```
  Y luego usarlo así:
  ```ruby
    active_users = User.status(true)
    inactive_users = User.status(false)
  ```

### Actualizar registros en la base de datos.
1. Crear registros:
    1. *create*:
        ```ruby
          user = User.create(name: 'John', email: 'john@example.com')

          # Los bang method como se le llama en rails, retornan un exception si la validacion falla
          user = User.create!(name: 'John', email: 'john@example.com')
        ```
    2. *new*: El método new se utiliza para crear una nueva instancia de un modelo en memoria, pero no la guarda en la base de datos. Puedes utilizar new para preparar una instancia con datos y luego guardarla explícitamente utilizando el método save.

        ```ruby
          user = User.new(name: 'John', email: 'john@example.com')
        ```
2. Actualizar registro:
    1. *update*: se utiliza para actualizar uno o varios registros en la base de datos. Puedes utilizarlo de varias formas para actualizar los atributos de un registro existente.
        ```ruby
          user = User.find(1)
          user.update(name: 'Johnny', email: 'johnny@example.com')

          user.update(name: '')  # Retorna false si name no puede estar en blanco
        ```

        ```ruby
          User.where(role: 'admin').update(active: true)
        ```

    2. Actualizar sin validaciones: Si deseas actualizar un registro sin ejecutar las validaciones del modelo, puedes utilizar update_columns o update_attribute.
        ```ruby
          user.update_columns(name: 'Johnny')  
          user.update_attribute(:name, 'Johnny')
        ```
    3. *update_all*: es un método de clase que se utiliza para actualizar varios registros que cumplan con ciertas condiciones en la base de datos. No ejecuta callbacks de Active Record ni actualiza los atributos updated_at.
        ```ruby
          User.where(role: 'admin').update_all(active: true)
        ```


## Relaciones
Las relaciones en Rails son una forma de asociar modelos entre sí, lo que permite representar las relaciones entre diferentes tipos de datos en una base de datos relacional. Rails proporciona varios tipos de relaciones que puedes utilizar para modelar diferentes situaciones:

1. belongs_to: Define una relación de pertenencia donde un modelo "pertenece a" otro modelo. Por ejemplo, en una relación de belongs_to, un modelo Comment puede pertenecer a un modelo Post.
    ```ruby
      class Comment < ApplicationRecord
        belongs_to :post
      end
    ```
2. has_one: Define una relación de uno a uno donde un modelo "tiene uno" de otro modelo. Por ejemplo, en una relación de has_one, un modelo Profile puede tener un User.
    ```ruby
      class Comment < ApplicationRecord
        belongs_to :post
      end
    ```
3. has_many: Define una relación de uno a muchos donde un modelo "tiene muchos" de otro modelo. Por ejemplo, en una relación de has_many, un modelo User puede tener muchos Comment.
    ```ruby
      class User < ApplicationRecord
        has_many :comments
      end
    ```
4. has_many :through: Define una relación de muchos a muchos a través de un tercer modelo "intermedio". Por ejemplo, en una relación has_many :through, un modelo Doctor puede tener muchos Patient a través de un modelo Appointment.
    ```ruby
      class Doctor < ApplicationRecord
        has_many :appointments
        has_many :patients, through: :appointments
      end
    ```
5. has_and_belongs_to_many: Define una relación de muchos a muchos directa entre dos modelos. Por ejemplo, en una relación has_and_belongs_to_many, un modelo Article puede tener muchos Tag y viceversa.
    ```ruby
      class Article < ApplicationRecord
        has_and_belongs_to_many :tags
      end
    ```
### Trucos y Convenciones
1. Nombrar relaciones: deben ser descriptivos y reflejar la naturaleza de la relación. Por ejemplo, si un usuario tiene muchos comentarios, la relación podría llamarse `has_many :comments` con su debida pluralizacion.
2. Nombres de claves foráneas: Por convención, Rails espera que las claves foráneas sigan un patrón específico: modelo_id. Por ejemplo, si un usuario tiene muchos comentarios, la clave foránea en la tabla de comentarios generalmente se llamaría user_id.
3. Inversión de relaciones: Rails intenta inferir la inversión de las relaciones basándose en el nombre de la clase del modelo. Por ejemplo, si un usuario tiene muchos comentarios, se esperaría que la clase Comment tenga una relación belongs_to :user.
4. Uso de dependent: Cuando una relación depende de otra y la eliminación de un registro en una tabla debe resultar en la eliminación de registros relacionados en otra tabla, puedes utilizar la opción dependent para especificar el comportamiento. Por ejemplo, `dependent: :destroy` eliminará automáticamente los registros relacionados cuando se elimine el registro principal.
5. Nombres de tablas intermedias en relaciones has_many :through: Para relaciones has_many :through, la tabla intermedia debe tener un nombre que combine los nombres de los modelos relacionados en orden alfabético y en plural. Por ejemplo, si tienes modelos Doctor y Patient relacionados a través de Appointment, la tabla intermedia podría llamarse appointments.

6. Prefijo de tabla para relaciones has_and_belongs_to_many: Para relaciones has_and_belongs_to_many, la tabla intermedia debe tener un nombre que combine los nombres de los modelos relacionados en orden alfabético y en plural, pero con el nombre del modelo base de la relación en singular como prefijo. Por ejemplo, si tienes modelos Article y Tag relacionados, la tabla intermedia podría llamarse articles_tags.

7. Uso de inverse_of: Para especificar la inversa de una relación en modelos asociados, puedes utilizar la opción inverse_of. Esto ayuda a Rails a optimizar la carga de asociaciones y a evitar problemas de referencia circular en las relaciones.


## Validar datos antes de guardar en la base de datos.
1. Validaciones básicas: puedes utilizar en tus modelos para asegurarte de que los datos sean válidos antes de guardarlos en la base de datos.
    ```ruby
      class User < ApplicationRecord
        validates :name, presence: true
        validates :email, presence: true, uniqueness: true
      end
    ```
2. Mensajes de error: Puedes personalizar los mensajes de error que se muestran cuando una validación falla para proporcionar una mejor retroalimentación al usuario.
    ```ruby
      class User < ApplicationRecord
        validates :name, presence: { message: "no puede estar en blanco" }
      end
    ```
    Si no se proporciona un mensaje personalizado para una validación en Rails, se utilizará un mensaje predeterminado que depende del tipo de validación. Puedes editar los mensajes de error al modificar los archivos de internacionalizacion ubicados en `config/locales/en.yml`
    ```yaml
      en:
        activerecord:
          errors:
            models:
              user:
                attributes:
                  name:
                    blank: "no puede estar en blanco"
                  email:
                    blank: "no puede estar en blanco"
                    taken: "ya está en uso"
    ```
3. Validaciones condicionales: Puedes utilizar validaciones condicionales para aplicar una validación solo en ciertas condiciones.
    ```ruby
      class User < ApplicationRecord
        validates :email, presence: true, if: :active?

        def active?
          status == 'active'
        end
      end
    ```
4. Validaciones de formato: Puedes utilizar validaciones de formato para asegurarte de que los datos cumplan con un formato específico, como una dirección de correo electrónico válida.
    ```ruby
      class User < ApplicationRecord
        validates :email, format: { with: URI::MailTo::EMAIL_REGEXP }
      end
    ```
5. Validaciones personalizadas: También puedes crear tus propias validaciones personalizadas para validar datos de forma específica.
    ```ruby
      class User < ApplicationRecord
        validate :must_be_friends

        def must_be_friends
          errors.add(:base, 'Debe ser amigo para enviar un mensaje') unless friends_with?(recipient)
        end
      end
    ```
6. Para verificar si un modelo cumple con las validaciones requeridas usamos el metodo `valid?`. Este método devuelve `true` si no hay errores de validación en el modelo y `false` si hay errores de validación.
    ```ruby
      class User < ApplicationRecord
        validates :name, presence: true
      end

      user = User.new(name: '')
      user.valid? # output: false
      user.name = 'Robert'
      user.valid? # output: true
    ```
    Luego de usar el metodo `valid?` o cualquier otro que realize las validaciones de lugar, siempre que encontremos algun error podemos obtener los mensajes usado el comando `errors`
    ```ruby
      user.valid?
      user.errors.full_messages  # Devuelve una lista de mensajes de error
    ```
7. Puedes utilizar los valores del enumerador en las validaciones de Active Record. Por ejemplo, puedes validar que el valor del atributo role esté incluido en la lista de roles permitidos.
    ```ruby
      class User < ApplicationRecord
        enum role: { admin: 0, user: 1, guest: 2 }
        validates :role, inclusion: { in: roles.keys }
      end
    ```

## Nested Attributes

Nested attributes (atributos anidados) es una característica de Active Record en Rails que te permite guardar atributos de modelos asociados a través de su modelo principal. Esto es útil cuando quieres crear o actualizar registros en múltiples tablas relacionadas en una sola operación. Aquí te explico cómo funciona y cómo se usa:

```ruby
  class Author < ApplicationRecord
    has_many :books
    accepts_nested_attributes_for :books
  end
```

```erb
  <%= form_for @author do |f| %>
    <%= f.text_field :name %>

    <%= f.fields_for :books do |b| %>
      <%= b.text_field :title %>
      <%= b.text_field :genre %>
    <% end %>
```

```ruby
  def create
    author = Author.new(author_params)
    author.save
  end

  private
  def author_params
    params.require(:author).permit(:name, books_attributes: [:id, :title, :genre])
  end
```

- Es importante validar los atributos anidados y asegurarse de que los modelos asociados sean creados o actualizados correctamente.

- Nested attributes puede ser útil, pero también puede complicar la lógica de tu aplicación, así que úsalo con moderación y considera otras opciones si la complejidad aumenta.

- El problema mas común con los modelos anidados es que no muestre los mensajes de error correctamente.

## Definir asociaciones polimórficas en Active Record.
El polimorfismo es un concepto de programación orientada a objetos que permite a un objeto ser tratado como si fuera de un tipo diferente. En el contexto de Rails, el polimorfismo se utiliza para crear asociaciones entre modelos que pueden pertenecer a diferentes tipos de modelos. Puedes utilizar el polimorfismo para crear relaciones "polimórficas" entre modelos. Por ejemplo, considera un modelo Comment que puede pertenecer tanto a un modelo Post como a un modelo Photo. En este caso, puedes utilizar el polimorfismo para definir la relación entre Comment y los otros modelos.
  ```bash
    rails generate migration create_comments
  ```

  ```ruby
    class CreateComments < ActiveRecord::Migration[6.0]
      def change
        create_table :comments do |t|
          t.text :content
          t.references :commentable, polymorphic: true, index: true
          t.timestamps
        end
      end
    end
  ```

  ```bash
    rails db:migrate
  ```

  ```ruby
    class Comment < ApplicationRecord
      belongs_to :commentable, polymorphic: true
    end

    class Post < ApplicationRecord
      has_many :comments, as: :commentable
    end

    class Photo < ApplicationRecord
      has_many :comments, as: :commentable
    end
  ```

El polimorfismo en Rails te permite crear relaciones flexibles entre modelos, lo que puede ser útil en situaciones donde varios modelos comparten una relación común con otro modelo. Por ejemplo, en lugar de tener una tabla de comentarios separada para cada tipo de modelo (PostComment, PhotoComment, etc.), puedes utilizar una sola tabla de comentarios que sea polimórfica y pueda asociarse con cualquier modelo.

## Herencia
La herencia es un concepto de programación orientada a objetos que permite que una clase (llamada clase derivada o subclase) herede atributos y métodos de otra clase (llamada clase base o superclase).

En Rails, puedes utilizar la herencia para compartir código entre modelos relacionados. Por ejemplo, considera los modelos Animal y Cat, donde Cat es un tipo específico de Animal con algunas características adicionales.

  ```ruby
    class Animal < ApplicationRecord
    end

    class Cat < Animal
      def meow
        "¡Miau!"
      end
    end
  ```
En este ejemplo, Cat hereda de Animal, lo que significa que Cat tiene acceso a todos los métodos y atributos de Animal y también puede definir sus propios métodos y atributos adicionales, como meow.

La herencia en Rails te permite compartir código común entre modelos relacionados, lo que puede ayudar a mantener tu código más limpio y modular.

También te permite definir comportamientos específicos para subclases sin afectar a la clase base, lo que facilita la extensión y modificación de tu código en el futuro.

Respecto a las migraciones en Rails, cuando se utiliza herencia en los modelos, no es necesario realizar ninguna modificación especial en las migraciones. La tabla asociada a la clase hija (Cat, en este caso) no necesita tener columnas adicionales para los atributos heredados de la clase padre (Animal). Rails maneja automáticamente la herencia en la base de datos, utilizando una columna especial llamada `type` para determinar el tipo de clase que representa cada registro en la tabla.

  ```ruby
    class CreateAnimals < ActiveRecord::Migration[6.0]
      def change
        create_table :animals do |t|
          t.string :name
          t.string :species
          t.string :type
          t.timestamps
        end
      end
    end
  ```

## Recursos adicionales:
  1. [Rails Guides](https://guides.rubyonrails.org/): Los Rails Guides son una excelente fuente de documentación oficial de Rails. Cubren una amplia variedad de temas, desde lo básico hasta temas avanzados, y proporcionan ejemplos prácticos.

  2. [Rails API](https://api.rubyonrails.org/): La API de Rails proporciona una referencia completa de todas las clases y métodos disponibles en Rails. Es útil para encontrar información detallada sobre clases específicas y sus métodos.

  3. [RailsCasts](http://railscasts.com/): Aunque ya no se actualiza, RailsCasts es una serie de screencasts creada por Ryan Bates que cubre una amplia gama de temas de Rails. Aunque algunos episodios pueden estar desactualizados, muchos conceptos siguen siendo relevantes.

  4. [GoRails](https://gorails.com/): GoRails es una plataforma en línea que ofrece screencasts y tutoriales sobre Ruby on Rails y tecnologías relacionadas. 

  5. [RailsApps Project](https://github.com/RailsApps/rails_apps_composer): El proyecto RailsApps proporciona una colección de aplicaciones de ejemplo y guías paso a paso para crear aplicaciones Rails completas para diversos propósitos.

  6. [Stack Overflow](https://stackoverflow.com/questions/tagged/ruby-on-rails): El sitio de preguntas y respuestas Stack Overflow es un recurso invaluable para encontrar respuestas a problemas específicos de Rails. Muchas preguntas comunes ya han sido respondidas aquí.

  7. Libros sobre Rails: Hay varios libros excelentes sobre Ruby on Rails, como "Agile Web Development with Rails" de Sam Ruby, Dave Thomas y David Heinemeier Hansson, que es una lectura recomendada para cualquier desarrollador de Rails. "Rails 5 Test Prescriptions" by Noel Rappin: Este libro se centra en las pruebas en Rails, cubriendo desde pruebas unitarias hasta pruebas de integración y pruebas funcionales. "The Rails 5 Way" by Obie Fernandez: Este libro ofrece una guía completa sobre cómo construir aplicaciones web con Rails 5, cubriendo desde los conceptos básicos hasta las técnicas avanzadas. "Rails AntiPatterns: Best Practice Ruby on Rails Refactoring" by Chad Pytel and Tammer Saleh: Este libro aborda algunos patrones comunes que pueden surgir en el desarrollo de aplicaciones Rails y ofrece soluciones efectivas para abordarlos.