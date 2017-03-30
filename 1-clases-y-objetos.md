# Clases y Objetos

Las clases y los objetos son los conceptos más importantes de la Programación Orientada por Objetos (POO), y están fuertemente relacionados. Los objetos se crean a partir de clases, y las clases sirven como plantillas para crear objetos. A los objetos también se les llama instancias de clase.

Utilicemos una analogía. Piensa en la palabra *persona*. Cuando hablamos de una persona no nos referimos a ninguna persona en particular, solo a la idea de la persona. Una persona tiene un nombre, una edad, una estatura, etc. Esa es la clase, la plantilla.

Ahora piensa en personas específicas: tu, tu mamá, el presidente de tu país, Alan Turing, Nelson Mandela, etc. Esos son los objetos. Para cada persona puedes llenar los datos de la plantilla: el nombre, la edad, la estatura, etc.

Veamos cómo definir una clase en Ruby. Crea un archivo llamado `person.rb` y escribe lo siguiente:

```ruby
class Person
end
```

Esa es la definición más simple de una clase. Comienza con la palabra clave `class`, seguida de el nombre que le quieras dar a la clase y termina con la palabra clave `end`.  Dentro de la clase van los métodos (comportamiento) y atributos (información) que van a tener los objetos que se creen a partir de esta clase, pero en este ejemplo aún no tenemos ninguno.

Para crear objetos a partir de una clase se utiliza el nombre de la clase seguido de la palabra clave `new`:

```ruby
Person.new
```

Esta línea retorna el objeto, que usualmente se almacena en una variable:

```ruby
p1 = Person.new
```

La variable la puedes llamar como quieras. También puedes crear varios objetos de esta clase, de hecho todos los que quieras mientras que no se llene tu memoria RAM:

```ruby
p1 = Person.new
p2 = Person.new
p3 = Person.new

muchas_personas = []
10000.each do 
  muchas_personas.push(Person.new) 
end
```

En este ejemplo estamos creando 10,003 personas: `p1`, `p2`, `p3` y las 10,000 que están dentro del arreglo almacenado en la variable `muchas_personas`. 

Podríamos crear más instancias de `Person`, de hecho podíamos crear 7 billones de instancias y, quizá, terminar simulando la realidad dentro del computador (o quizá alguien ya hizo eso y nosotros no somos más que ... una simulación!). Pero bueno, no nos desviemos del tema, necesitamos aprender varios conceptos antes de eso. (Aunque lo más interesante sería crear una simulación dentro de una simulación! Ya veremos).

## Comportamiento (métodos)

Hasta ahora nuestra clase `Person` no es muy útil. Podemos agregarle un primer comportamiento: saludar.

```ruby
class Person
  def greet
    "Hola"
  end
end
```

Los comportamientos son métodos que defines dentro de la clase, pero para llamar el método, tienes que hacerlo sobre un objeto, como en el siguiente ejemplo:

```ruby
p1 = Person.new
puts p1.greet # imprime "Hola"
puts p1.greet # imprime "Hola"

# no es obligatorio guardar la persona en una variable, puedes hacer lo siguiente:
puts Person.new.greet # imprime "Hola"
```

Para llamar un método sobre un objeto debes agregar un punto seguido del nombre del método (p.e. `p1.greet`). A estos métodos también se les conoce como **métodos de instancia**, porque la única forma de invocarlos es sobre un objeto. (Más adelante veremos métodos que se pueden llamar directamente sobre la clase, sin necesidad de crear un objeto, pero no nos adelantemos).

Los **métodos de instancia** también pueden recibir argumentos:

```ruby
class Person
  def greet(name)
    "Hola #{name}"
  end
end

p1 = Person.new
puts p1.greet("Pedro") # imprime "Hola Pedro"
puts p1.greet("Juan") # imprime "Hola Juan"
```

## Visibilidad de los métodos

También es posible crear métodos privados, es decir, métodos que solo se pueden acceder internamente por otros métodos. La forma de hacerlo es la siguiente:

