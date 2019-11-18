# Ride 
Un lenguaje para crear "Contratos inteligentes" en la plataforma Waves

## Introducción

Ride es el lenguaje de programación diseñado específicamente para crear contratos inteligentes en la plataforma Waves. Fue creado para abordar las deficiencias más graves de otros lenguajes de contratos inteligentes. La idea general es ofrecer un lenguaje funcional que sea facil de aprender y que sirva para el desarrollo de dApps en la cadena de bloques Waves.

Ride es fácil de aprender, especialmente para desarrolladores principiantes. Aquí ofrecemos una introducción completa a Ride, junto con ejemplos y otras herramientas y recursos.

## Resumen

Ride es un lenguaje de programación funcional, flojo, de tipo estático y compilado basado en expresiones. Está diseñado para construir aplicaciones descentralizadas amigables para el desarrollador (dApps).

Ride no es Turing Complete y su motor de ejecución (máquina virtual) no tiene ningún concepto de bucles o posibilidad de recurrencias. Además, existen varias limitaciones por diseño, las que ayudan a garantizar que la ejecución sea segura y directa. Sin embargo, reconocemos que las iteraciones son necesarias y las hemos implementado como macros FOLD (ver más abajo). Una de las características clave es que el costo de ejecución siempre es predecible y conocido de antemano, por lo que todo el código se ejecuta según lo previsto sin registrar transacciones fallidas en la cadena y eliminando una fuente importante de frustración.

A pesar de ser fácil de usar, Ride es potente y ofrece una amplia funcionalidad a los desarrolladores. Se basa ampliamente en Scala y también está influenciado por F# y el paradigma funcional.

Ride es simple y conciso. Le tomará alrededor de una hora leer esta descripción, después de lo cual sabrá todo sobre Ride y las oportunidades que brinda para el desarrollo de dApps.

## Descargo de responsabilidad
Ride Standard Library (STDLIB) está en constante desarrollo. Al momento de esta publicación, la versión más actualizada es STDLIB_VERSION 3, con STDLIB_VERSION 4 en camino. Aquí se cubren la mayoría de las características a ser desarrolladas tambien. Las cuales no forman parte de STDLIB_VERSION 3 se encuentran marcados con un (*).

## “Hello world!”
Comencemos con un ejemplo familiar:

```scala
func say() = {
  "Hello world!"
}
```

Las funciones en Ride se declaran con `func` (ver más abajo). Las funciones tienen tipos de retorno, el compilador deduce esto automáticamente, por lo que no tiene que declararlos. En el caso anterior, la función `say` devuelve la string `Hello World!`. No hay una declaración `return` en el lenguaje porque Ride está basado en expresiones (todo es una expresión), y la última declaración es el resultado de la función.

## Blockchain

Ride fue creado específicamente para su ejecución dentro de un entorno Blockchain y está optimizado para este propósito. Debido a que la blockchain es una cadena decentralizada compartida, ubicada en muchas computadoras en todo el mundo, Ride funciona de manera un poco diferente a los lenguajes de programación convencionales.

Dado que Ride está diseñado para usarse dentro de la cadena de bloques, no hay forma de acceder al sistema de archivos o mostrar nada en la consola. En cambio, las funciones Ride pueden leer datos de la blockchain y devolver acciones como resultado, que luego se pueden aplicar a la blockchain.

## Comments

Puede agregar comentarios a su código tal como se puede en otros lenguajes, como Python:

```scala
# Esta es una linea de comentario

# Y no existen comentarios multilineas

"Hello world!" # Puedes escribir comentarios así también
```

## Directivas

Cada script de Ride debe comenzar con directivas para el compilador. En el momento de la publicación, hay tres tipos de directivas, con diferentes valores posibles.

```scala
{-# STDLIB_VERSION 3 #-}
{-# CONTENT_TYPE DAPP #-}
{-# SCRIPT_TYPE ACCOUNT #-}
```

`STDLIB_VERSION` establece la versión de la biblioteca estándar. La última versión actualmente en producción es la número 3.

