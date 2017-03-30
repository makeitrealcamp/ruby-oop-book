# Métodos y atributos de clase

Hasta ahora los métodos y atributos que hemos utilizado ha sido sobre los objetos, instancias de una clase. En este capítulo hablaremos sobre métodos y atributos que puedes llamar sobre la clase directamente.

En general, los métodos y atributos de instancia se utilizan mucho más que los métodos y atributos de clase. Sin embargo, es importante que conozcan para qué sirven los métodos y atributos de clase y cómo se definen.

## Métodos de clase

Un método de clase es muy parecido a un método de instancia, con la diferencia de que el nombre tiene el prefijo `self.`:

```ruby
class Person
  def self.mi_metodo_de_clase
    puts "Este es un método de clase"
  end
end
```

Para invocar este método debes hacerlo directamente sobre la clase (fíjate que el prefijo `self.` se omite):

```ruby
Person.mi_metodo_de_clase
```

Si intentas invocar el método sobre una instancia recibirás un error:

```ruby
p1 = Person.new
p1.mi_metodo_de_clase # error
```

### ¿Para qué sirven los métodos de clase?

Los métodos de clase sirven para ese código que está relacionado con la clase pero que no depende de ninguna instancia particular. El uso depende de las necesidades de cada programa y la imaginación de cada persona, pero en mi experiencia he encontrado que los métodos de clase se utilizan generalmente para:

1. Crear objetos complejos.
2. Como métodos de ayuda que se pueden utilizar desde diferentes partes, pero no están asociados a ningún objeto.

Veamos algunos ejemplos. Empecemos agregando algunos métodos a nuestra clase `Person` para ayudarnos en la creación de objetos:

```ruby
class Person
  def initialize(name, gender)
    ...
  end

  def self.create_female(name)
    Person.new(name, :female)
  end

  def self.create_male(name)
    Person.new(name, :male)
  end
end
```

Para este ejemplo hemos creados dos métodos de clase `create_female` y `create_male` que podemos utilizar de la siguiente forma:

```ruby
pedro = Person.create_male("Pedro")
maria = Person.create_female("Maria")
```

Este es un ejemplo trivial y quizá no sea una buena idea crear métodos de clase para ahorrarse pasar un argumento más al crear una persona, pero imagina que la creación de un hombre o de una mujer implique muchos otros atributos y fuera más complejo. En ese caso tendría mucho sentido.

Veamos otro ejemplo. Supongamos que necesitamos enviar correos desde diferentes partes de nuestra aplicación. Podríamos crear una clase `Mailer` que exponga algunos métodos de clase para enviar correos con algunas variaciones:

```ruby
class Mailer
  # Método genérico para enviar un correo
  def self.send(to, from, body)
    ...
  end

  # Método para enviar un correo a partir de una plantilla
  def self.send_with_template(to, from, template, attributes)
    ...
  end

  # Método para enviar un correo utilizando un from por defecto
  def self.send_with_default_sender(to, body)
    ...
  end
end
```

Ahora podemos utilizar estos métodos desde donde queramos sin necesidad de crear una instancia de `Mailer`:

```ruby
Mailer.send("pedro@example.com", "juan@example.com", "Hola amigo!")
Mailer.send_with_template("pedro@example.com", "juan@example.com", "Hola {{name}}", name: "Pedro")
Mailer.send_with_default_sender("pedro@example.com", "Hola!")
```

La última línea de este código debería enviar un correo a "pedro@example.com" desde una dirección de correo que configuraríamos por defecto. Pero ¿dónde podemos almacenar dicha dirección? De eso vamos a hablar en la siguiente sección.

## Atributos de clase

Supongamos que necesitamos contar cuántas objetos se crean a partir de la clase `Person`. Esa información la podríamos almacenar en un atributo de clase. Los atributos de clase se identifican porque utilizan dos `@@` en vez de una:

```ruby
class Person
  @@people_count = 0

  def initialize
    @@people_count += 1
  end

  def self.people_count
    @@people_count
  end
end
```

En este ejemplo estamos inicializando nuestro atributo de clase `@@people_count` en 0. Cada vez que se cree un objeto de tipo `Person` se va a invocar el constructor `initialize` que incrementa `@@people_count` en 1.

Los atributos de clase no son visibles fuera de la clase. Es por eso que creamos un método de clase `people_count` que va a retornar `@@people_count`.

Probemos esta clase a ver si funciona:

```ruby
puts Person.people_count # Imprime 0

Person.new
puts Person.people_count # Imprime 1

Person.new
puts Person.people_count # Imprime 2
```

Volvamos a nuestro ejemplo de la clase `Mailer`. Podemos utilizar un atributo de clase para almacenar la dirección de correo por defecto que se va a utilizar desde el método de clase `send_with_default_sender`:

```ruby
class Mailer
  @@default_sender = "juan@example.com"

  def self.default_sender=(sender)
    @@default_sender = sender
  end

  # acá van los métodos que definimos en la sección anterior
end
```

Agregamos un atributo de clase `@@default_sender` que contiene un valor por defecto. Sin embargo, también creamos un método de clase `default_sender=` que va a permitir cambiar el valor por defecto:

```ruby
Mailer.default_sender = "maria@example.com"
```

Muy bien. Ahora ya sabes que existen los métodos y los atributos de clase, también sabes cómo utilizarlos.

## Ejercicios



