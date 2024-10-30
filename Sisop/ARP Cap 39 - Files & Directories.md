# Interlude: Files and Directories

Para poder guardar de forma persistente los datos se crean nuevas abstracciones:

- **File:** Es un array de bytes, en donde se puede leer o escribir. Tiene un nombre de bajo nivel, tambien conocído como **número de inodo**.

- **Directory:** Es un mapeo de nombres a archivos o directorios. Contiene una lista de pares (nombre, número de inodo).