`CONTENT_TYPE` establece el tipo de archivo en el que está trabajando. Hay diferentes tipos de contenido, `DAPP` y `EXPRESSION`. El tipo `DAPP` le permite definir funciones y finalizar la ejecución con ciertas transacciones (cambios en la cadena de bloques), así como también usar anotaciones. El tipo `EXPRESSION` siempre debe devolver un valor booleano, ya que se utiliza como un predicado para la validación de transacciones.

`SCRIPT_TYPE` establece el tipo de entidad que queremos agregar al script para cambiar su comportamiento predeterminado. Los scripts de Ride se pueden adjuntar a una `ACCOUNT` o a un `ASSET`.

No todas las combinaciones de directivas son correctas. El siguiente ejemplo no funcionará, porque el tipo de contenido `DAPP` está permitido solo para cuentas, mientras que el tipo `EXPRESSION` está permitido para activos y cuentas.

```scala
{-# STDLIB_VERSION 3 #-}
{-# CONTENT_TYPE DAPP #-}
{-# SCRIPT_TYPE ASSET #-} # El tipo de contenido de dApp no está permitido para un activo
```

## Variables

Las variables se declaran e inicializan con la palabra clave `let`. Esta es la única forma de declarar variables en Ride.


```scala
let a = "Bob"
let b = 1
```

Todas las variables en Ride son inmutables. Esto significa que no puede cambiar el valor de una variable después de la declaración.

Ride está fuertemente tipado y el tipo de la variable se infiere del valor en el lado derecho.

Ride le permite definir variables globalmente, dentro de cualquier función o incluso dentro de una definición de variable.

```scala
func lazyIsGood() = {
  let a = "Bob"
  let b = {
     let x = 1
     “Alice”
    }  
  true
}
```

La función anterior compilará y devolverá verdadero como resultado, pero la variable `a` no se inicializará porque Ride es un lenguaje flojo, lo que significa que las variables no utilizadas no serán calculadas.

## Funciones

Las funciones en Ride solo se pueden usar después de declararlas.

```scala
func greet(name: String) = {
  "Hello, " + name
}

func add(a: Int, b: Int) = {
  func m(a:Int) = a
  m(a) + b
}
```
El tipo (`Int`, `String`, etc) viene después del nombre del argumento.

Como en muchos otros idiomas, las funciones no deben sobrecargarse. Ayuda a mantener el código simple, legible y mantenible.

```scala
func calc() = {
  42
}

func do() = { 
  let a = calc()
  true
}
```
La función `callable` tampoco se llamará porque la variable `a` no se utiliza.

A diferencia de la mayoría de lenguajes, el sombreado de variables no está permitido. La declaración de una variable con un nombre que ya se utiliza en un ámbito primario dará como resultado un error de compilación.

Las funciones deben definirse antes de ser utilizadas.

Las funciones se pueden invocar en orden de prefijo y sufijo:

```scala
let list = [1, 2, 3]
let a1 = list.size()
let a2 = size(list)

let b1 = getInteger(this, “key”)
let b2 = this.getInteger(“key”)
```
En estos ejemplos, `a1` es igual a `a2` y `b1` es igual a `b2`.



## Tipos basicos

Los principales tipos y ejemplos básicos se enumeran a continuación:
```scala
Boolean    #   true
String     #   "Hey"
Int        #   1610
ByteVector #   base58'...', base64'...', base16'...', fromBase58String("...") etc.
```
Exploraremos cadenas y tipos especiales más abajo.

### Strings

```scala
let name = "Bob"   # use "double" quotes only
let coolName = name + " is cool!" # string concatenation by + sign

name.indexOf("o")  # 1
```

Al igual que otras estructuras de datos en Ride, las strings son inmutables. Los datos de strings se codifican con UTF-8.

Solo se pueden usar comillas dobles para denotar strings. Las strings son inmutables, como todos los demás tipos. Esto significa que la función `substring` es muy eficiente: no se realiza ninguna copia y no se requieren asignaciones adicionales.

