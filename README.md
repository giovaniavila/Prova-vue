# Prova-vue

- caso de erro utilizar pnpm 

configurar banco em main/resources/application.properties
```
## Logging
# Show sql statement
logging.level.org.hibernate.SQL = debug

## Spring DATASOURCE (DataSourceAutoConfiguration & DataSourceProperties) nome do banco, nome padrao do postgre e senha do banco
spring.datasource.url = jdbc:postgresql://localhost:5432/test 
spring.datasource.username = postgres
spring.datasource.password = 180857

# Hibernate ddl auto (create, create-drop, validate, update)
spring.jpa.hibernate.ddl-auto = validate
```


atualizar rota do backend em main.ts
<br>
``` axios.defaults.baseURL = 'https://09cbw18q-8080.brs.devtunnels.ms/' ```
