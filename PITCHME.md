
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

+++
#### Entidad
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

@[1] (Notacion Spring)

+++
#### Consultas
```
@Service
public class ColoresClasesQuery {
	
	@Autowired
	ColoresClasesRepo clasesRepo;
	
	@GraphQLQuery(name = "obtenerColoresClases")
    public List<ColoresClases> obtenerColoresClases() {
		List<ColoresClases> list = new ArrayList<ColoresClases>();
		list = (List<ColoresClases>) clasesRepo.findAll();
		list = bubbleSort(list);
		return list;
	}
	
	@GraphQLQuery(name = "obtenerColorClasePorId")
	public Optional<ColoresClases> getById(@GraphQLNonNull @GraphQLArgument(name="id") Integer id) {
		Optional<ColoresClases> author = clasesRepo.findById(id);
		return author;
	}
	
	@GraphQLQuery(name = "contarColoresClases")
	public Long contarColoresClases() {
		return clasesRepo.count();
	}
}
```

@[1,4] (Notaciones Spring)
@[7,15-16,21] (Notaciones SPQR)

+++
#### Mutaciones
```
@Service
public class ColoresClasesMutation {
	
	@Autowired
	ColoresClasesRepo clasesRepo;
	
	@GraphQLNonNull
	@GraphQLMutation(name = "registrarColor")
    public ColoresClases registrarColor(@GraphQLNonNull @GraphQLArgument(name = "detalleColor") String detalleColor,
    								@GraphQLNonNull @GraphQLArgument(name = "numeroColor") int numeroColor){
		ColoresClases colorNuevo = new ColoresClases();
		colorNuevo.setDetalleColor(detalleColor);
		colorNuevo.setNumeroColor(numeroColor);
		
		clasesRepo.save(colorNuevo);
        return colorNuevo;
    }
	
	@GraphQLMutation(name = "borrarColor")
	public boolean borrarColor(@GraphQLId @GraphQLArgument(name = "id") Integer id) {		
		
		clasesRepo.deleteById(id);
		return true;
	}
	
	@GraphQLMutation(name = "modificarColor")
	public ColoresClases modificarColor(@GraphQLNonNull @GraphQLArgument(name = "detalleColor") String detalleColor, 
								@GraphQLNonNull @GraphQLArgument(name = "numeroColor") int numeroColor, 
								@GraphQLId @GraphQLArgument(name = "id") Integer id) {
		
		ColoresClases coloresClases = clasesRepo.findById(id).orElse(null);
		
		coloresClases.setDetalleColor(detalleColor);
		coloresClases.setNumeroColor(numeroColor);
		
		clasesRepo.save(coloresClases);
		
    return coloresClases;
	}
}
```

@[1,4] (Notaciones Spring)
@[7-10,19-20,26-29] (Notaciones SPQR)
@[7-17] (Ingreso de datos)
@[19-24] (Eliminacion de datos)
@[26-40] (Modificacion de datos)
