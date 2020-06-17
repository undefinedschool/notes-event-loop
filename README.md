# ![Funciones _First-Class_](https://i.imgur.com/CwGrO3W.png)

### 👉 Ver [todas las notas](https://github.com/undefinedschool/notes)

## Contenido

- [Intro](https://github.com/undefinedschool/notes-event-loop#intro)
- [Concurrencia y el _Event Loop_](https://github.com/undefinedschool/notes-event-loop#concurrencia-y-el-event-loop)
  - [Event Loop](https://github.com/undefinedschool/notes-event-loop#event-loop)
  - [Event Loop y _rendering_](https://github.com/undefinedschool/notes-event-loop#event-loop-y-rendering)
  - [Paso a paso](https://github.com/undefinedschool/notes-event-loop#paso-a-paso)
  - [_macrotasks_ & _microtasks_](https://github.com/undefinedschool/notes-event-loop#macrotasks--microtasks)
- [Event Loop y Async/Await](https://github.com/undefinedschool/notes-event-loop#event-loop-y-asyncawait)
- [Tips](https://github.com/undefinedschool/notes-event-loop#tips)
- [_Web Workers_](https://github.com/undefinedschool/notes-event-loop#web-workers)

---

## Intro

JavaScript es un lenguaje de programación _single-thread_, lo que equivalente a decir que _sólo puede hacer 1 cosa a la vez_, ejecutar 1 sola instrucción y finalizarla antes de pasar a la siguiente.

**Tener 1 sólo thread de ejecución significa tener también 1 sólo [_stack_](https://www.youtube.com/watch?v=W8AeMrVtFLY)**, por lo que las operaciones _lentas_ (como el procesamiento de imágenes o los requests HTTP) resultan _bloqueantes_ (_bloquean_ el thread de ejecución), en el sentido de que el resto de las instrucciones de nuestro código no se ejecutarán hasta que estas finalicen.

> **Nota:** llamamos operaciones _bloqueantes_ (o _blocking_) a aquellas que son _lentas_, de las que no podemos obtener un resultado de forma inmediata

Si tenemos muchas operaciones _bloqueantes_, vemos claramente el gran impacto que esto tendría en la performance de nuestra aplicación. Un browser por ejemplo, no podría realizar ciertas operaciones como renderizar la UI correspondiente, resultando en una experiencia de uso poco deseable.

[![What the heck is the event loop anyway? | Philip Roberts | JSConf EU](https://img.youtube.com/vi/8aGhZQkoFbQ/0.jpg)](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
> Ver [What the heck is the event loop anyway? | Philip Roberts | JSConf EU](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

> 👉 **La forma que tenemos de evitar _bloquear_ nuestra aplicación es escribiendo código asincrónico, utilizando [_callbacks_](https://github.com/undefinedschool/notes-callbacks) o [_Promises_](https://github.com/undefinedschool/notes-es6-promises)**.

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

## Concurrencia y el _Event Loop_

Como mencionamos antes, **JavaScript es _single-thread_**, por lo que no puede ejecutar más de 1 tarea (proceso) a la vez. Esto es cierto, **pero la plataforma (o _entorno_) sobre la que corremos JavaScript si permite realizar más tareas**, de forma [_concurrente_](https://www.youtube.com/watch?v=kMr3mF71Kp4). Por ejemplo, **a través del browser tenemos acceso a las [_Web APIs_](https://developer.mozilla.org/en-US/docs/Web/API)**, que nos proveen de más _threads_ **para realizar ciertas tareas** en un 2do plano, es decir, **fuera del thread principal**. **Algo similar ocurre en [Node](https://nodejs.org/uk/docs/guides/dont-block-the-event-loop/)**.

![JavaScript & the Event Loop](https://d6vdma9166ldh.cloudfront.net/media/images/9aacbcd0-44c5-45e1-b3eb-be84a2eb99d8.png)

> 👉 **Cuando estas _APIs_ externas terminan de realizar la tarea asignada, la envían a una cola de tareas (_callback queue_)**. Es en este momento cuando **aparece el _event loop_ para** realizar una tarea muy simple: encargarse de **chequear el _stack_ de funciones actual y el _callback queue_; si el _stack_ se encuentra vacío, toma el primer callback<sup>1</sup>** (del _callback queue_) **y lo pushea al _stack_ para que sea ejecutado**.

Las tareas asincrónicas se delegan a APIs externas (threads adicionales) y luego son encoladas (en el _callback queue_) para eventualmente ejecutarse en el thread principal.

> 👉 Es importante notar que **el _Event Loop_ no forma parte de JavaScript** en si, sino del entorno donde este se ejecute (browser, Node, etc).

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

### Event Loop

El concepto de _Event Loop_ resulta entonces, bastante simple. Se trata de un _loop infinito_ que espera a que el _thread principal_ esté libre y haya tareas disponibles esperando, para asignarle una nueva tarea, proveniente del _callback queue_, para luego quedarse esperando hasta que haya más tareas.

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

### Event Loop y _rendering_

**El _rendering_ en el browser nunca sucede mientras el runtime de JavaScript (engine) se encuentra ejecutando una tarea**, independientemente de si una tarea toma o no mucho tiempo. Los cambios que realicemos en el DOM no se verán reflejados hasta que la tarea finalice.

Por lo tanto **si una tarea toma mucho tiempo, estamos bloqueando la UI (y el _thread_)** y el browser no puede ocpuarse de otras cosas (como procesar eventos). Luego de cierto tiempo, dependiendo de cada browser, mostrará un mensaje indicando que la página no responde, por lo que necesitaremos cerrar la pestaña, el browser o terminar de alguna forma el proceso. 

> 👉 **Es por esto que debemos evitar realizar tareas muy complejas (computacionalmente costosas) en el thread principal (o que tomen mucho tiempo) si queremos evitar una mala UX**.

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

### Paso a paso

> 👉 Ver [✨♻️ JavaScript Visualized: Event Loop](https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif)

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

### _macrotasks_ & _microtasks_

A su vez, las tareas asincrónicas pueden dividirse en _macro_ y _micro_ tareas:

- _macrotasks_: como [`SetInterval`](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Timeouts_and_intervals#setInterval) o [`SetTimeout`](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Timeouts_and_intervals#setTimeout), **se ejecutan en el siguiente _event loop_**, es decir, la próxima iteración.
- _microtasks_: como una [Promise](https://github.com/undefinedschool/notes-es6-promises) resuelta, **se ejecutan antes del inicio del próximo _event loop_**, es decir, tienen prioridad sobre las _macrotasks_ y se van a ejecutar antes. Los mismo sucede con [_Async/Await_](https://github.com/undefinedschool/notes-es2017-async-await/), al tratarse de otra forma de escribir Promises.

Podríamos decir entonces, que en realidad el _callback queue_ está compuesto por 2 colas más pequeñas: [_microtask queue_](https://javascript.info/microtask-queue) y [_macrotask queue_](https://javascript.info/event-loop#macrotasks-and-microtasks) y que el _Event Loop_ se va a encargar de asignar al thread principal, primero todas las _microtasks_ y a continuación, cuando todas estas se completen, las _macrotasks_.

![](https://i.imgur.com/YfUpNlJ.png)

> 👉 **Inmediatamente después de cada _macrotarea_, el _engine_ ejecuta todas las tareas de la cola de _microtareas_, antes de cualquier otra _macrotask_, _rendering_, etc**. Las _microtasks_ siempre tienen prioridad sobre el resto de las tareas asincrónicas.

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

## Event Loop y [`async/await`](https://github.com/undefinedschool/notes-es2017-async-await)

[![The Async Await Episode I Promised](https://img.youtube.com/vi/vn3tm0quoqE/0.jpg)](https://www.youtube.com/watch?v=vn3tm0quoqE)
> Ver [The Async Await Episode I Promised](https://www.youtube.com/watch?v=vn3tm0quoqE)

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

## Tips

- Escribir **código asincrónico** (ver [callbacks](https://github.com/undefinedschool/notes-callbacks), [Promises](https://github.com/undefinedschool/notes-es6-promises), [Async/Await](https://github.com/undefinedschool/notes-es2017-async-await))
- Evitar realizar operaciones computacionalmente costosas (a nivel CPU) en el [_stack_](https://www.youtube.com/watch?v=W8AeMrVtFLY) para **no bloquear el event loop** (como procesamiento de imágenes o video)
- [Dividir tareas costosas en tareas más chicas](https://javascript.info/event-loop#use-case-1-splitting-cpu-hungry-tasks), aprovechando el asincronismo

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

## _Web Workers_

**Para realizar cálculos complejos u operaciones muy largas y evitar bloquear el event loop, lo más conveniente es utilizar [_Web Workers_](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers)**.

Básicamente, **nos permiten ejecutar código en otro thread, fuera del principal**.

> 👉 **Web Workers pueden intercambiar información con el thread principal, pero tienen sus propias variables _e incluso su propio event loop_**.

Los **Web Workers no tienen acceso al DOM**, por lo que suelen utilizarase para realizar cálculos complejos y aprovechar los múltiples cores del CPU _para poder ejecutar código de forma paralela_, a diferencia de la concurrencia que nos provee el asincronismo.

[↑ Ir al inicio](https://github.com/undefinedschool/notes-event-loop#contenido)

---

<sup>1</sup>Hay tareas del _callback queue_ que tienen prioridad sobre otras y por lo tanto el event loop las moverá antes al thread principal. Ver [_macrotasks & microtasks_](https://github.com/undefinedschool/notes-event-loop#macrotasks--microtasks).