Todos los operadores en Ride deben tener valores del mismo tipo en ambos lados. El siguiente código no se compilará porque `age` es un `int`:

```scala
let age = 21
"Bob is " + age # no compilara
```

Para que funcione tenemos que convertir `age` a `string`:

```scala
let age = 21
"Alice is " + age.toString() # funciona!
```

## Tipos especiales

Ride tiene pocos tipos principales, que operan tanto como lo hacen en Scala.

### Unidades

No hay `null` en Ride, como es el caso en muchos otros lenguajes. Por lo general, las funciones integradas devuelven el valor de la unidad de tipo `unit` en lugar de `null`.

```scala
"String".indexOf("substring") == unit # true
```

### Nothing

Nothing es el "tipo inferior" del sistema de tipos de Ride. Ningún valor puede ser de tipo Nothing, pero una expresión de tipo Nothing puede usarse en todas partes. En lenguajes funcionales, esto es esencial para el soporte al momento de lanzar una excepción:


```scala
2 + throw() # the expression compiles because
 	    # there's a defined function +(Int, Int).
 	    # The type of the second operand is Nothing, 
 	    # which complies to any required type.
```

### Listas

```scala
let list = [16, 10, 1997, "birthday"]       # puede contener diferentes tipos de datos

let second = list[1]                        # 10 - lee el segundo valor de la lista

```

`List` no tiene ningún campo, pero hay funciones en la biblioteca estándar que facilitan el trabajo con campos.

```scala
let list = [16, 10, 1997, "birthday"]

let last = list[(list.size() - 1)] # "birthday", llamada del sufijo de la función size() 

let lastAgain = getElement(collection, size(collection) - 1) # Lo mismo que arriba
```

La función `.size()` devuelve la longitud de una lista. Tenga en cuenta que es un valor de solo lectura y que el usuario no puede modificarlo. (Tenga en cuenta también que `last` podría ser de más de un tipo, pero esto solo se infiere cuando se establece la variable).

```scala
let initList = [16, 10]                   # init value
let newList = cons(1997, initList)        # [1997, 16, 10]
let newList2 = 1997 :: initList           # [1997, 16, 10]
let newList2 = initList :+ 1              # [16, 10, 1](* Disponible en STDLIB_VERSION 4)
let newList2 = [4, 8, 15, 16] ++ [23, 42]     # [4 8 15 16 23 42](*)
```

- Para anteponer un elemento en una lista ya existente, use la función `cons` u el operador ::
- Para agregar un elemento, use el operador :+ (*)
- Para concatenar 2 listas, use el operador ++ (*)


### Tipos de unión y correspondencia de tipos

```scala
let valueFromBlockchain = getString("3PHHD7dsVqBFnZfUuDPLwbayJiQudQJ9Ngf", "someKey") # Union(String | Unit)
```
Los tipos de unión son una forma muy conveniente de trabajar con abstracciones. `Union (String | Unit)` muestra que el valor es una intersección de estos tipos.

El ejemplo más simple de los tipos `Union` se da a continuación (tenga en cuenta que la definición de tipos de usuario personalizados en el código dApp será compatible en futuras versiones):

```scala
type Human : { firstName: String, lastName: String, age: Int}
type Cat : {name: String, age: Int }
```

`Union(Human | Cat)` es un objeto con un campo, `age`, pero podemos usar la coincidencia de patrones
:
```scala
Human | Cat => { age: Int }
```
La coincidencia de patrones está diseñada para verificar un valor contra el tipo de dicho valor:
```scala
  let t = ...               # Cat | Human
  t.age                     # OK
  t.name                    # Compiler error
  let name = match t {      # OK
    case h: Human => h.firstName
    case c: Cat   => c.name
  }
```
La coincidencia de tipos es un mecanismo para:

```scala
let amount = match tx {              # tx es una transacción saliente actual
  case t: TransferTransaction => t.amount
  case m: MassTransferTransaction => m.totalAmount
  case _ => 0
}
```

