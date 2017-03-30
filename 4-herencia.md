# Herencia

La herencia es una forma de reutilizar código en la programación orientada a objetos. A través de herencia, una clase puede **recibir** los métodos de otra clase. 

Veamos un ejemplo para aclarar este concepto. Supongamos que estamos creando una aplicación de dibujo. En la mayoría de aplicaciones de dibujo puedes agregar formas: círculos, cuadrados, etc:

```ruby
class Circle
  attr_accessor: :stroke, :fill, :radius
  ...
end

class Square
  attr_accessor :stroke, :fill, :side
  ...
end

class Triangle
  attr_accessor :stroke, :fill, :base, :height
end
```

Todas las clases están repitiendo los atributos `stroke` y `fill` que corresponden a la línea y el relleno de la figura. ¿Cómo podemos hacer para no repetir los mismos atributos en todas las figuras?

Acá es donde entra la **herencia**. Podemos crear una clase **padre** llamada `Figure` que va a contener todos los métodos que todas las figuras tienen en común. La sintaxis para indicar que una clase **hereda** (también vas a escuchar **extender**) de otra clase es la siguiente: a la definición de la clase **hija** le agregas el operador `<` seguido del nombre de la clase **padre**. Por ejemplo:

```ruby
# Clase Padre
class Figure
  attr_accessor: :stroke, :fill
end

# Hereda de Figure
class Circle < Figure
  attr_accessor :radius
end

# Hereda de Figure
class Square < Figure
  attr_accessor :side
  ...
end

# Hereda de Figure
class Triangle < Figure
  attr_accessor :base, :height
end
```

En este caso `Circle`, `Square` y `Triangle` heredan de `Figure`, es decir, que tienen todos los métodos se definan en `Figure` (en este caso los métodos para leer y escribir `stroke` y `fill`). Por ejemplo:

```ruby
c1 = Circle.new
c1.fill = "red"
puts c1.fill
```

Aunque `Circle` no tiene los métodos para leer y escribir `fill`, este código funciona porque `Circle` extiende (o hereda) de la clase `Figure`, que sí tiene esos métodos.

**Nota:** Una clase padre puede tener muchas clases hijas, pero una clase hija solo puede tener un padre.

La ventaja de utilizar herencia es que si necesitas que agregar más métodos a la clase **padre**, todas las clases **hijas** reciben ese método automáticamente. Por ejemplo, supongamos que agregamos otros atributos a `Figure`:

```ruby
class Figure
  attr_accessor :stroke, :fill, :x, :y, :shadow
end
```

Ahora todas las clases que extiendan de `Figure` también tendrán los métodos para leer y escribir los atributos `x`, `y`, `shadow`.

## Jerarquía de clases

Una clase puede ser padre de muchas otras clases, e hija de otra al mismo tiempo. 

Siguiendo con nuestra aplicación de dibujo, supongamos que queremos implementar la figura cilindro. Un cilindro es como un círculo, pero también tiene una longitud, así que nuestra jerarquía de clases sería la siguiente:

```ruby
class Figure
  ...
end

class Circle < Figure
  attr_accessor :radius
end

class Cylinder < Circle
  attr_accessor :length
end
```

`Circle` es hija de `Figure` y padre de `Cylinder`. A medida que se van creando más clases se va creando una jerarquía de clases.

### Object

