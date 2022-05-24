VTS Kit Spring Boot Microservice Archetype
-------
Archetype for generating a spring-boot service based on Spring Boot 2.6.6

Quickly create your project by following the instructions [here](#quick-start)


<b>Built-in Feature</b>
* [Auto Generation Base Code Structure](#code-structure)
* [Auto Generation Example API](#api-example)
* [Auto Generation API Docs](#api-docs)
* [Internationalization supported](#internationalization)
* [Prometheus metrics supported](#prometheus-metric)
* [Auto Mapper supported](#mapper)
* [Lombok supported](#lombok)
* [Spring Validation supported](#validation)
* [Common Exception Handling](#exception-handler)
* [Logging](https://github.com/vts-contributor/vts-kit-ms-logs-handler.git)

<b>Addition feature</b>
* [Integration Maria DB](https://github.com/vts-contributor/vts-kit-ms-maria-data.git)
* [Integration Mongo DB](https://github.com/vts-contributor/vts-kit-ms-mongo-data.git)
* [Integration MinIO](https://github.com/vts-contributor/vts-kit-ms-minio-integrated.git)
* [Integration Keycloak Auth](https://github.com/vts-contributor/vts-kit-ms-keycloak-auth.git)
* [Integration Kafka](https://github.com/vts-contributor/vts-kit-ms-kafka-integrated.git)
* [Integration Elasticsearch](https://github.com/vts-contributor/vts-kit-ms-elasticsearch-data.git)
* [Integration Kong Ingress](//github.com/vts-contributor/vts-kit-ms-kong-gateway.git)
* [Excel Handler Utility](https://github.com/vts-contributor/vts-kit-ms-excel-handler.git)
* [Redis Caching](https://github.com/vts-contributor/vts-kit-ms-redis-cache.git)

<b>Requirement</b>
-------
* JDK >= 1.8
* Maven >= 3.6.3
* IDE: Eclipse or Intellij IDEA

Quick start
-------
* Run this script to generate new project from this archetype
```shell script
mvn archetype:generate -DarchetypeGroupId=com.atviettelsolutions -DarchetypeArtifactId=vts-kit-ms-template -DarchetypeVersion=1.0.0
```

* Enter your project infomation (groupId, artifactId, package,...)

![Screenshot](screenshots/Capture01.PNG)

* Build Generated Project.
```shell script
mvn clean package
# Skip Unittest
mvn clean package -DskipTests
```

* Import Open API Docs URL into API Testing Tool (Ex: Postman)

`http://localhost:8080/<artifactId>/api-docs`

![Screenshot](screenshots/Capture02.PNG)

* Call Example API

`curl --location --request GET 'http://localhost:8080/<artifactId>/book/view/getListBook' --header 'Accept: */*'`

![Screenshot](screenshots/Capture03.PNG)

Usage
-------
### Spring profiles
* `local`: Default. Use for Development on local enviroment
* `docker`: Use for running on Docker or Kubernetes

### Code Structure

This Template Generated Code Structure base on MVC Pattern

![Screenshot](screenshots/Capture04.PNG)

### Example API
Example API with CRUD operation auto generated for testing

```java
@RestController
@RequestMapping("/book")
public class BookController extends AbstractRestController {
    private BookService bookService;

    @PostMapping("/edit/createBook")
    public ResponseEntity<Object> createBook(@Valid @RequestBody BookDTO bookInfo) {
        return RestUtils.responseOk(bookService.createBook(bookInfo));
    }

    @GetMapping("/view/getBookDetail/{bookId}")
    public ResponseEntity<Object> getBookDetail(@PathVariable("bookId") String bookId) {
        BookDTO rsDelete = bookService.getBookDetail(bookId);
        if(rsDelete == null){
            return RestUtils.response404(InternationalizationUtils.getMessage("msg.error.notFound"));
        }
        return RestUtils.responseOk(rsDelete);
    }

    @PostMapping("/edit/updateBook")
    public ResponseEntity<Object> updateBook(@Valid @RequestBody UpdateBookDTO bookInfo) {
        BookDTO rsUpdate = bookService.updateBook(bookInfo);
        if(rsUpdate == null){
            return RestUtils.response404(InternationalizationUtils.getMessage("msg.error.notFound"));
        }
        return RestUtils.responseOk(rsUpdate);
    }

    @PostMapping("/edit/deleteBook/{bookId}")
    public ResponseEntity<Object> deleteBook(@PathVariable("bookId") String bookId) {
        BookDTO rsDelete = bookService.deleteBook(bookId);
        if(rsDelete == null){
            return RestUtils.response404(InternationalizationUtils.getMessage("msg.error.notFound"));
        }
        return RestUtils.responseOk(rsDelete);
    }

    @GetMapping("/view/getListBook")
    public ResponseEntity<Object> getListBook() {
        return RestUtils.responseOk(bookService.getListBook());
    }

}
```

### API Docs

This Template Built-in supported Open API docs base on [Swagger OAS](https://swagger.io/specification/)

API Docs Auto Generated at URL: `http://localhost:8080/<artifactId>/api-docs`

### Internationalization

By default, This Template built-in supported Internationalization with 2 language: English and Vietnamese.\
Language auto resolve by Request Header `Accept-Language`

* First, define message on `messages_en.properties` and `messages_vi.properties` file.

`messages_en.properties`
```properties
msg.error.notFound=Infomation not found
```

`messages_vi.properties`
```properties
msg.error.notFound=Không tìm thấy thông tin
```

* Then, Get message in java code by properties key.
```properties
String message = InternationalizationUtils.getMessage("msg.error.notFound")
```

### Prometheus metrics
This Template built-in supported `spring-boot-starter-actuator` for monitoring.

Endpoint: `http://localhost:8080/<artifactId>/actuator/prometheus`

### Mapper
By supported [Mapstruct](https://mapstruct.org/) built-in. Mapper binding become easy and quickly

```java
@Mapper(componentModel = "spring")
public interface BookMapper extends IMapper<Book, BookDTO> {

}
```

### Lombok
With Lombok, Getter or Setter Auto generated
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class BookDTO {

    private String id;
    private String name;
    private String author;

}

```

### Validation
Use annotation for define validation. Refer [Javax Validation Constraints](https://docs.jboss.org/hibernate/beanvalidation/spec/2.0/api/javax/validation/constraints/package-summary.html).

Internationalization message built-in supported. Define on `validation_en.properties` and `validation_vi.properties`

`validation_en.properties`
```properties
validation.book.nameNotEmpty=Name must not empty
validation.book.authorNotEmpty=Author must not empty
```

`validation_vi.properties`
```properties
validation.book.nameNotEmpty=Tên không được rỗng
validation.book.authorNotEmpty=Tác giả không được rỗng
```

`Annotation Usage`
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class BookDTO {

    private String id;

    @NotEmpty(message = "{validation.book.nameNotEmpty}")
    private String name;

    @NotEmpty(message = "{validation.book.authorNotEmpty}")
    private String author;

}
```

### Exception handler
All Exception errors are handled centrally at `ExceptionHandler` for easy management and maintenance
```java
@ControllerAdvice
public class ExceptionHandler extends AbstractExceptionHandler {

    @Override
    protected ResponseEntity<ErrorDTO> handleAccessDeniedException() {
        return super.handleAccessDeniedException();
    }

    @Override
    protected ResponseEntity<ErrorDTO> handleCommonException(Exception ex) {
        return super.handleCommonException(ex);
    }

    @Override
    protected ResponseEntity<ErrorDTO> handleMethodArgumentException(MethodArgumentNotValidException ex) {
        return super.handleMethodArgumentException(ex);
    }

    @Override
    protected ResponseEntity<ErrorDTO> handleConstraintViolationException(ConstraintViolationException ex) {
        return super.handleConstraintViolationException(ex);
    }

    @Override
    protected ResponseEntity<ErrorDTO> handleMissingPartException(Exception ex) {
        return super.handleMissingPartException(ex);
    }
}
```

Contribute
-------
#### Setting up the development environment
* <b>IDE:</b> Eclipse, Intellij IDEA
* <b>JDK:</b> >= JDK 8
* <b>Maven:</b> >= 3.6.0
* <b>Build:</b>
```shell script
# cd archetype directory
cd archetype
# build
mvn clean install -DskipTests
```
#### Contribute Guidelines
If you have any ideas, just open an issue and tell us what you think.

If you'd like to contribute, please refer [Contributors Guide](CONTRIBUTING.md)

License
-------
This code is under the [MIT License](https://opensource.org/licenses/MIT).

See the [LICENSE](LICENSE) file for required notices and attributions.