El código anterior muestra un ejemplo de coincidencia de tipos. Hay diferentes tipos de transacciones en Waves, y dependiendo del tipo, la cantidad real de tokens transferidos se puede almacenar en diferentes campos. Si una transacción es `TransferTransaction` o `MassTransferTransaction`, usamos el campo correspondiente, mientras que en todos los demás casos, obtendremos 0.

## Funciones de lectura de estado

```scala
let readOrZero = match getInteger(this, "someKey") { # lectura de datos del estado
    case a:Int => a
    case _ => 0
}

readOrZero + 1

```
`getString` devuelve` Union (String | Unit)` porque mientras se lee datos desde la blockchain (el estado `key-value` de las cuentas) algunos pares `key-value` puedieran no existir.

```scala
let v = getInteger("3PHHD7dsVqBFnZfUuDPLwbayJiQudQJ9Ngf", "someKey")
v + 1    # no se compila, lo que obliga a un desarrollador a prever la posibilidad de un valor no existente para la clave

v.valueOrErrorMessage(“oops”) +  1 # se compila y ejecuta

let realStringValue2 = getStringValue(this, "someKey")

```
Para obtener el tipo real y el valor de Union, use la función `extract`, que terminará el script en caso de valor `Unit`. Otra opción es utilizar funciones especializadas como `getStringValue`,` getIntegerValue`, etc.

## If

```scala
let amount = 1610
if (amount > 42) then "Afirmo que la cantidad es mayor que 42"
  else if (amount > 100500) then "Too big!"
  else "Afirmo algo mas"
```

Las declaraciones `if` son bastante directas y similares a la mayoría de los otros lenguajes, con una diferencia importante con algunas: `if` es una expresión, por lo que debe tener una cláusula `else` (el resultado es asignable a una variable).

```scala
let a = 16
let result = if (a > 0) then a / 10 else 0 #
```

## Excepciones

```scala
throw("Aquí hay tun exto de excepción")
```
La función `throw` terminará la ejecución del script inmediatamente, con el texto proporcionado. No hay forma de atrapar las excepciones lanzadas.

La idea de `throw` es detener la ejecución y enviar comentarios útiles al usuario.


```scala
let a = 12
if (a != 100) then
  throw ("a no es 100, el valor actual es " + a.toString())
  else throw("A es 100")
```

## Estructuras de datos predefinidas

\#**QUE COMIENCE LA GUERRA SANTA**

Ride tiene muchas estructuras de datos predefinidas específicas para la cadena de bloques Waves, tales como: `Address`, `Alias`, `DataEntry`, `ScriptResult`, `Invocation`, `ScriptTransfer`, `TransferSet`, `WriteSet`, `AssetInfo`, `BlockInfo`.

```scala
let keyValuePair = DataEntry("someKey", "someStringValue")
```

Por ejemplo, `DataEntry` es una estructura de datos que describe un par clave-valor, p. para almacenamiento de cuenta.

```scala
let transferSet = TransferSet([ScriptTransfer("3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs", amount, unit)])
```
Todas las estructuras de datos se pueden usar para la verificación de tipos, la coincidencia de patrones y también sus constructores.

## Bucles con FOLD<N>

Como la máquina virtual de Ride no tiene ningún concepto de bucles, se implementan a nivel del compilador a través del macro FOLD <N>. El macro se comporta como la función `fold` en otros lenguajes de programación, tomando los argumentos: colección para iteración, valores iniciales del acumulador y función de plegado.

El aspecto más importante es N que define la cantidad máxima de interacciones sobre las colecciones. Esto es necesario para mantener costos de cálculo predecibles.

Por ejemplo, este código suma los números de la matriz:

```scala
let a = [1, 2, 3, 4, 5]
func foldFunc(acc: Int, e: Int) = acc + e
FOLD<5>(a, 0, foldFunc) # arrojando el valor 15
```