```ruby
class Person
  ...

  private
    def secret_method
      puts "Este es el método secreto"
    end

    def another_secret_method
      puts "Este es otro método secreto"
    end
end
```

Para crear métodos privados debes utilizar la palabra clave `private`. **Todos los métodos que estén debajo de esa palabra serán privados** y no se podrán acceder desde afuera:

```ruby
p1 = Person.new("Pedro")
p1.secret_method # genera un error!
```

Lo mismo ocurre si intentamos acceder a `another_secret_method`.

## El constructor

Las clases pueden tener un método especial llamado `initialize` que se llama cada vez que se crea un objeto con `.new`. Por ejemplo, supongamos que queremos imprimir “creando nueva persona …” cada vez que se crea un objeto de tipo `Person`:

```ruby
class Person
	def initialize
    puts "creando nueva persona ..."
  end

  ...
end

Person.new # imprime "creando nueva persona ..."
```

A ese método `initialize` se le conoce como el *constructor*. Como buena práctica te recomiendo ubicarlo siempre encima de cualquier otro método de tu clase.

El *constructor* también puede recibir argumentos: 

```ruby
class Person
	def initialize(name)
    puts "creando nueva persona llamada #{name}"
  end
  ...
end
```

Ahora cuando crees una persona debes pasarle el argumento:

```ruby
Person.new("Pedro") # imprime "creando nueva persona llamada Pedro"
Person.new("Juan") # imprime "creando nueva persona llamada Juan"
```

En este momento no estamos almacenando el nombre de la persona en ninguna parte. Pero eso es precisamente de lo que vamos a hablar a continuación.

### Atributos (información de un objeto)

Así como un objeto tiene un comportamiento, los objetos también pueden tener información, o atributos (hablo de objeto y no de clase porque, aunque los métodos se definen en la clase, se llaman sobre los objetos).

Como decíamos antes, una persona puede tener un nombre, una edad, una estatura, etc. Esos son los atributos.

Imagina a los atributos como variables que están asociadas a un objeto. De hecho, a los atributos se les conoce como **variables de instancia** (instancia se refiere a un objeto, la instancia de una clase).

En Ruby vas a identificar los atributos en una clase porque comienzan con el carácter `@`. Por ejemplo, podemos almacenar el argumento que llega en el constructor dentro de un atributo de `Person`:

```ruby
class Person
	def initialize(name)
    @name = name
  end
  ...
end
```

Analiza este último ejemplo con cuidado. El constructor está recibiendo un **argumento** llamado `name` y almacenamos el valor en el **atributo** `@name`. `name` no es lo mismo que `@name`.

La ventaja de guardar el nombre en `@name`, es que hora lo podemos utilizar desde cualquier otro método:

```ruby
class Person
	def initialize(name)
    @name = name
  end
  
  def greet(other_person_name)
    "Hola #{other_person_name}, me llamo #{@name}"
  end
end
```

Fíjate cómo estamos utilizando `@name` dentro del método `greet`. Creemos una instancia (objeto) de `Person` para probarlo:

```ruby
pedro = Person.new("Pedro")
puts pedro.greet("Juan") # imprime "Hola Juan, me llamo Pedro"
```

### Visibilidad de los atributos

El atributo `@name` solo puede ser leído y modificado dentro de instancias de `Person`. Si queremos exponerlo al mundo exterior tenemos que crear métodos para leerlo y modificarlo:

```ruby
class Person
	def initialize(name)
    @name = name
  end
  
  def greet(other_person_name)
    "Hola #{other_person_name}, me llamo #{@name}"
  end

  # Método para que @name pueda ser leído desde afuera
  def name
    @name
  end

  # Método para que @name pueda ser modificado desde afuera
  def name=(name)
    @name = name
  end
end
```

En este ejemplo hemos creado dos métodos: `name` y `name=` (más abajo entenderás por qué lo llame así).  El método `name` retorna el valor de `@name` mientras que `name=` recibe un argumento y modifica `@name`.

Ahora podemos utilizar esos métodos para leer y modificar el nombre desde afuera del objeto. Por ejemplo:

