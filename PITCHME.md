
# GraphQL

Mejorando las consultas a servidores

---

### Rest vs GraphQL

![Image-Absolute](https://cdn-images-1.medium.com/max/600/1*f_XvFD7FvliMM74WHJ0vRQ.png)
  
@fa[arrow-down]

+++
@title[GFM]

### Estructura consulta

```
{
	"query": "mutation updateAuthor($lastName: String!, $firstName: String!, $id: ID) {  updateAuthor(lastName: $lastName, firstName: $firstName, id: $id) {   firstName    id    lastName  }}",
	"variables": {
		"id": "1",
		"firstName": "Nombre",
		"lastName": "Apellido"
	}
}

mutation{
  	updateAuthor(
    		id: 1
    		firstName: "Nombre"
    		lastName: "Apellido"
 	 ){
    		id
 	 }
}
```

@[2-3,10-11] (Tipo de consulta y datos)
@[4-6,12-14] (Valores a ingresar)

---

### Schema

```
type Article {
    id: ID!
    title: String!
    text: String!
    author: Profile!
    comments: [Comment]
}

type Profile {
    id: ID!
    username: String!
    bio: String
    articles: [Article!]
}

type Comment {
    id: ID!
    text: String!
    author: Profile!
}

type Query{
	articles: [Article]
    profiles: [Profile]
    comments: [Comment]
    article(id: ID!): Article
}

type Mutation {
	createArticle(title: String!, text: String!, author: Profile!, comments: [Comment]): Article!
	deleteArticle(id: ID!): Boolean
}
```
@[1,9,16] (Se definen las entidades)
@[2-4,10-12,17-18] (Se definen los campos)
@[5-6,13,19] (Se definen las relaciones)
@[22-27] (Se definen las consultas)
@[29-32] (Se definen las mutaciones]
