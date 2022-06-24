# Modelando para plataforma aprendizaje dev

##  Se solicita lo siguiente

- mostrar los últimos cursos 
  - los cursos se muestran por áreas (front, back...)
- mostrar un curso con sus videos
- en un video mostramos su autor

## Creando Colecciones 

a simple vista solo con los requerimientos podemos definir que como poco necesitaremos las siguientes colecciones

- cursos
- videos
- autores

Ademas necesitamos una colección que de una tacada nos devuelva la información para nuestra pagina principal

- cursos_categorias

vamos a definir un poco cada uno y que patrón o patrones decidí usar

## Modelando

### cursos_categorias

esta colección es la que se va a usar para crear nuestra pagina principal, en este punto he pensado que podríamos usar ***extended ref*** para incluir los datos que nos interesa mostrar por curso y categoría. De esta forma podríamos tener en un solo documento una serie de cursos definidos en un array por categorías.

tendríamos algo como esto

```javascript
// course_categories
{
  _id: objectid, //generado por mongo
  categorie:[
    {
      name:'frontend',
      courses:[
        {
          _id: objectid,
          name: 'Recorriendo la FRONTera',
          creation: '10/10/2022',
          related_categories:[
            'react',
            'frontend',
          ],
          description:'aqui va un lorem ipsum como una casa pero al final termino escribiendo yo',
          photo: 'url.alafotoquevamosaponer.com'
        }
        ... // el resto de cursos para la categoría frontend
      ]
    }
    ... // el resto de categorías que vayamos necesitando
  ]
}


```

Seria ideal que para controlar el tamaño del documento estuviera limitado el numero de cursos por categoría, entiendo que esto lo hacemos a través de código o con alguna validación de esquema a través de mongo.

Creo que lo ideal para esta colección sera trabajar sobre un documento e ir actualizando, por que no se hasta que punto se podría aprovechar un histórico sobre la información de este documento

### cursos

para los cursos también use el ***extended ref*** y también ***schema versioning***

Para esta colección si vi interesante usar un patrón de control de versiones en caso de que queramos cambiar el modelo bien añadiendo o eliminando campos.

Ademas tenemos un caso muy similar al anterior en el que necesitamos ciertos datos de otra colección, como podría ser información del autor o cierta información de los videos

veamos como quedaría nuestro modelo

```javascript
// courses
{
  _id: objectId,
  name:'Recorriendo la FRONTera',
  categorie: 'frontend',
  photo:'url.detufoto.com',
  description: 'aqui va un lorem ipsum como una casa pero al final termino escribiendo yo',
  authors: {
    main:{
      name:'Paco',
      _id: objectId,
    },
    related:[
      {
        name:'pepe',
        _id: objectId,
      },
      ... // resto de autores relacionados
    ]
  },
  videos:[
    {
      _id: objectId,
      name:'El primer paso de muchos',
      description:'Aprende lo basico de lo que es es el frontend, aqui bla bla',
      author: 'Paco'
    }
    ... // resto de videos
  ]
  creation_date: '10/10/2022',
  last_modified: null,
  version_schema: '1',
}

```

### videos

En este punto solo me hacia falta recuperar cierta informacion de el autor del video asi que volví a usar el ***extended reference*** y ***schema versioning***.

```javascript
{
  _id:objectId,
  name:'El primer paso de muchos',
  text_guide: 'Bueno como podeis imaginar esto es un lorem ipsum hand made, aqui escribiendo por escribir y tal...',
  author:{
    _id:objectId,
    name:'Paco',
  },
  version_schema:'1',
}
```
creo en futuras iteraciones esta colección es muy susceptible al cambio. Por ejemplo añadiendo campos que den mas información del video o creando una relación que nos indique algún tipo de jerarquía para poder agruparlos.

### autores

no hay muchas sorpresas también me decante por el patrón de ***extended reference***, en este caso para contener cierta información de los cursos del autor o en los que haya participado.

```javascript
{
  _id:objectId,
  name:'Paco',
  description:' pues me gusta pescar y cuando puedo ago cursos para la plataforma de TomatoCode.tv',
  social:[
    twitter:'@pacofisherman',
    linkedIn:'Pacoempresario',
    github:'hiruleWarrior57',
    other:[],
  ],
  photo:'url.depaco.com',
  courses:[
    {
      _id:objectId,
      name:'Recorriendo la FRONTera',
      description: 'aqui va un lorem ipsum como una casa pero al final termino escribiendo yo',
    }
  ]
}
```

## Conclusiones

Es mi primera experiencia modelando algo de verdad, creo que para la iteración básica el tandem de extended reference y schema versioning, me viene muy bien.

creo que para los siguiente desafíos tendré que cambiar varias cosas en varias colecciones.

como hacer una jerarquía en cursos, ser capaz de relacionar videos con las posibles categorías a las que pueda pertenecer, incluir la colección de usuarios para manejar estados algo más complejos.