```ruby
p1 = Person.new("Pedro")
puts p1.name # Imprime "Pedro"

# Supongamos que Pedro se cambio el nombre a Mary
p1.name=(“Mary”)
puts p1.name # Imprime "Mary"
```

Fíjate en la línea `p1.name=(“Mary”)`. El nombre de ese método es especial porque nos permite hacer lo siguiente:

```ruby
p1.name = "Mary" # Equivalente a p1.name=(“Mary”)
```

A esto se le conoce como *operator overloading* en Ruby por si quieres investigar más en Internet.

Supongamos ahora que le vamos a agregar los atributos `age` y `gender` a `Person`. Sería muy engorroso tener que crear el método de lectura y de escritura para cada uno de los atributos.

Ruby nos ofrece unos atajos para no tener que escribir tanto código: `attr_accessor`, `attr_reader` y `attr_writer`.

Estos atajos se utilizan de la siguiente forma. Supongamos que tenemos los atributos `name`, `age` y `gender`. Podemos crear los métodos para leer y escribir estos atributos así:

```ruby
class Person
  attr_accessor :name, :age, :gender

  def initialize(name, age, gender)
    @name = name
    @age = age
    @gender = gender
  end
end
```

La línea `attr_accessor :name, :age, :gender` crea los métodos para leer y escribir los atributos por nosotros (utilizando una técnica que se llama metaprogramming en Ruby). Ya no es necesario crear los métodos `name`, `name=`, `age`, `age=`, `gender` y `gender=`manualmente.

Pero supongamos que el atributo `age` no debería ser posible modificarlo directamente, podemos utilizar `attr_reader` para que Ruby solo cree el método `age` y no `age=`:

```ruby
class Person
  attr_accessor :name, :gender
  attr_reader :age # No crea el método age=

  def initialize(name, initial_age, gender)
    @name = name
    @age = initial_age
    @gender = gender
  end

  # Nuevo método
  def grow
    @age = @age + 1
  end
end
```

Hice las siguientes modificaciones a nuestro `Person`:

1. Ahora el atributo `age` es de solo lectura.
1. Cambié el nombre del argumento en el *constructor* de `age` a `initial_age` para que sea más claro.
2. Creé un método `grow` que incrementa la edad en 1.

Ahora ya no es posible modificar el atributo `age` directamente desde afuera del objeto:

```ruby
p1 = Person.new("Pedro", 30, :male)
puts p1.age # Imprime 30
p1.age = 40 # Genera error

p1.grow
puts p1.age # Imprime 31
p1.grow
p1.grow
puts p1.age # Imprime 33
```

Ahora supongamos que nos piden que el atributo `gender` sea solo de escritura, no de lectura, porque, no sé, estaba ocasionando discriminación de genero. Podemos utilizar `attr_writer` para que Ruby solo cree el método de escritura `gender=` y no el de lectura `gender`:

```ruby
class Person
  attr_accessor :name
  attr_reader :age # no crea el método age=
  attr_writer :gender # no crea el método gender

  def initialize(name, initial_age, gender)
    @name = name
    @age = initial_age
    @gender = gender
  end
  ...
end
```

Ahora no va a ser posible leer el atributo `gender` desde fuera del objeto:

```ruby
p1 = Person.new("Pedro", 30, male)
puts p1.gender # genera error
```

Se empieza a volver más denso cada vez ¿no? Es fácil perderse en los detalles, pero no olvides lo siguiente:

* Una clase es una plantilla para crear objetos.
* Un objeto es una instancia de una clase.
* La clase puede tener un método `initialize` llamado **constructor** que se va a llamar cada vez que se cree un objeto de esa clase.
* El **constructor** se utiliza generalmente para inicializar los atributos de los objetos.
* En la clase puedes definir métodos (el comportamiento) y atributos (información) que va a tener los objetos. A los atributos se les llama **variables de instancia** y a los métodos se les llama **métodos de instancia**
* Los atributos son información asociada a cada objeto.
* Los atributos son privados y solo pueden ser leídos y modificados dentro del objeto, a menos de que crees métodos para leerlos y modificarlos.
* Puedes utilizar los atajos `attr_accessor`, `attr_reader` y `attr_writer` para que Ruby cree los métodos para leer y modificar los atributos.

