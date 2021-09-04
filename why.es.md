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
    
      if idioma == "es" {
         return "Hola, " + nombre
      }
    
      if idioma == "fr" {
         return "Bonjour, " + nombre
      }
      
      // Imagínate docenas de idiomas más...
    
      return "Hello, " + nombre
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
      "es": "Hola",
      "fr": "Bonjour",
      //etc..
    }
    
    func saludo(idioma string) string {
      saludo, existe := saludos[idioma]
      
      if existe {
         return saludo
      }
      
      return "Hello"
    }

The nature of this refactor isn't actually important, what's important is I haven't changed behaviour. 

When refactoring you can do whatever you like, add interfaces, new types, functions, methods etc. The only rule is you don't change behaviour

### When refactoring code you must not be changing behaviour

This is very important. If you are changing behaviour at the same time you are doing _two_ things at once. As software engineers we learn to break systems up into different files/packages/functions/etc because we know trying to understand a big blob of stuff is hard. 

We don't want to have to be thinking about lots of things at once because that's when we make mistakes. I've witnessed so many refactoring endeavours fail because the developers are biting off more than they can chew.  

When I was doing factorisations in maths classes with pen and paper I would have to manually check that I hadn't changed the meaning of the expressions in my head. How do we know we aren't changing behaviour when refactoring when working with code, especially on a system that is non-trivial?

Those who choose not to write tests will typically be reliant on manual testing. For anything other than a small project this will be a tremendous time-sink and does not scale in the long run. 
 
**In order to safely refactor you need unit tests** because they provide

- Confidence you can reshape code without worrying about changing behaviour
- Documentation for humans as to how the system should behave
- Much faster and more reliable feedback than manual testing

#### An example in Go

A unit test for our `Hello` function could look like this

    func TestHello(t *testing.T) {
      got := Hello(“Chris”, es)
      want := "Hola, Chris"
    
      if got != want {
         t.Errorf("got %q want %q", got, want)
      }
    }

At the command line I can run `go test` and get immediate feedback as to whether my refactoring efforts have altered behaviour. In practice it's best to learn the magic button to run your tests within your editor/IDE. 

You want to get in to a state where you are doing 

- Small refactor
- Run tests
- Repeat

All within a very tight feedback loop so you don't go down rabbit holes and make mistakes.

Having a project where all your key behaviours are unit tested and give you feedback well under a second is a very empowering safety net to do bold refactoring when you need to. This helps us manage the incoming force of complexity that Lehman describes.

## If unit tests are so great, why is there sometimes resistance to writing them?

On the one hand you have people (like me) saying that unit tests are important for the long term health of your system because they ensure you can keep refactoring with confidence. 

On the other you have people describing experiences of unit tests actually _hindering_ refactoring.

Ask yourself, how often do you have to change your tests when refactoring? Over the years I have been on many projects with very good test coverage and yet the engineers are reluctant to refactor because of the perceived effort of changing tests.

This is the opposite of what we are promised!

### Why is this happening?

Imagine you were asked to develop a square and we thought the best way to accomplish that would be stick two triangles together. 

![Two right-angled triangles to form a square](https://i.imgur.com/ela7SVf.jpg)

We write our unit tests around our square to make sure the sides are equal and then we write some tests around our triangles. We want to make sure our triangles render correctly so we assert that the angles sum up to 180 degrees, perhaps check we make 2 of them, etc etc. Test coverage is really important and writing these tests is pretty easy so why not? 

A few weeks later The Law of Continuous Change strikes our system and a new developer makes some changes. She now believes it would be better if squares were formed with 2 rectangles instead of 2 triangles. 

![Two rectangles to form a square](https://i.imgur.com/1G6rYqD.jpg)

She tries to do this refactor and gets mixed signals from a number of failing tests. Has she actually broken important behaviours here? She now has to dig through these triangle tests and try and understand what's going on. 

_It's not actually important that the square was formed out of triangles_ but **our tests have falsely elevated the importance of our implementation details**. 

## Favour testing behaviour rather than implementation detail

When I hear people complaining about unit tests it is often because the tests are at the wrong abstraction level. They're testing implementation details, overly spying on collaborators and mocking too much. 

I believe it stems from a misunderstanding of what unit tests are and chasing vanity metrics (test coverage). 

If I am saying just test behaviour, should we not just only write system/black-box tests? These kind of tests do have lots of value in terms of verifying key user journeys but they are typically expensive to write and slow to run. For that reason they're not too helpful for _refactoring_ because the feedback loop is slow. In addition black box tests don't tend to help you very much with root causes compared to unit tests. 

So what _is_ the right abstraction level?

## Writing effective unit tests is a design problem

Forgetting about tests for a moment, it is desirable to have within your system self-contained, decoupled "units" centered around key concepts in your domain. 

I like to imagine these units as simple Lego bricks which have coherent APIs that I can combine with other bricks to make bigger systems. Underneath these APIs there could be dozens of things (types, functions et al) collaborating to make them work how they need to.

For instance if you were writing a bank in Go, you might have an "account" package. It will present an API that does not leak implementation detail and is easy to integrate with.

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
