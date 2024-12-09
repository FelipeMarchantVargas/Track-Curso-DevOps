# Problemas que me surgieron

## Problemas con boxes de CentOS7

Mi principal problemas al ejecutar CentOS7 fue al momento de obtener los paquetes con "wget", como

```
yum install unzip
```

ya que tenía problemas con la configuración de los Mirror (donde se obtienen los paquetes).

## Problemas de autenticación con CentOS9

Cuando intentaba hacer

```
vagrant up
```

la máquina intentaba conectar eternamente con el cliente ssh. No he encontrado solución a ese error.

## Problema con interfaz al aplicar ip privada

Cuando usaba una ip privada, siempre se me pedía que introdujera la interfaz que iba a usar. Bastaba con introducir "1" y dar enter, pero me sorprendió que no fuera automático.
