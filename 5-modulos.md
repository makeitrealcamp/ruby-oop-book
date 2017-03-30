# Módulos

Los módulos en Ruby cumplen una doble función: evitan colisiones de nombres y nos ayudan a reutilizar código.

## ¿Qué es un módulo?

Un módulo es un contenedor de clases, métodos y constantes. Los módulos se definen utilizando la palabra clave `module`. Creemos un módulo `MyModule`:

```ruby
module MyModule
end
```

Esta es la definición más simple de un módulo. Empezamos con la palabra clave `module`, seguido de un espacio, seguido del nombre del módulo `MyModule`. Para cerrar el módulo utilizamos la palabra clave `end`.

Dentro del módulo podemos definir clases, métodos y constantes. Por ejemplo:

```ruby
module MyModule
  MAX_CONNECTIONS = 5

  def method_one
  end

  def method_two
  end

  class ThingOne
  end

  class ThingTwo
  end
end
```

En el módulo `MyModule` hemos definido 5 cosas: una constante `MAX_CONNECTIONS`, los métodos `method_one` y `method_two`, y las clases `ThingOne` y `ThingTwo`. 

¿Cómo accedemos a las clases, métodos y constantes que están definidos dentro de un módulo? 

Para acceder a las clases y constantes escribes el nombre del módulo, seguido de `::` (doble dos puntos), seguido del nombre de la clase o la constante. Por ejemplo:

```ruby
puts MyModule::MAX_CONNECTIONS # Imprime 5

MyModule::ThingOne.new # Crea una instancia de la clase
```

A los métodos de no podemos acceder directamente, tenemos que **mezclar** el módulo dentro de una clase.

## Mezclando el módulo

Los módulos se pueden incluir dentro de una clase utilizando la palabra clave `include`:

```ruby
class Person
  include MyModule
end
```

Esto va a incluir todos los métodos de `MyModule` dentro de `Person`, así que podemos llamar los métodos `method_one` y `method_two` (que definimos dentro del módulo `MyModule` en la sección anterior) sobre instancias de `Person`:

```ruby
p1 = Person.new
p1.method_one
p1.method_two
```

Otra ventaja de incluir (o mezclar) un módulo dentro de una clase es que podemos acceder a las clases y las constantes que están definidas dentro del módulo directamente:

```ruby
class Person
  include MyModule

  def initialize
    puts MAX_CONNECTIONS # no hay necesidad de agregar el prefijo MyModule::
    ThingOne.new # no hay necesidad de agregar el prefijo MyModule::
  end
end
```

En este ejemplo hemos mezclado el módulo `MyModule` en `Person` y eso nos permite acceder directamente a la constante `MAX_CONNECTIONS` y `ThingOne` dentro de `MyModule`.

Es posible mezclar más de un módulo en una clase. Por ejemplo, suponiendo que existen los módulos `Module1` y `Module2`, podemos hacer lo siguiente:

```ruby
class Person
  include Module1
  include Module2
end
```

En este ejemplo estamos incluyendo dos módulos dentro de `Person`. Tanto los métodos de `Module1` como los métodos de `Module2` estarán disponibles en las instancias de la clase `Person`.

## Anidando módulos

Es posible anidar módulos dentro de módulos. Por ejemplo:

```ruby
module System
  module Currency
    class Dollar
    end
  end
end
```

En este ejemplo estamos anidando el módulo `Currency` dentro de `System`. Dentro del módulo `Currency` tenemos una clase llamada `Dollar`. 

¿Cómo accedemos a la clase `Dollar`? De la siguiente forma:

```ruby
System::Currency::Dollar.new
```

En este caso estamos creando una instancia de la clase `Dollar`, que está dentro del módulo `Currency`, que está dentro del módulo `System`. Utilizamos `::` (doble dos puntos) para separar `System` de `Currency` y `Currency` de `Dollar`.

## ¿Para qué sirven los módulos?

Ahora ya sabes cómo definir un módulo, cómo acceder a las clases y constantes, y cómo mezclarlo dentro de una clase. Pero ¿para qué se utilizan los módulos?

