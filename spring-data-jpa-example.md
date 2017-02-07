
## Complete Code Example


### /pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.in28minutes</groupId>
	<artifactId>jpa-1</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>Spring-Data-JPA-FirstExample</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.1.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>


</project>
```
---

### /src/main/java/com/in28minutes/ClientBusiness.java

```java
package com.in28minutes;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import com.in28minutes.model.Client;
import com.in28minutes.repository.ClientRepository;

@Component
public class ClientBusiness {
	@Autowired
	ClientRepository clientRepository;

	public Long sumOfProductIds() {
		long sum = 0L;
		Iterable<Client> all = clientRepository.findAll();
		for (Client one : all) {
			sum += one.getUuid();
		}
		return sum;
	}
}
```
---

### /src/main/java/com/in28minutes/model/Amount.java

```java
package com.in28minutes.model;

import java.math.BigDecimal;

public class Amount {
	private BigDecimal value;
	private Currency currency;

	public BigDecimal getValue() {
		return value;
	}

	public void setValue(BigDecimal value) {
		this.value = value;
	}

	public Currency getCurrency() {
		return currency;
	}

	public void setCurrency(Currency currency) {
		this.currency = currency;
	}

}
```
---

### /src/main/java/com/in28minutes/model/Client.java

```java
package com.in28minutes.model;

import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Client {

	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	private Long uuid;

	private String name;

	@Enumerated(EnumType.STRING)
	private ClientType type;

	// private List<Product> products;

	public Client(String name, ClientType type) {
		super();
		this.name = name;
		this.type = type;
	}

	public Client() {
		super();
	}

	public Long getUuid() {
		return uuid;
	}

	public void setUuid(Long uuid) {
		this.uuid = uuid;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public ClientType getType() {
		return type;
	}

	public void setType(ClientType type) {
		this.type = type;
	}

	/*
	 * public List<Product> getProducts() { return products; }
	 * 
	 * public void setProducts(List<Product> products) { this.products =
	 * products; }
	 */

}
```
---

### /src/main/java/com/in28minutes/model/ClientType.java

```java
package com.in28minutes.model;

public enum ClientType {
	
	TYPE1("TYPE1"), TYPE2("TYPE2");
	
	private String code;

	ClientType(String code) {
		this.code = code;
	}
}
```
---

### /src/main/java/com/in28minutes/model/Currency.java

```java
package com.in28minutes.model;

public enum Currency {
	EUR, USD, INR
}
```
---

### /src/main/java/com/in28minutes/model/Product.java

```java
package com.in28minutes.model;

public class Product {
	private long id;
	private String name;
	private ProductType type;
	private Amount amount;

	public long getId() {
		return id;
	}

	public void setId(long id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public ProductType getType() {
		return type;
	}

	public void setType(ProductType type) {
		this.type = type;
	}

	public Amount getAmount() {
		return amount;
	}

	public void setAmount(Amount amount) {
		this.amount = amount;
	}

}
```
---

### /src/main/java/com/in28minutes/model/ProductType.java

```java
package com.in28minutes.model;

public enum ProductType {
	Type1,Type2
}
```
---

### /src/main/java/com/in28minutes/repository/ClientRepository.java

```java
package com.in28minutes.repository;

import java.util.List;

import org.springframework.data.repository.CrudRepository;

import com.in28minutes.model.Client;

public interface ClientRepository extends CrudRepository<Client, Long>{
	List<Client> findByName(String name);
}
```
---

### /src/main/java/com/in28minutes/SpringDataJpaFirstExampleApplication.java

```java
package com.in28minutes;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringDataJpaFirstExampleApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringDataJpaFirstExampleApplication.class, args);
	}
}
```
---

### /src/main/resources/application.properties

```
logging.level.:debug

spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```
---

### /src/test/java/com/in28minutes/repository/ClientRepositoryTest.java

```java
package com.in28minutes.repository;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.Assert.*;

import java.util.List;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;
import org.springframework.test.annotation.DirtiesContext;
import org.springframework.test.context.junit4.SpringRunner;

import com.in28minutes.model.Client;

@DataJpaTest
@RunWith(SpringRunner.class)
public class ClientRepositoryTest {

	@Autowired
	ClientRepository clientRepository;

	@Autowired
	TestEntityManager entityManager;

	@Test
	public void should_find_a_client_by_name() {
		List<Client> clients = clientRepository.findByName("abc");
		assertThat(clients).hasSize(1);
	}

	@Test
	@DirtiesContext
	public void should_delete_a_client_by_its_id() {

		Client before = clientRepository.findOne(1L);
		
		assertNotNull(before);
		
		clientRepository.delete(1L);

		Client after = clientRepository.findOne(1L);
		
		assertNull(after);
	}

}
```
---

### /src/test/java/com/in28minutes/SpringDataJpaFirstExampleApplicationTests.java

```java
package com.in28minutes;

import static org.junit.Assert.assertEquals;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureTestDatabase
public class SpringDataJpaFirstExampleApplicationTests {

	@Autowired
	ClientBusiness clientBusiness;

	@Test
	public void contextLoads() {
		Long sumOfProductIds = clientBusiness.sumOfProductIds();
		Long expected = 1L;
		assertEquals(expected, sumOfProductIds);
	}

}
```
---

### /src/test/resources/data.sql

```
insert into client values (1,'abc','TYPE1');
```
---
