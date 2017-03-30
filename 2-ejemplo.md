# Ejemplo: adivina el número

Para este primer ejemplo, vamos a reescribir un juego escrito de forma procedimental a programación orientada por objetos. El juego se trata de adivinar el número secreto, veamos la implementación procedimental:

```ruby
num_attempts = 0
number = rand(1..10)

found = false
until found
  print "Adivina el número de 1 a 10 que estoy pensando: "
  guess = gets.chomp.to_i

  if guess == number
    puts "Muy bien! Lo lograste en #{num_attemps} intentos!"
    found = true
  else
    puts "Lo siento! No es el número, intenta nuevamente."
    num_attempts += 1
  end
end
```

Este código empieza inicializando las variables que vamos a utilizar: `num_attempts` y `number`. También creamos una variable `found` que nos va a indicar si ya se encontró la respuesta.

Después iteramos mientras que `found` sea `false`. En cada iteración le pedimos al usuario que adivine el número y si es correcto termina el juego (cambiamos `found` a `true` para que termine la iteración). De lo contrario, incrementamos el número de intentos.

En este momento nuestro juego está muy **acoplado** a la línea de comandos y sería muy difícil adaptarlo a otros ambientes como una aplicación de escritorio o Web. Lo que vamos a hacer con programación orientada a objetos es separar el juego de la interfaz de usuario.

Primero vamos a crear una clase `Game` que se encargue de la lógica del juego. Crea un nuevo archivo llamado `game.rb` y escribe lo siguiente:

```ruby
class Game
  attr_reader :num_attempts, :found

  def initialize
    @number = rand(1..10)
    @num_attempts = 0
    @found = false
  end

  def attempt(guess)
    if guess == number
      @found = true
    else
      @num_attempts += 1
    end
  end
end
```

Nuestra clase `Game` va a tener 3 métodos públicos: `attempt`, `num_attempts` y `found` (los últimos dos los crea el `attr_reader`). El constructor se va a encargar de inicializar los atributos, incluyendo el número que se va a adivinar.

Ahora, si quisiéramos utilizar nuestro juego en otro ambiente lo podríamos hacer fácilmente, sería cuestión de copiar esta clase. Ahora veamos cómo lo utilizaríamos desde una interfaz de línea de comando (como lo hicimos anteriormente). Reemplaza el contenido de `guess_number.rb` con el siguiente código:

```ruby
require "./game"

game = Game.new
until game.found
  print "Adivina el número de 1 a 10 que estoy pensando: "
  guess = gets.chomp.to_i

  game.attempt(guess)
  if game.found
    puts "Muy bien! Lo lograste en #{game.num_attemps} intentos!"
  else
    puts "Lo siento! No es el número, intenta nuevamente."
  end
end
```

Veamos qué hace este código. El primer paso es importar el archivo con la clase `Game`. Luego creamos una nueva instancia de `Game` e iteramos mientras que el número no haya sido encontrado. En cada iteración utilizamos nuestra case `Game` para realizar los intentos y verificar si ya fue encontrado.

## Código más fácil de probar

Supongamos que queremos crear pruebas automatizadas para nuestra clase `Game`. Lo que queremos probar es que:

1. Al crear un nuevo juego el número de intentos es 0 y no se ha encontrado el número.
2. El número de intentos (`num_attempts`) se incremente correctamente. 
3. `found` se actualiza a `true` cuando sea adivina el número.

Crea un archivo `game_test.rb` y escribe lo siguiente:

```ruby
require "minitest/autorun"
require "./game"

class GameTest < Minitest::Test
  def setup
    @game = Game.new
  end

  def test_constructor_initializes_num_attempts_and_found
    assert_equal 0, @game.num_attempts
    assert_not @game.found
  end

  def test_increments_num_attemps_on_failed_attempts
  end

  def test_found_is_updated_when_number_is_guessed
  end
end
```

Tenemos un problema para implementar las dos últimas pruebas. Actualmente el número se genera de forma aleatoria. ¿Cómo podemos saber cuál es el número?

Vamos a hacer uso de la programación orientada a objetos para solucionar este problema. Primero, vamos a crear una clase `RandomNumberGenerator` con un único método `generate` que retorne un número aleatorio. Crea una nueva clase `random_number_generator.rb` y escribe lo siguiente:

```ruby
class RandomNumberGenerator
  def generate
    rand(1..10)
  end
end
```

Ahora, vamos a cambiar `Game` para que reciba un argumento en el constructor, que por defecto va a ser una nueva instancia de `RandomNumberGenerator`:

```ruby
require './random_number_generator.rb'

class Game
  attr_reader :num_attempts, :found
  
  # recibe un objeto que responda a generate, por defecto va a ser RandomNumberGenerator
  def initialize(number_generator = RandomNumberGenerator.new)
    @number = number_generator.generate
    @num_attempts = 0
    @found = false
  end

  ...
end
```

¿Cuál es la ventaja de este cambio? Que para las pruebas podemos crear un nueva clase que nos devuelva un número fijo para poder probar fácilmente. Al constructor de `Game` no le importa qué objeto le llegue como argumento siempre y cuando responda al método `generate`. A esto se le conoce en el mundo de la programación como **duck typing**. 

Crea un nuevo archivo llamado `fixed_number_generator.rb` con el siguiente contenido:

```ruby
class FixedNumberGenerator
  def initialize(number)
    @number = number
  end

  def generate
    @number
  end
end
```

Esta clase recibe un argumento en el constructor, el número que se va a generar. El método `generate` siempre retorna el número que le pasamos en el constructor. De esta forma vamos a saber cuál es el número que se debe adivinar.

Ahora podemos utilizar nuestro `FixedNumberGenerator` para implementar nuestras pruebas:

```ruby
require "minitest/autorun"
require "./fixed_number_generator"
require "./game"

class GameTest < Minitest::Test
  def setup
    # el número a adivinar siempre va a ser 5
    generator = FixedNumberGenerator.new(5)
    @game = Game.new(generator)
  end

  def test_constructor_initializes_num_attempts_and_found
    assert_equal 0, @game.num_attempts
    assert_not @game.found
  end

  def test_increments_num_attemps_on_failed_attempt
    @game.attempt(4)

    assert_not @game.found
    assert_equal 1, @game.attempts
  end

  def test_found_is_updated_when_number_is_guessed
    @game.attemp(5)

    assert @game.found
  end
end
```

Hemos utilizado la programación orientada a objetos para dos cosas:

1. Separar la lógica del juego de la forma en que se le muestra al usuario.
2. Hacer nuestro código más fácil de probar.

Esto, en el mundo de la programción, es muy valioso!