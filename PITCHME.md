
# GraphQL

Mejorando las consultas a servidores

---

### Rest vs GraphQL

![Image-Absolute](https://cdn-images-1.medium.com/max/600/1*f_XvFD7FvliMM74WHJ0vRQ.png)

@fa[arrow-down]

+++
@title[GFM]

#### Estructura consulta

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

### BackEnd
#### Entidades y Esquemas con SPQR e Hibernate

+++
#### Construccion entidad
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
@[1-21]

+++
#### Manejo de datos en la entidad
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
@[1-19]

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
@[1-24]

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
@[26-39] (Modificacion de datos)
@[1-40]

+++
#### Controlador
```
@RestController
public class GraphQLController {
	
    private static final Logger LOGGER = LoggerFactory.getLogger(GraphQLController.class);

    private final GraphQL graphQL;

    // Este metodo se encarga de la creacion de las Querys y Mutations del esquema
    @Autowired
    public GraphQLController(ColoresClasesQuery coloresClasesQuery,
    								ColoresClasesMutation coloresClasesMutation) {

        //Schema generated from query classes
        GraphQLSchema schema = new GraphQLSchemaGenerator()
                .withResolverBuilders(
                        new AnnotatedResolverBuilder())
                .withOperationsFromSingleton(coloresClasesQuery)
                .withOperationsFromSingleton(coloresClasesMutation)
                /** Traduce los metodos en sentencias de GraphQL */
                .withValueMapperFactory(new JacksonValueMapperFactory())
                .generate();
        graphQL = GraphQL.newGraphQL(schema).build();

        LOGGER.info("Generated GraphQL schema using SPQR");
    }

    @PostMapping(value = "/graphql", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE, produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
    @ResponseBody
    public Map<String, Object> indexFromAnnotated(@RequestBody Map<String, Object> request, HttpServletRequest raw) {
        ExecutionResult executionResult = graphQL.execute(ExecutionInput.newExecutionInput()
                .query((String) request.get("query"))
                .operationName((String) request.get("operationName"))
                .variables((Map<String, Object>) request.get("variables"))
                .context(raw)
                .build());
        LOGGER.info(executionResult.toSpecification().toString());
        return executionResult.toSpecification();
    }
}
```

@[1,9] (Notacion Spring)
@[10-25] (Generacion de Esquema)
@[27-39] ("Mapeo" de respuesta)

+++
#### Conexion
```
spring:
    jpa:
        database: POSTGRESQL
        show-sql: true
        hibernate.ddl-auto: update
    datasource:
        platform: postgres
        url: jdbc:postgresql://localhost:5432/graphqltest
        username: desarrollo5
        password: Admin2018    
        driverClassName: org.postgresql.Driver

server:
    port: 8080
    
logging:
    level:
      org.hibernate.engine.jdbc.env.internal.LobCreatorBuilderImpl: ERROR
```

+++
#### Main Application
```
@SpringBootApplication
public class BdHorarioApplication {

	public static void main(String[] args) {
		SpringApplication.run(BdHorarioApplication.class, args);
	}
}
```

---

### FrontEnd
#### GraphQL en Android

+++
### Apollo Android

![Image-Absolute](https://cdn-images-1.medium.com/max/2000/1*-IFErNUtG6-6e_Fb2u0teA.png)

+++
### Librerias

<span style="font-size:0.6em;"> - Gradle Projecto: classpath 'com.apollographql.apollo:apollo-gradle-plugin:0.5.0'</span> |
<span style="font-size:0.6em;"> - Gradle Android: implementation 'com.apollographql.apollo:apollo-runtime:0.5.0'</span> |

---

#### Controlador
```
@Override public void onCreate() {
        super.onCreate();
        ApolloDemoApplication.context = getApplicationContext();

        OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .addNetworkInterceptor(new HttpLoggingInterceptor().setLevel(HttpLoggingInterceptor.Level.BODY))
                .build();

        NormalizedCacheFactory cacheFactory = new LruNormalizedCacheFactory(EvictionPolicy.builder().maxSizeBytes(10 * 1024).build());
        CacheKeyResolver cacheKeyResolver = new CacheKeyResolver() {
            @Nonnull
            @Override
            public CacheKey fromFieldRecordSet(@Nonnull ResponseField field, @Nonnull Map<String, Object> recordSet) {
                if (recordSet.containsKey("id")) {
                    String id = (String) recordSet.get("id");
                    return CacheKey.from(id);
                }
                return CacheKey.NO_KEY;
            }

            @Nonnull @Override
            public CacheKey fromFieldArguments(@Nonnull ResponseField field, @Nonnull Operation.Variables variables) {
                return CacheKey.NO_KEY;
            }
        };

        apolloClient = ApolloClient.builder()
                .serverUrl(BASE_URL)
                .normalizedCache(cacheFactory, cacheKeyResolver)
                .okHttpClient(okHttpClient)
                .build();
    }
```
@[5-7] (OkHttp)
@[9-25] (Especificando los parametros)
@[27-32] (Creando apolloClient)
@[1-32]

---

### Consultas

+++

```
public static void createAuthor(String firstname, String lastname) {
        application.apolloClient().mutate(
                CreateAuthorMutation.builder()
                        .firstName(firstname)
                        .lastName(lastname)
                        .build())
                .enqueue(createAuthorMutationCallback);
}
```

Preparacion de Query

+++

```
private static ApolloCall.Callback<CreateAuthorMutation.Data> createAuthorMutationCallback = new ApolloCall.Callback<CreateAuthorMutation.Data>() {
        @Override
        public void onResponse(@Nonnull final Response<CreateAuthorMutation.Data> dataResponse) {
            Log.d(ApolloDemoApplication.TAG, "Executed mutation: " + dataResponse.data().createAuthor().toString());
            mainActivity.fetchAuthors();
        }
        @Override
        public void onFailure(@Nonnull ApolloException e) {
            Log.d(ApolloDemoApplication.TAG, "Error:" + e.toString());
        }
    };
```

Respuesta
