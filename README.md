# OverTheWire

## Bandit

## Natas

### LVL 0
Hay un password en los comentarios de la web.
```
g9D9cREhslqBKtcA2uocGHPfMZVzeFK6
```
### LVL 1
Bloquea el click derecho con un javascript, podemos pulsar Control + U o poner "view-source" detrás de la URL.
La contraseña está en los comentarios.
```
h4ubbcXrWqsTo7GGnnUMLppXbOogfBZ7
```
### LVL 2
Podemos observar en el codigo fuente de la pagina que nos adjunta una imagen, si vamos al directorio que esta la imagen `files` podremos ver que el indexador esta allowed. Encontramos el users.txt. Donde esta la contraseña
```
G6ctbMJ5Nb4cbFwhpMPSvxGHhQ7I6W8Q
```
### LVL 3
Vamos al inspector de codigo y encontramos un comentario que dice que google no va a poder indexar este sitio. Entonces si vamos a robots.txt encontramos una carpeta en disallow `s3cr3t`. Vamos a esa carpeta y tenemos el user.txt
```
tKOcJIbzM4lTs8hbCmzn5Zr4434fGZQm
```
### LVL 4 
Nos dicen que para sera auterizados si vienen de natas5. Entonces pillamos el burpsuite y cambiamos el Referer a natas5.

```
Z0NsrtIkJoKALBCLi5eqFfcRN82Au2oD
```

### LVL 5
Nos deniegan el acceso. Podemos ver que tenemos una cookie `loggedin` que tiene el valor de 0. Eso quiere decir que es false, entonces nos estan denegando el acceso. Si lo ponemos a 1 podremos acceder.
```
fOIvE0MDtPTgRhqmmvvAOt2EfXR6uQgR
```

### LVL 6
Podemos ver que guardan el fichero secrets y podemos acceder a verlo.

```
jmxSiH3SP6Sonf8dv66ng8v1cIEdjXWr
```

### LVL 7 

Podemos hacer un path reverse y obtener la contraseña. /etc/natas_webpass/natas8

```
a6bZCNYwdKqN5cGP11ZdtPg0iImQQhAB
```

### LVL 8

Decodificamos la contraseña.

```php
<?php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function decodeSecret($secret) {
    return base64_decode(strrev(hex2bin($secret)));
}

echo decodeSecret($encodedSecret);

?>
```
```
Sda6t0vkOPkM8YeOZkAGVhFoaplvlJFd
```

### LVL 9
Ponemos `| cat /etc/natas_webpass/natas10` con el pipe nos saltamos el echo y vamos directamente a ver el natas10
```
D44EcsFkLxPIkAAKLosx8z3hxX1Z4MCE
```

### LVL 10
Ponemos `a /etc/natas_webpass/natas11`, asi buscamos una a dentro del fichero natas11.

```
1KFqoJXi6hRaPluAmk8ESDW4fSysRoIg
```

### LVL 11
Necesitamos poner "showpassword"=>"no" a yes.


Obtenemos la key ya que tenemos el json y la cookie. (XOR)
```php
<?php
$encodedJSON = "MGw7JCQ5OC04PT8jOSpqdmkgJ25nbCorKCEkIzlscm5oKC4qLSgubjY%3D";

function decodejson($secret) {
    return base64_decode($secret);
}

$cookie = decodejson($encodedJSON);

function xor_encrypt($in) {
    $key = json_encode(array( "showpassword"=>"no", "bgcolor"=>"#ffffff"));
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

echo xor_encrypt($cookie);

?>

```
key = KNHL

Cuando hayamos obtenido la llave ahora podremos cambiar la cookie a nuestro gusto.
```php
<?php

$defaultdata = array( "showpassword"=>"yes", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = "KNHL";
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

echo base64_encode(xor_encrypt(json_encode($defaultdata)));

?>
```

```
YWqo0pjpcXzSIl5NMAVxg12QxeC1w9QG
```