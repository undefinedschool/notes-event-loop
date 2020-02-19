> El siguiente contenido fue elaborado por [@_nhsz](https://twitter.com/_nhsz) como guía para las clases de [undefined school](https://twitter.com/undefinedSchool)
> Son bienvenidos los _issues_ y _PRs_ para mejorar el contenido, corregir errores, etc. 

> 👉 Si te resultó útil, **se agradece que lo compartas para que le llegue a más gente!**

# [WIP] 🔁 Notas sobre el _Event Loop_

JavaScript es un lenguaje de programación _single-thread_, lo que equivalente a decir que _sólo puede hacer 1 cosa a la vez_, ejecutar 1 sola instrucción y finalizarla antes de pasar a la siguiente.

**Tener 1 sólo thread de ejecución significa tener también 1 sólo stack**, por lo que las operaciones _lentas_ (como el procesamiento de imágenes o los requests HTTP) resultan _bloqueantes_ (_bloquean_ el thread de ejecución), en el sentido de que el resto de las instrucciones de nuestro código no se ejecutarán hasta que estas finalicen.

Si tenemos muchas operaciones _bloqueantes_, vemos claramente el gran impacto que esto tendría en la performance de nuestra aplicación. Un browser por ejemplo, no podría realizar ciertas operaciones como renderizar la UI correspondiente, resultando en una experiencia de uso poco deseable.

[![What the heck is the event loop anyway? | Philip Roberts | JSConf EU](https://img.youtube.com/vi/8aGhZQkoFbQ/0.jpg)](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
> Ver [What the heck is the event loop anyway? | Philip Roberts | JSConf EU](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

La forma más simple que tenemos de evitar _bloquear_ nuestra aplicación es escribiendo código asincrónico, por ejemplo, utilizando [_callbacks_](https://github.com/undefinedschool/notes-callbacks).

## Concurrencia y el _Event Loop_

Como mencionamos antes, **JavaScript es _single-thread_**, por lo que en teoría no puede realizar tareas de forma _concurrente_. Esto es cierto, **pero la plataforma (o _entorno_) sobre la que corremos JavaScript si permite realizar más tareas**. Por ejemplo, **a través del browser tenemos acceso a las [_Web APIs_](https://developer.mozilla.org/en-US/docs/Web/API)**, que nos proveen de más _threads_ para realizar ciertas tareas en un 2do plano. **Algo similar ocurre en [Node](https://nodejs.org/uk/docs/guides/dont-block-the-event-loop/)**.

![JavaScript & the Event Loop](https://d6vdma9166ldh.cloudfront.net/media/images/9aacbcd0-44c5-45e1-b3eb-be84a2eb99d8.png)

> 👉 **Cuando estas _APIs_ externas terminan de realizar la tarea asignada, la envían a una cola de tareas (_callback queue_)**. Es en este momento cuando **aparece el _event loop_ para** realizar una tarea muy simple: se encarga de **chequear el _stack_ de funciones actual y el _callback queue_ y si el _stack_ se encuentra vacío, toma el primer callback** (del _callback queue_) **y lo pushea al _stack_ para que sea ejecutado**.
