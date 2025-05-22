
## About me
- 👋 Hi, I’m @GbrlLyl
- 👀 I’m interested in web development.
- 🌱 I’m currently learning electronjs & python.

## Tech Stack
![PHP](https://img.shields.io/badge/-PHP-05122A?style=flat&logo=python)&nbsp;
![JavaScript](https://img.shields.io/badge/-JavaScript-05122A?style=flat&logo=javascript)&nbsp;
![Java](https://img.shields.io/badge/-Java-05122A?style=flat&logo=Java&logoColor=FFA518)&nbsp;
![C](https://img.shields.io/badge/-C-05122A?style=flat&logo=C&logoColor=A8B9CC)&nbsp;
![C++](https://img.shields.io/badge/-C++-05122A?style=flat&logo=C%2B%2B&logoColor=00599C)&nbsp;
![C#](https://img.shields.io/badge/-CSharp-05122A?style=flat&logo=C%2B%2B&logoColor=00599C)&nbsp;
![Python](https://img.shields.io/badge/-Python-05122A?style=flat&logo=python)&nbsp;
![R (Statistics)](https://img.shields.io/badge/-R-05122A?style=flat&logo=R&logoColor=276DC3)\
![HTML](https://img.shields.io/badge/-HTML-05122A?style=flat&logo=HTML5)&nbsp;
![CSS](https://img.shields.io/badge/-CSS-05122A?style=flat&logo=CSS3&logoColor=1572B6)\
![Visual Studio Code](https://img.shields.io/badge/Visual%20Studio%20Code-0078d7.svg?style=for-the-badge&logo=visual-studio-code&logoColor=white)&nbsp;
![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)
![Postman](https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)&nbsp;

## Contact Me
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/loyolagabriel/)
<!---[![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/)
[![Facebook](https://img.shields.io/badge/Facebook-1877F2?style=for-the-badge&logo=facebook&logoColor=white)](https://www.facebook.com/)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://www.instagram.com/)-->


<!---
## My Stats
![GbrlLyl's GitHub stats](https://github-readme-stats.vercel.app/api?username=GbrlLyl&show_icons=true)
GbrlLyl/GbrlLyl is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->




Facturama utiliza la autenticación de tipo Basic para poder identificar al usuario que está enviando las peticiones.

> **_⚠️ Importante_**: Para poder usar la API de Facturama, primero deberás crear una cuenta en el ambiente que deseas utilizar. ¿Cómo crear una cuenta en sandbox?

### Generación del token de autenticación

El token se genera al concatenar el _nombre de usuario, dos puntos, contraseña_. Luego esa cadena se deberá convertir a base64. Ejemplo: 

*   Si mi nombre de usuario es **_`pruebas`_** y mi contraseña es **_`pruebas2011`_**, la concatenación queda como **_`pruebas:pruebas2011`_**
*   El resultado del paso anterior se convierte a _base64_. Para este ejemplo, la conversión resulta en **_`cHJ1ZWJhczpwcnVlYmFzMjAxMQ==`_**.
*   Se agrega el header de autenticación tipo `Basic`, y se especifica el token creado en el paso anterior. Ejemplo:  
    `**_Authorization: Basic cHJ1ZWJhczpwcnVlYmFzMjAxMQ==_**`

El token solo se para algunos de los _SDK_, como el de _**JavaScript**_ o _**Node**_. Para los otros _SDK_ y para _Postman_ solo es necesario colocar el usuario y contraseña donde se requiera, especificando autentificación de tipo `Basic`.

#### Ejemplo para el SDK de JavaScript

var valuesFacturama = {
    useragent: "tu\_nombre\_de\_usuario",
    token: "tu\_token",
    url: "https://apisandbox.facturama.mx/"     // URL del ambiente
};

#### Ejemplo para el SDK de Node

var valuesFacturama = {
    token: "tu\_token",
    url: "https://apisandbox.facturama.mx/",    // URL del ambiente
    user: 'tu\_nombre\_de\_usuario',
    pass: 'tu\_contraseña'
};

#### Ejemplo para Postman

![](https://cdn.elev.io/file/uploads/GMmWbjNkwx_4xETR7KjSJNkYai4UVRsw7WzUfwzPgZk/lml8binCNzp8NUHugE6jwakJ4W90mHug7BvSKN2VgTM/1747952099242-_CM.png)

#### Para otros SDK

Puedes encontrar cómo autenticarte en el resto de _SDK_ en la wiki de cada uno de los repositorios, especificando tu nombre de usuario y contraseña. 

*   GitHub de Facturama: [https://github.com/Facturama](https://github.com/Facturama)
