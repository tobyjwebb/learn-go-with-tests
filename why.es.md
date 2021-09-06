# Por qué pruebas unitarias y cómo aprovecharlas

[Aquí hay un enlace a una charla que di sobre este tema (en inglés)](https://www.youtube.com/watch?v=Kwtit8ZEK7U)

Si no te van los vídeos, a continuación hay una versión en texto.

## Software 

La promesa del software es que puede cambiar. De ahí viene la parte llamada _soft_ (flexible), es maleable comparado con hardware. Un equipo de ingeniería genial debería ser una baza increíble para su empresa, pudiendo desarrollar sistemas que pueden evolucionar con el negocio y sigan generando valor.

Así que, ¿por qué se nos da tan mal? ¿De cuántos proyectos te enteras que simplemente fracasan? O simplemente se convierten en "legacy" (legado) y tienen que ser reescritos por completo (y estas nuevas versiones a menudo fracasan también).

Exactamente, ¿cómo "fracasa" un sistema de software? ¿No se puede cambiar hasta que esté bien? ¡Esa era la promesa del software!

Muchas personas están eligiendo Go para desarrollar porque el diseño del lenguaje tiene una serie de características que hacen que sea más probable que no se convierta en software "legacy".

- Comparado con mi vida previa de Scala, donde [describí cómo tiene justo la cantidad de cuerda para ahorcarte](http://www.quii.dev/Scala_-_Just_enough_rope_to_hang_yourself), Go tiene sólo 25 palabras clave, y _muchos_ sistemas se pueden desarrollar con la librería estándar y un par de otras pequeñas librerías. La idea es que con Go, puedes escribir código, y volver a leerlo dentro de 6 meses y todavía tendrá sentido.
- Las herramientas para testeo, rendimiento, limpieza y distribución son de primera categoría comparado con la mayoría de las alternativas.
- La librería estándar es muy completa.
- Velocidad de compilación muy rápida para bucles de retroalimentación más cerradas.
- La promesa de retrocompatibilidad de Go. Parece que Go obtendrá "generics" (programación con plantillas) y otras características en el futuro, pero los diseñadores han prometido que incluso código Go que se escribió hace 5 años se seguirá compilando. Yo tardé literalmente semanas actualizando un proyecto desde Scala 2.8 a 2.10.

Incluso con todas estas grandes características aún podemos hacer sistemas terribles, así que deberíamos mirar al pasado para entender las lecciones de ingeniería de software que aplican sin importar cómo de reluciente (o no) es tu lenguaje de preferencia.

En 1974, un ingeniero de softare listo llamado [Manny Lehman](https://es.wikipedia.org/wiki/Meir_M._Lehman) [(en inglés)](https://en.wikipedia.org/wiki/Manny_Lehman_%28computer_scientist%29) escribió [las leyes de Lehman de la evolución del software](https://es.wikipedia.org/wiki/Leyes_de_Lehman_de_la_evolución_del_software) [(Lehman's laws of software evolution)](https://en.wikipedia.org/wiki/Lehman%27s_laws_of_software_evolution).

> Las leyes describen un equilibrio entre fuerzas que empujan nuevos desarrollos por un lado, y fuerzas que ralentizan el progreso por el otro.

Estas fuezas parecen importantes para entender si queremos tener alguna esperanza de encontrarnos en un bucle infinito de distribución de sistemas que se convierten en legado, y que tengan que ser reescritos una y otra vez.

## La Ley del Cambio Continuo

> Cualquier sistema de software usado en el mundo real debe cambiar o deja de tener utilidad más y más en el entorno

Parece obvio que un sistema _tiene_ que cambiar o se vuelve menos útil, pero ¿cuántas veces se ignora este hecho?

A muchos equipos se les incentiva para entregar un proyecto en una fecha en particular, y después se cambian a otro proyecto. Si el software "tiene suerte", hay por lo menos algún tipo de entrega a otro grupo de individuos para mantenerlo, pero claro, ellos no lo escribieron.

Muchas veces se elige un sistema de apoyo (framework) que ayude en la "entrega rápida", pero el enfoque no es en la longevidad del sistema en cuanto a sus necesidades de evolucionar.

Aunque seas un ingeniero de software increíble, caerás preso al hecho de no conocer las necesidades futuras de tu sistema. Con los cambios de negocio, parte del código brillante que has escrito dejará de ser relevante.

Lehman estaba de racha en los 70 porque nos dio otra ley para digerir.

## La Ley de la Complejidad Creciente

> A medida que evoluciona un sistema, su complejidad crece a menos que se dedique trabajo para reducirlo

Lo que nos está diciendo aquí es que no podemos tener equipos de desarrollo como factorías de mejoras ciegas, apilando más y más mejoras a un software con la esperanza que sobrevivirá a largo plazo.

**Tenemos** que seguir gestionando la complejidad del sistema a medida que nuestro conocimiento de nuestro dominio cambia.

## Refactorización

Hay _muchas_ facetas de la ingeniería de software que mantiene el software adaptable, tales como:

- Empoderamiento del desarrollador
- Código "bueno" en genral. Separación de intereses sensatas, etc, etc...
- Habilidades de comunicación
- Arquitectura
- Observabilidad
- Facilidad de despliegue
- Pruebas automatizadas
- Bucles de retroalimentación

Me voy a centrar en la refactorización. Es una palabra que sale mucho "necesitamos refactorizar esto" - dicho a un desarrollador en su primer día de programación sin pensarlo dos veces.

De dónde proviene la palabra? En qué se diferencia la refactorización de simplemente escribir código?

Sé que muchos, y yo mismo hemos _pensado_ que estabamos refactorizando, pero estábamos equivocados

[Martin Fowler describo cómo la gente se equivoce](https://martinfowler.com/bliki/RefactoringMalapropism.html)

> Sin embargo, el término "refactorización" se usa cuando no es apropiado. Si alguien habla sobre un sistema estando roto varios días mientras se refactoriza, puedes estar bastante seguro que no están refactorizando.

Así que, ¿qué es exactamente?

### Factorización

Cuando estuviste aprendiendo matemáticas en el colegio probablemente aprendiste sobre la factorización. Aquí hay un ejemplo muy simple

Calcula `1/2 + 1/4`

Para hacer esto, debes _factorizar_ los denominaddores, convirtiendo la expresión a

`2/4 + 1/4` que ya se puede convertir en `3/4`. 

Podemos tomar algunas lecciones importantes de esto. Cuando _factorizamos la expresión_, **no hemos cambiado el significado de la expresión**. Ambos equivalen a `3/4` pero hemos hecho que sea más fácil de trabajar con ello: cuando cambiamos `1/2` a `2/4`, encaja nuestro "dominio" más fácilmente.

Cuando refactorizas tu código, estás intentando encontrar maneras de hacer que tu código sea más fácil de entender y que "encaje" con tu comprensión actual de lo que el sistema debe hacer. Crucialmente **no debes cambiar el comportamiento**.

#### Un ejemplo en Go

Aquí hay una función que saluda a `nombre` en un `idioma` en particular

    func Hola(nombre, idioma string) string {
    
      if idioma == "en" {
         return "Hello, " + nombre
      }
    
      if idioma == "fr" {
         return "Bonjour, " + nombre
      }
      
      // Imagínate docenas de idiomas más...
    
      return "Hola, " + nombre
    }

Me da mala espina tener muchas sentencias `if` y tenemos una duplicación de la concatenazión del saludo de cada idioma con `, ` y el `nombre.` Así que refactorizaré el código.

    func Hola(nombre, idioma string) string {
      	return fmt.Sprintf(
      		"%s, %s",
      		saludo(idioma),
      		nombre,
      	)
    }
    
    var saludos = map[string]string {
      "en": "Hello",
      "fr": "Bonjour",
      //etc..
    }
    
    func saludo(idioma string) string {
      saludo, existe := saludos[idioma]
      
      if existe {
         return saludo
      }
      
      return "Hola"
    }

La naturaleza de esta refactorización realmente no es importante, lo que importa es que no he cambiado el comportamiento.

Cuando refactorizas puedes hacer lo que quieras, agregar interfaces, nuevos tipos, funciones, métodos etc. La única regla es que no puedes cambiar el comportamiento.

### Cuando refactorizas código no debes cambiar el comportamiento

Esto es de vital importancia. Si cambias comportamiento a la vez, estás haciendo _dos_ cosas simultáneas. Como ingenieros de software aprendemos a dividir sistemas en distintos ficheros/paquetes/funciones/etc porque sabemos que intentar entender un gran conjunto de algo es difícil.

No queremos tener que estar pensando sobre muchas cosas a la vez porque es entonces cuando nos equivocamos. He sido testigo de muchos intentos fallidos de refactorización porque los desarrolladores intentaron morder más de lo que pudieron masticar.

Cuando hacía factorizaciiones en clases de matemáticas con papel y boli, tenía que comprobar manualmente que no había cambiado el significado de las expresiones en la cabeza. ¿Cómo sabemos que no estamos cambiando el comportamiendo cuando refactorizamos, especialmente en un sistema que no es trivial?

Aquellos que no escriben pruebas típicamente dependerán de pruebas manuales. Para cualquier cosa que no sea un proyecto pequeño esto será un agota-tiempos enorme y no será escalable a largo plazo.
 
**Para refactorizar con seguridad necesitas pruebas unitarias** porque nos proporcionan

- Confianza de que puedes cambiar la forma del código sin tener que preocuparte sobre el comportamiento
- Documentación para humanos sobre cómo el sistema debería comportarse
- Un retroalimentación mucho más rápido y fiable que testeo manual

#### Un ejemplo en Go

A unit test for our `Hello` function could look like this
Una prueba unitaria para nuestra función `Hola` podría parecerse a esto:


    func TestHola(t *testing.T) {
      obtenido := Hola("Chris", en)
      esperado := "Hello, Chris"
    
      if obtenido != esperado {
         t.Errorf("obtenido %q, esperado %q", obtenido, esperado)
      }
    }

En la línea de comandos puedo ejecutar el comando `go test` y obtener una retroalimentación inmediata en cuanto a si mis esfuerzos de refactorización han cambiado el comportamiento. En la práctica es mejor aprenderse el botón mágico para ejecutar las pruebas dentro de tu editor/IDE.

Lo óptimo es llegar a un punto en el que estás haciendo

- Una pequeña refactorización
- Ejecución de pruebas
- Repetir

Todo con un bucle de retroalimentación rápido para que no empieces a abarcar demasiado y cometer fallos.

Tener un proyecto donde todos los comportamientos principales estén cubiertos con pruebas unitarias y tengas una retroalimentación en bastante menos de un segundo es un arma muy poderosa para hacer refactorizaciones sin temor sobre lo que necesites hacer. Esto nos ayuda a manejar la fuerza entrante de complejidad que describe Lehman.

## Si las pruebas unitarias son tan buenas, ¿por qué hay veces que se duda en codificarlas?

Por un lado tienes a personas (como yo) diciendo que las pruebas unitarias son importantes para la salud a largo plazo de tu sistema porque aseguran que puedes seguir refactorizando con confianza.

Por el otro lado hay personas describiendo experiencias de pruebas unitarias _obstacularizando_ la refactorización.

Pregúntate, ¿cuántas veces tienes que cambiar las pruebas cuando refactorizas? A lo largo de los años he estado en muchos proyectos con una cobertura de pruebas muy buena, y sin embargo los ingenieros son rehacios a refactorizar por una percepción de un esfuerzo de cambiar las pruebas.

¡Esto es justo lo contrario de lo que se nos prometió!

### ¿Por qué pasa esto?

Imagina que te piden desarrollar un cuadrado, y pensamos que la mejor solución sería juntar dos triángulos.

![Dos triángulos rectángulos para formar un cuadrado](https://i.imgur.com/ela7SVf.jpg)

Escribimos nuestras pruebas unitarias alrededor del cuadrado para asegurarnos que los laterales son iguales, y después escribimos algunas pruebas acerca de los triángulos. Queremos asegurarnos que nuestros triángulos se renderizan correctamente así que colocamos afirmaciones de que los ángulos deben sumar 180 grados, quizás hacemos 2 de ellos, etc etc. La cobertura de pruebas es muy importante y escribir estas pruebas es bastante fácil así que ¿por qué no?

Un par de semanas más tarde, la Ley de Cambio Contínuo azota nuestro sistema y una nueva desarrolladora hace algunos cambios. Ahora piensa que sería mejor que los cuadrados se formaran de dos rectángulos en vez de dos triángulos.

![Dos rectángulos para formar un cuadrado](https://i.imgur.com/1G6rYqD.jpg)

La nueva programadora intenta hacer esta refactorización, y recibe señales mixtos de unas pruebas fallidas. ¿Ha roto alguna funcionalidad importante? Ahora tiene que buscar entre las pruebas de los triángulos e intentar entender qué está pasando.

_Realmente no es importante que el cuadrado se formara de triángulos_ but **nuestras pruebas han equivocadamente puesto la importancia en detalles de implementación**. 

## Preferir probar comportamiento en vez de detalle de implementación

Cuando oigo como algunas personas se quejan sobre las pruebas unitarias, es a menudo debido a que las pruebas se han concebido al nivel equivocado de abstracción. Están probando detalles de implementación, prestando demasiada importancia en colaboradores, y usando demasiados "dobles de prueba" (mocks, o test doubles).

Creo que la raíz de este problema es un malentendido de lo que hacen las pruebas unitarias, y la persecución de métricas vanidosas (cobertura de pruebas).

Si digo que sólo se debe probar el comportamiento, ¿no deberíamos escribir pruebas de sistema o caja negra? Este tipo de pruebas sí tienen mucho valor en cuanto a la validación de experiencias de usuario principales, pero normalmente son caros para escribir y lentos para ejecutar. Por esa razón no son de gran ayuda para la _refactorización_ porque el bucle de retroalimentación es muy lento. Adicionalmente, pruebas de caja negra no suelen ayudar mucho con respecto a los problemas raíces comparado con las pruebas unitarias.

Así que ¿cuál es _exactamente? el nivel de abstracción correcto?

## Escribir pruebas unitarias efectivas es un problema de diseño

Si obviamos por el momento las pruebas, es deseable tener dentro de tu sistema "unidades" autocontenidas y desacopladas centradas alrededor de conceptos clave de tu dominio.

Me gusta imaginarme estas unidades como sencillos ladrillos de Lego que tienen interfaces (APIs) coherentes que puedo usar para combinar con otros ladrillos para construir sistemas más grandes. Debajo de estas interfaces podría haber docenas de cosas (tipos, funciones, etcétera) colaborando para hacer que funcionen como deben.

Por ejemplo si estuvieras escribiendo un banco en Go, podrías tener un paquete (package) "cuenta". Presentará un API que no enseñe detalles de implementación, y que será fácil de integrar.

If you have these units that follow these properties you can write unit tests against their public APIs. _By definition_ these tests can only be testing useful behaviour. Underneath these units I am free to refactor the implementation as much as I need to and the tests for the most part should not get in the way.

### Are these unit tests?

**YES**. Unit tests are against "units" like I described. They were _never_ about only being against a single class/function/whatever.

## Bringing these concepts together

We've covered

- Refactoring
- Unit tests
- Unit design

What we can start to see is that these facets of software design reinforce each other. 

### Refactoring

- Gives us signals about our unit tests. If we have to do manual checks, we need more tests. If tests are wrongly failing then our tests are at the wrong abstraction level (or have no value and should be deleted).
- Helps us handle the complexities within and between our units.

### Unit tests

- Give a safety net to refactor.
- Verify and document the behaviour of our units.

### (Well designed) units

- Easy to write _meaningful_ unit tests.
- Easy to refactor.

Is there a process to help us arrive at a point where we can constantly refactor our code to manage complexity and keep our systems malleable?

## Why Test Driven Development (TDD)

Some people might take Lehman's quotes about how software has to change and overthink elaborate designs, wasting lots of time upfront trying to create the "perfect" extensible system and end up getting it wrong and going nowhere. 

This is the bad old days of software where an analyst team would spend 6 months writing a requirements document and an architect team would spend another 6 months coming up with a design and a few years later the whole project fails.

I say bad old days but this still happens! 

Agile teaches us that we need to work iteratively, starting small and evolving the software so that we get fast feedback on the design of our software and how it works with real users;  TDD enforces this approach.

TDD addresses the laws that Lehman talks about and other lessons hard learned through history by encouraging a methodology of constantly refactoring and delivering iteratively.

### Small steps

- Write a small test for a small amount of desired behaviour
- Check the test fails with a clear error (red)
- Write the minimal amount of code to make the test pass (green)
- Refactor
- Repeat

As you become proficient, this way of working will become natural and fast.

You'll come to expect this feedback loop to not take very long and feel uneasy if you're in a state where the system isn't "green" because it indicates you may be down a rabbit hole. 

You'll always be driving small & useful functionality comfortably backed by the feedback from your tests.

## Wrapping up 

- The strength of software is that we can change it. _Most_ software will require change over time in unpredictable ways; but don't try and over-engineer because it's too hard to predict the future.
- Instead we need to make it so we can keep our software malleable. In order to change software we have to refactor it as it evolves or it will turn into a mess
- A good test suite can help you refactor quicker and in a less stressful manner
- Writing good unit tests is a design problem so think about structuring your code so you have meaningful units that you can integrate together like Lego bricks.
- TDD can help and force you to design well factored software iteratively, backed by tests to help future work as it arrives.