## En Ruby (casi) todo es un objeto

Si has trabajado con Ruby seguramente ya has trabajado con clases, objetos y métodos. Por ejemplo, cuando haces algo como `"Hola".length` estás llamando el método `length` de una clase llamada `String`. De hecho podemos ser más explicitos y crear la cadena de la siguiente forma:

```shell
$ irb
> s = String.new("Hola")
 => "Hola"
> s.length
 => 4 
```

Cuando creas una cadena con comillas, Ruby lo traduce a `String.new("...")`. Lo mismo ocurre con los arreglos:

```shell
$ irb
> a = Array.new
 => []
> a.push("Hola")
 => ["Hola"]
> a.push("Mundo")
 => ["Hola", "Mundo"]
> a.reverse
 => ["Mundo", "Hola"]
```

Cuando creas un arreglo con corchetes cuadrados, Rubylo traduce a `Array.new`. También los hashes:

```shell
> h = Hash.new
 => {}
> h[:one] = "uno"
 => "uno"
> h
 => {:one=>"uno"} 
```

Puedes consular la documentación de las clases <a href="https://ruby-doc.org/core-2.3.3/String.html" target="_blank">String</a>, <a href="https://ruby-doc.org/core-2.3.3/Array.html" target="_blank">Array</a> y <a href="https://ruby-doc.org/core-2.3.3/Hash.html" target="_blank">Hash</a>.

Los números también son objetos en Ruby y puedes llamar métodos sobre ellos:

```shell
$ irb
> 2.even?
 => true
```

De hecho, cuando sumas dos números en Ruby (p.e. `1 + 2`) realmente estás llamando el método `+` sobre el primer número y le estás pasando el segundo número como argumento!

```shell
$ irb
> 1+(2)
 => 3
```

Para ver todos los métodos que tienen los números puedes consultar la documentación de <a href="https://ruby-doc.org/core-2.3.3/Fixnum.html" target="_blank">Fixnum</a>, la clase que representa números en Ruby (fíjate en todos los métodos que parecen operadores como `+`, `=`, etc.).

## Conociendo la clase de un objeto

Todos los objetos tienen un método especial llamado `class` que retorna la clase que se utilizó para crearlos:

```shell
$ irb
> 2.class
 => Fixnum
> "Hola".class
 => "String"
```

## self

Por último, es muy probable que veas la palabra clave `self` en las clases. Veamos para qué sirve con un ejemplo:

```ruby
class Person
  def initialize
    random_number = self.random_number
  end

  def random_number
    # generates random number
  end
end
```

Con `self` podemos ser más explícitos para referirnos a un método del objeto. En este caso, estamos almacenando el resultado del **método** `random_number` en una **variable** `random_number`. Como tienen el mismo nombre utilizamos `self` para indicarle a Ruby que nos estamos refiriendo al **método** y no a la **variable**.

`self` se utiliza generalmente cuando hay colisiones de nombres entre un método y una variable.

Suficiente información por este capítulo. En el siguiente capítulo pondremos en práctica todos estos conocimientos.

## Ejercicios

1. Crea una clase llamada `Square`.

2. Agrega un constructor a `Square` que reciba un argumento `length` y asígnalo a un atributo con el mismo nombre.

3. Agrega métodos para leer y modificar el atributo `length`.

4. Agrega un método llamado `area` que retorne el área del cuadrado.

5. Agrega un método llamado `perimeter` que retorne el perímetro del cuadrado.

6. Crea 2 instancias de `Square`, una con longitud de 5 y la otra con longitud de 10. Verifica que retornen el área y la longitud correcta.

## Avanzado

Crea una clase `Board` que reciba una longitud y un ancho (imagina un tablero de ajedrez). Crea una clase `Player` que reciba un nombre. Crea una clase `Monster` que recibe un color 

Hacer otro ejercicio que sea más del mundo real: Facturas, productos, items, etc.