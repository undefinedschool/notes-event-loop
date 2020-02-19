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