Por un lado, los módulos nos ayudan a evitar colisiones de nombre, es decir, evitar que dos clases o constantes se llamen igual. Por otro lado, los módulos nos van a ayudar a reutilizar código.

### Los módulos evitan colisiones de nombres

Supongamos que tenemos una clase Account que se refiere a una cuenta bancaria:

```ruby
class Account
  ...
end
```

Pero ahora estamos haciendo nuestro sistema de administración y nos damos cuenta que tenemos otra clase `Account` que colisionaría con nuestra `Account` ya existente. Podemos utilizar un módulo:

```ruby
class Account
  ...
end

module Admin
  class Account
    ...
  end
end
```

De esa forma ya no tenemos colisión de nombres. Una clase se llama `Account` y la otra `Admin::Account`.

### ¿Qué pasa si hay una colisión de nombres?

En Ruby, si dos módulos se llaman igual, su contenido se mezcla en uno solo (lo mismo pasa con las clases). Esto nos permite extender y sobrescribir funcionalidad de librerías (gemas) o hasta del mismo lenguaje Ruby en nuestras aplicaciones. A esto se le conoce como **monkey patching**, y en general no se considera una buena práctica.

Por ejemplo, supongamos que queremos incluir un método `palindrome?` dentro de la clase `String` de Ruby que devuelva si una cadena es un palíndrome (un palíndrome es una palabra o frase que se lee igual hacia adelante que hacia atrás):

```ruby
class String
  def palindrome?
    letters = self.downcase.scan(/\w/)
    letters == letters.reverse
  end
end

"anita lava la tina".palindrome? # => true
"hora de irse".palindrome? # => false
```

La implementación del método `palindrome?` no es importante en este momento. Lo interesante es que ahora podemos llamar el método `palindrome?` sobre cualquier cadena de texto!

### Los módulos nos ayudan a reutilizar código

Los módulos nos permiten agrupar métodos que después podemos incluir (o mezclar) en otras clases. Por ejemplo, supongamos que tenemos un módulo llamado `Persistable` que nos permite guardar o cargar cualquier objeto desde un archivo:

```ruby
module Persistable
  def load
    # código para cargar un objeto de un archivo
  end

  def save
    # código para almacenar un objeto en un archivo
  end
end
```

Ahora podemos mezclar este módulo en cualquier objeto que necesite ser persistido a un archivo:

```ruby
class Document
  include Persistable
end

class Image
  include Persistable
end
```

En vez de repetir los métodos `load` y `save` en `Document` e `Image`, lo definimos en el módulo `Persistable` y lo mezclamos en las clases que necesiten estos métodos.

## ¿Incluir un módulo o extender una clase?

En el capítulo pasado vimos que la herencia también es una forma de reutilizar código. En la herencia una clase **hija** recibe todos los métodos de una clase **padre**.

Los módulos también nos permiten reutilizar código, la diferencia es que en vez de extender una clase, la mezclamos con `include`.

¿Cómo decidir entre herencia y módulo? En general yo intento evitar la herencia. La razón es que una clase solo puede tener un padre y esto nos va a limitar a la hora de reutilizar código.

La herencia la utilizo únicamente cuando una clase está especializando otra. Por ejemplo, un circulo **es** una figura pero más especializada, más específica. En ese caso utilizaría herencia. Un bus **es** una especie de vehículo. En estos casos cuando una clase especializa a otra utilizo herencia.

Por otro lado, cuando necesito reutilizar un grupo de métodos en varias clases prefiero módulos sobre herencia. Generalmente, trato de agrupar los módulos en **características** o **roles** que le puedo aplicar a diferentes clases.

```ruby
class Bus < Vehicle
  include Persistable
  include Trackable
end
```

En este ejemplo decidí que `Bus` extendiera `Vehicle` porque un bus **es** un vehículo. En cambio, que el bus se pueda persistir (p.e. en la base de datos) es una característica que le puedo aplicar a cualquier clase. Lo mismo que se le pueda hacer seguimiento (p.e. por GPS).

## Resumen

## Ejercicios





