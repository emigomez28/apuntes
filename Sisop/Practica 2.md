# Syscall wait

- Espera a que un _algún proceso_ hijo termine.
- Es _bloqueante_ si hay procesos que esperar.
- Devuelve el _PID_ del proceso hijo que terminó.

## Huerfanos

- Procesos hijos que terminan y el padre no les hizo ningún _wait_, el padre no existe.

## Zombies

- El proceso hijo termina pero el padre no hizo _wait_, el padre sigue viviendo.

### Nota

> El pipe se hace siempre antes del fork, lo cual duplica todos los fd que esten abiertos. Si no se cierran los fd que no se usan cuando se puede, se pueden tener problemas.
