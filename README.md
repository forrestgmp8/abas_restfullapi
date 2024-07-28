# abas_restfullapi

# Proper Responses in Spring Boot

Bu rehber, REST metodlarının doğru yanıtları döndürmesini sağlamaya yönelik adımları anlatır. `POST` metodunu güncelleyerek, yeni bir `Employee` nesnesi oluştururken doğru yanıtları nasıl döndüreceğimizi göstereceğiz.

## POST Method (newEmployee)

### Kod
```java
@PostMapping("/employees")
ResponseEntity<?> newEmployee(@RequestBody Employee newEmployee) {

  EntityModel<Employee> entityModel = assembler.toModel(repository.save(newEmployee));

  return ResponseEntity
      .created(entityModel.getRequiredLink(IanaLinkRelations.SELF).toUri())
      .body(entityModel);
}
```

Gereksinimler
Yukarıdaki kodu kullanmadan önce aşağıdaki sınıfları içe aktarmanız gerekmektedir:

java
Copy code
import org.springframework.hateoas.EntityModel;
import org.springframework.hateoas.server.mvc.WebMvcLinkBuilder;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
Detaylar
Yeni Employee Nesnesinin Kaydedilmesi:
Yeni Employee nesnesi, önceki gibi kaydedilir. Ancak, sonuç nesnesi EmployeeModelAssembler ile sarılır.

HTTP 201 Created Durum Mesajı:
Spring MVC'nin ResponseEntity sınıfı, bir HTTP 201 Created durum mesajı oluşturmak için kullanılır. Bu tür bir yanıt tipik olarak bir Location yanıt başlığı içerir ve bu başlık, modelin self ilişkili linkinden türetilen URI'yi kullanır.

Model Tabanlı Versiyonun Dönülmesi:
Kaydedilen nesnenin model tabanlı versiyonu döndürülür.

API Kullanımı
Aşağıdaki komutla yeni bir çalışan kaydı oluşturabilirsiniz:

$ curl -v -X POST localhost:8080/employees -H 'Content-Type:application/json' -d '{"name": "Samwise Gamgee", "role": "gardener"}' | json_pp

Çıktı
Aşağıdaki gibi bir yanıt almanız beklenir:

> POST /employees HTTP/1.1
> Host: localhost:8084
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Type:application/json
> Content-Length: 46
>
< Location: http://localhost:8080/employees/3
< Content-Type: application/hal+json;charset=UTF-8
< Transfer-Encoding: chunked
< Date: Fri, 10 Aug 20yy 19:44:43 GMT
<
{
  "id": 3,
  "firstName": "Samwise",
  "lastName": "Gamgee",
  "role": "gardener",
  "name": "Samwise Gamgee",
  "_links": {
    "self": {
      "href": "http://localhost:8080/employees/3"
    },
    "employees": {
      "href": "http://localhost:8080/employees"
    }
  }
}