En Ruby la clase `Object` es la clase padre de todas las clases que no hereden de alguna otra. `Object` define métodos como `class`, `to_s`, `methods`, `respond_to?`, `nil?`, `is_a?`, entre otros. Puedes ver la lista completa en la documentación de la clase [Object](https://ruby-doc.org/core-2.3.3/Object.html). Todos estos métodos están disponibles en cualquier clase de Ruby.

Por ejemplo, a pesar de que `Figure` en nuestro ejemplo anterior parece no heredar de ninguna clase, realmente está heredando de `Object`, así que puedes llamar cualquier método que esté definido en `Object`:

```ruby
f1 = Figure.new
puts f1.class # Imprime Figure
puts f1.to_s # Imprime algo como #<Figure:0x007f98fc1b8810>
puts f1.nil? # Imprime false
```

**Nota:** `Object` extiende de otra clase llamada `BasicObject`, que es la raíz de toda la jerarquía de clases en Ruby, pero esa es una clase vacía, así que en la práctica puedes pensar en `Object` como la clase raíz.

Los que hemos vistos hasta acá son los conceptos básicos de la herencia. Sin embargo, hay varios detalles que surgen, por ejemplo:

* ¿El constructor de la clase padre se llama cuando creo una instancia de la clase hija?
* ¿Qué pasa si la clase hija tiene un método que se llama igual que uno de la clase padre?
* ¿Cómo puedo llamar métodos de la clase padre?

Empecemos a responder cada una de estas preguntas:

## Herencia y el constructor

Considera el siguiente código:

```ruby
class Parent
  def initialize
    puts "Este es el constructor de Parent"
  end
end

class Child
end
```

¿Qué pasa si ejecutamos `Child.new`, se imprimirá "Este es el constructor de Parent"?

La respuesta es **no**. Los constructores son independientes. Si quieres invocar el constructor del padre debes hacerlo explícitamente utilizando la palabra clave `super`:

```ruby
class Parent
  def initialize
    puts "Este es el constructor de Parent"
  end
end

class Child
  def initialize
    super # con esta línea ejecutamos el constructor del padre
  end
end
```

La palabra `super` debe ser la primera línea dentro de `initialize`. Si aparece después de cualquier otra línea se genera un error:

```ruby
class Child
  def initialize
    puts "Este es el constructor de Child"
    super # error!!!!!! super debe ser la primera línea
  end
end
```

Volvamos a nuestro ejemplo de la aplicación de dibujo para ver cómo funcionaría esto:

```ruby
class Figure
  attr_accessor: :stroke, :fill

  def initialize(stroke, fill)
    @stroke = stroke
    @fill = fill
  end
end

class Circle < Figure
  attr_accessor :radius

  def initialize(stroke, fill, radius)
    super(stroke, fill)
    @radius = radius
  end
end
```

La clase `Figure` define un constructor que recibe dos argumentos: `stroke` y `fill`, que inicializa `@stroke` y `@fill`. 

`Circle` define un constructor que recibe tres argumentos: `stroke`, `fill` y `radius`. Utiliza `super` para llamar el constructor de `Figure` e inicializa `@radius`.

El llamado a `super` es equivalente a copiar el código que existe en la clase padre y reemplazarlo por el `super`:

```ruby
class Circle < Figure
  attr_accessor :radius

  def initialize(stroke, fill, radius)
    # super(stroke, fill) es equivalente a
    @stroke = stroke
    @fill = fill

    @radius = radius
  end
end
```

## Sobrescribiendo métodos

Es posible sobreescribir los métodos de la clase padre definiendo un método que se llame igual en la clase hija. Por ejemplo, es muy común sobrescribir el método `to_s` que está definido en `Object`:

```ruby
class Circle < Figure
  ...

  def to_s
    "Este es un círculo con radio #{@radius}"
  end
end
```

**Nota:** No es necesario que el método esté definido directamente en la clase padre, puede estar más arriba en la jerarquía como en este ejemplo.

Ahora, cuando llamemos el método `to_s` sobre cualquier instancia de `Circle` veremos la frase "Este es un círculo con radio x":

```ruby
c1 = Circle.new(10, true, 5)
puts c1.to_s # Imprime "Este es un círculo con radio 5"
```

Puedes utilizar la palabra clave `super` para invocar el método en la clase padre. Imagina que estamos haciendo una aplicación para calcular el salario de los empleados. A todos los empleados de la compañía se les calcula el salario de la misma forma, exceptuando a los directores que reciben un bono adicional.

```ruby
class Employee
  def calculate_salary
    # código complejo para calular el salario
  end
end

class Manager < Employee
  def calculate_salary
    base_salary = super
    base_salary + @bonus
  end
end
```

En este ejemplo `Manager` sobrescribe el método `calculate_salary` de `Employee` y utiliza `super` para invocar `calculate_salary` de `Employee`. El resultado lo almacena en una variable llamada `base_salary` que después suma al bono. De esa forma no es necesario copiar todo el código que ya teníamos en `calculate_salary` de `Employee`, que podría ser bastante!

## ¿Cuándo utilizar herencia?



## Resumen

La herencia es una forma de reutilizar código. Para indicar que una clase hereda de otra se utiliza el operador `<` en la definición de la clase seguido del nombre de la clase que se quiere extender (heredar). Por ejemplo:

```ruby
class Circle < Figure
end
```

Cuando una clase extiende (hereda) de otra, la clase **hija** recibe todos los métodos de la clase **padre**, incluyendo aquellos que la clase padre haya heredado de otras clases.

En Ruby una clase solo puede extender de una sola clase (solo puede tener un padre). Sin embargo, una clase puede tener muchas hijas.

Todos los objetos que no extiendan de otra clase, extienden de `Object` implícitamente.

El constructor no se hereda. Sin embargo, se puede utilizar la palabra `super` para invocar el constructor de la clase padre.

Es posible **sobrescribir** métodos de la clase padre creando un método con el mismo nombre en la clase hija. El método en la clase hija puede utiliza la palabra `super` para invocar el método de la clase padre.

La herencia 

## Ejercicios