`FOLD<N>` también se puede usar para filtrar, mapear y otro tipo de operaciones. Aquí hay un ejemplo de mapa con reversa:
```scala
let a = [1, 2, 3, 4, 5]
func foldFunc(acc: List[Int], e: Int) = (e + 1) :: acc
FOLD<5>(a, [], foldFunc) # arroja los valores [6, 5, 4, 3, 2]
```

## Anotaciones

Las funciones pueden ser sin anotaciones, o con anotaciones `@ Callable` o` @ Verifier`.

```scala
func getPayment(i: Invocation) = {
  let pmt = i.payment.valueOrErrorMessage(“El pago debe estar adjunto”)
  if (isDefined(pmt.assetId)) then 
    throw("Esta función acepta solo tokens Waves")
  else
  	pmt.amount
}

@Callable(i)
func pay() = {
  let amount = getPayment(i)
  WriteSet([DataEntry(i.caller.bytes, amount)])
}
```

Las anotaciones pueden vincular algunos valores a la función. En el ejemplo anterior, la variable `i` estaba vinculada a la función` pagar` y almacenaba toda la información sobre el hecho de la invocación (clave pública, dirección, pago adjunto a la transacción, tarifa, Id de la transacción, etc.).

Las funciones sin anotaciones no están disponibles desde el exterior. Pueden ser llamadas solo dentro de otras funciones.

```scala
@Verifier(tx)
func verifier() = {
  match tx {
    case m: TransferTransaction => tx.amount <= 100 # puede enviar hasta 100 tokens
    case _ => false
  }
}
```

### @Verifier 

```scala
@Verifier(tx)
func verifier() = {
  match tx {
    case m: TransferTransaction => tx.amount <= 100 # puede enviar hasta 100 tokens
    case _ => false
  }
}
```

Una función con la anotación `@ Verifier` establece las reglas para las transacciones salientes de una aplicación descentralizada (dApp). Las funciones del verificador no se pueden invocar desde el exterior, pero se ejecutan cada vez que se intenta enviar una transacción desde una dApp.

Como resultado, las funciones del verificador siempre deben devolver un valor `booleano`, dependiendo de qué transacción se registrará en la cadena de bloques o no.

Los scripts de expresión (con la directiva `{- # CONTENT_TYPE EXPRESSION # -}`) junto con las funciones anotadas por @Verifier siempre deben devolver un valor booleano. Dependiendo de ese valor, la transacción será aceptada (en caso de `verdadero`) o rechazada (en caso de` falso`) por la cadena de bloques.


```scala
@Verifier(tx)
func verifier() = {
  sigVerify(tx.bodyBytes, tx.proofs[0], tx.senderPublicKey)

}
```
La función `@Verifier()` enlaza la variable `tx`, que es un objeto con todos los campos de la transacción saliente actual.

Sólo una una función `@Verifier()` puede ser definida en cada script de dApp.

### @Callable

Las funciones con la anotación `@ Callable` pueden llamarse (o invocarse) desde fuera de la cadena de bloques. Para llamar a una función invocable, debe utilizarse `InvokeScriptTransaction`.

```scala
@Callable(i)
func giveAway(age: Int) = {
  ScriptResult(
    WriteSet([DataEntry("age", age)]),
    TransferSet([ScriptTransfer(i.caller, age, unit)])
  )
}
```
Cada persona que invoque la función 'giveAway' recibirá tantos Waves como su edad y la dApp almacenará información sobre el hecho de la transferencia en su estado.


#### Acciones

Las acciones iniciales son `DataEntry`, que permiten escribir datos como un par `key-value`, y `ScriptTransfer`, que permite realizar una transferencia de tokens desde la dApp al destinatario. Otras acciones como `Issue / Reissue / Burn` están diseñadas para admitir operaciones nativas de token, así como la familia de operaciones de `Leasing` (Disponible en STDLIB_VERSION 4).

Una lista de estructuras DataEntry en `WriteSet` establecerá o actualizará pares `key-value` en el almacenamiento de una cuenta, mientras que una lista de estructuras ScriptTransfer en `TransferSet` moverá tokens de la cuenta dApp a otras cuentas.


