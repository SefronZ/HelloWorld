
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

### Esquema

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
@[29-32] (Se definen las mutaciones)

---

### Entidades y Esquemas con SPQR e Hibernate

```
@Entity
public class ColoresClases {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;
	
	private String detalleColor;
	
	private int numeroColor;
	
	public ColoresClases() {
		
	}
	
	public ColoresClases(Integer id) {
		this.id = id;
	}
	
	public ColoresClases(String detalleColor, int numeroColor) {
		this.detalleColor = detalleColor;
		this.numeroColor = numeroColor;
	}
```
@[1,4-5] (Notaciones Hibernate)

+++
```
@GraphQLId
	@GraphQLNonNull
	@GraphQLQuery(name = "id")
	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	@GraphQLNonNull
	@GraphQLQuery(name = "detalleColor")
	public String getDetalleColor() {
		return detalleColor;
	}

	public void setDetalleColor(String detalleColor) {
		this.detalleColor = detalleColor;
	}
```

@[1-3,12-13] (Notaciones SPQR)

+++
#### Repositorio
```
@Repository
public interface ColoresClasesRepo extends CrudRepository<ColoresClases, Integer>{

}
```