```scala
@Callable(i)
func callMePlease(age: Int) = {
  TransferSet([ScriptTransfer(i.caller, age, unit)])
}
```

En STDLIB_VERSION 3, las funciones `@Callable` pueden devolver una de las siguientes estructuras: `ScriptResult`, `WriteSet`, `TransferSet`.

`WriteSet` puede contener hasta 100 `DataEntry`, mientras que `TransferSet` puede contener hasta 10 `ScriptTransfer`.

## Scripts de Cuenta vs de Asset
```scala
{-# STDLIB_VERSION 3 #-}
{-# CONTENT_TYPE EXPRESSION #-}
{-# SCRIPT_TYPE ACCOUNT #-}

let a = this # Address of the current account
a == Address(base58'3P9DEDP5VbyXQyKtXDUt2crRPn5B7gs6ujc') # verdadero si el script se ejecuta en la cuenta con la dirección definida

```
Ride scripts on the Waves blockchain can be attached to accounts and assets (`{-# SCRIPT_TYPE ACCOUNT #-}` defines it) and depending on the `SCRIPT_TYPE` keyword this can refer to different entities. For `ACCOUNT` script types this is an `Address` type.

For `ASSET` script type this will have `AssetInfo` type.

Los scripts Ride se pueden adjuntar a cuentas y activos de la cadena de bloques Waves (`{- # SCRIPT_TYPE ACCOUNT # -}` lo define) y, dependiendo de la palabra clave `SCRIPT_TYPE`, esto puede referirse a diferentes entidades. Para los tipos de script `ACCOUNT`, corresponde a un tipo de `Address`.

Para el tipo de script `ASSET`, este tendrá el tipo` AssetInfo`.

```scala
{-# STDLIB_VERSION 3 #-}
{-# CONTENT_TYPE EXPRESSION #-}
{-# SCRIPT_TYPE ASSET #-}
let a = this # AssetInfo of the current asset
a.assetId == AssetInfo(base58'3P9DEDP5VbyXQyKtXDUt2crRPn5B7gs6ujc').assetId # verdadero si el script se está ejecutando para el activo con el ID definido
```


## Pruebas y herramientas

Puede probar Ride en REPL tanto en línea en [https://ide.wavesplatform.com/font>(https://ide.wavesplatform.com/)] y en el escritorio a través de la terminal `surfboard`:

```scala
> npm i -g @waves/surfboard
> surfboard repl
```

Para un mayor desarrollo, las siguientes herramientas y utilidades son útiles:

- Complemento de Visual Studio Code: Waves-Ride
- La herramienta `surfboard` le permitirá REPLICAR y ejecutar pruebas en su nodo personal: [https://github.com/wavesplatform/surfboard]
- También debe instalar la extensión del navegador Waves Keeper: [https://wavesplatform.com/products-keeperfont](https://wavesplatform.com/products-keeper)
- IDE en línea con ejemplos: [https://ide.wavesplatform.com/font](https://ide.wavesplatform.com/)

Puede encontrar más ayuda e información sobre las herramientas aquí: [https://wavesplatform.com/developersfont](https://wavesplatform.com/developers)


## Enjoy the RIDE!

Esperemos que este articulo le haya dado una buena introducción a Ride: un lenguaje de programación sencillo, seguro y potente para la creación de contratos inteligentes y dApps en la cadena de bloques Waves.

Ahora debería poder escribir sus propios contratos inteligentes y tener todas las herramientas que necesita para probarlos antes de implementarlos en la cadena de bloques Waves.

Si necesita ayuda para aprender los conceptos básicos del lenguaje Ride, puede tomar el curso "Dominando Web3 con Waves": [https://stepik.org/course/56010/syllabus](https://stepik.org/course/56010/syllabus).
Waves también organiza talleres de desarrollo y hackatones en diferentes lugares del mundo. Consulte nuestra página manejada por la comunidad para mantenerse actualizado: [https://wavescommunity.com](https://wavescommunity.com)

¡Esperamos conocerte pronto en línea o fuera de línea!


