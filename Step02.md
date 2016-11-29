\pom.xml
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.in28minutes</groupId>
	<artifactId>springboot-example</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>test</name>
	<description>tests</description>
	<packaging>war</packaging>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.4.0.RELEASE</version>
	</parent>

	<dependencies>
		
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-hateoas</artifactId>
		</dependency>
		
		 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>		
	</dependencies>

	<properties>
		<java.version>1.8</java.version>
	</properties>


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
\readme.md
```
```
\src\main\java\com\mastering\spring\springboot\Application.java
```
package com.mastering.spring.springboot;

import java.util.Arrays;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);

		System.out.println("Let's inspect the beans provided by Spring Boot:");

		String[] beanNames = ctx.getBeanDefinitionNames();
		Arrays.sort(beanNames);
		for (String beanName : beanNames) {
			System.out.println(beanName);
		}
	}

}
```
\src\main\java\com\mastering\spring\springboot\bean\Todo.java
```
package com.mastering.spring.springboot.bean;

import java.util.Date;

import javax.validation.constraints.Size;

public class Todo {

	private int id;
	private String user;

	@Size(min = 10, message = "Enter atleast 10 Characters.")
	private String desc;

	private Date targetDate;
	private boolean isDone;

	public Todo() {

	}

	public Todo(int id, String user, String desc, Date targetDate, boolean isDone) {
		super();
		this.id = id;
		this.user = user;
		this.desc = desc;
		this.targetDate = targetDate;
		this.isDone = isDone;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getUser() {
		return user;
	}

	public void setUser(String user) {
		this.user = user;
	}

	public String getDesc() {
		return desc;
	}

	public void setDesc(String desc) {
		this.desc = desc;
	}

	public Date getTargetDate() {
		return targetDate;
	}

	public void setTargetDate(Date targetDate) {
		this.targetDate = targetDate;
	}

	public boolean isDone() {
		return isDone;
	}

	public void setDone(boolean isDone) {
		this.isDone = isDone;
	}

	@Override
	public String toString() {
		return String.format("Todo [id=%s, user=%s, desc=%s, targetDate=%s, isDone=%s]", id, user, desc, targetDate,
				isDone);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + id;
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Todo other = (Todo) obj;
		if (id != other.id)
			return false;
		return true;
	}

}
```
\src\main\java\com\mastering\spring\springboot\bean\WelcomeBean.java
```
package com.mastering.spring.springboot.bean;

public class WelcomeBean {
	private String message;

	public WelcomeBean(String message) {
		super();
		this.message = message;
	}

	public String getMessage() {
		return message;
	}
}
```
\src\main\java\com\mastering\spring\springboot\controller\BasicController.java
```
package com.mastering.spring.springboot.controller;

import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.mastering.spring.springboot.bean.WelcomeBean;

@RestController
public class BasicController {

	private static final String helloWorldTemplate = "Hello World, %s!";

	@RequestMapping("/welcome")
	public String welcome() {
		return "Hello World";
	}

	@RequestMapping("/welcome-with-object")
	public WelcomeBean welcomeWithObject() {
		return new WelcomeBean("Hello World");
	}

	@RequestMapping("/welcome-with-parameter/name/{name}")
	public WelcomeBean welcomeWithParameter(@PathVariable String name) {
		return new WelcomeBean(String.format(helloWorldTemplate, name));
	}

}
```
\src\main\java\com\mastering\spring\springboot\controller\TodoController.java
```
package com.mastering.spring.springboot.controller;

import static org.springframework.hateoas.mvc.ControllerLinkBuilder.linkTo;
import static org.springframework.hateoas.mvc.ControllerLinkBuilder.methodOn;

import java.net.URI;
import java.util.List;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.hateoas.Resource;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.mastering.spring.springboot.bean.Todo;
import com.mastering.spring.springboot.service.TodoService;

@RestController
public class TodoController {
	@Autowired
	private TodoService todoService;

	@RequestMapping("/users/{name}/todos")
	public List<Todo> retrieveTodos(
			@PathVariable String name) {
		return todoService.retrieveTodos(name);
	}

	@RequestMapping("/users/{name}/todos/{id}")
	public Resource<Todo> retrieveTodo(
			@PathVariable String name,
			@PathVariable int id) {
		Resource<Todo> todo = new Resource<Todo>(
				todoService.retrieveTodo(id));
		todo.add(linkTo(methodOn(TodoController.class)
				.retrieveTodo(name, id)).withSelfRel());
		todo.add(linkTo(methodOn(TodoController.class)
				.retrieveTodo(name, id))
						.withRel("all-todos"));
		return todo;
	}

	@RequestMapping(method = RequestMethod.POST, path = "/users/{name}/todos")
	ResponseEntity<?> add(@PathVariable String name,
			@Valid @RequestBody Todo todo) {

		Todo createdTodo = todoService.addTodo(name,
				todo.getDesc(), todo.getTargetDate(),
				todo.isDone());

		if (createdTodo == null)
			return ResponseEntity.noContent().build();

		URI location = ServletUriComponentsBuilder
				.fromCurrentRequest().path("/{id}")
				.buildAndExpand(createdTodo.getId())
				.toUri();

		return ResponseEntity.created(location).build();

	}
}
```
\src\main\java\com\mastering\spring\springboot\service\TodoService.java
```
package com.mastering.spring.springboot.service;

import java.util.ArrayList;
import java.util.Date;
import java.util.Iterator;
import java.util.List;

import org.springframework.stereotype.Service;

import com.mastering.spring.springboot.bean.Todo;

@Service
public class TodoService {

	private static List<Todo> todos = new ArrayList<Todo>();
	private static int todoCount = 3;

	static {
		todos.add(new Todo(1, "Jack", "Learn Spring MVC", new Date(), false));
		todos.add(new Todo(2, "Jack", "Learn Struts", new Date(), false));
		todos.add(new Todo(3, "Jill", "Learn Hibernate", new Date(), false));
	}

	public List<Todo> retrieveTodos(String user) {
		List<Todo> filteredTodos = new ArrayList<Todo>();
		for (Todo todo : todos) {
			if (todo.getUser().equals(user))
				filteredTodos.add(todo);
		}
		return filteredTodos;
	}

	public Todo addTodo(String name, String desc, Date targetDate, boolean isDone) {
		Todo todo = new Todo(++todoCount, name, desc, targetDate, isDone);
		todos.add(todo);
		return todo;
	}

	public void deleteTodo(int id) {
		Iterator<Todo> iterator = todos.iterator();
		while (iterator.hasNext()) {
			Todo todo = iterator.next();
			if (todo.getId() == id) {
				iterator.remove();
				--todoCount;
			}
		}
	}

	public Todo retrieveTodo(int id) {
		for (Todo todo : todos) {
			if (todo.getId() == id)
				return todo;
		}
		return null;
	}

	public void updateTodo(Todo todo) {
		todos.remove(todo);
		todos.add(todo);
	}
}
```
\src\main\resources\application.properties
```
server.port: 9000
management.port: 9001
```
\src\test\java\com\mastering\spring\springboot\controller\BasicControllerIT.java
```
package com.mastering.spring.springboot.controller;

import static org.hamcrest.Matchers.containsString;
import static org.hamcrest.Matchers.equalTo;
import static org.junit.Assert.assertThat;

import java.net.URL;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.context.embedded.LocalServerPort;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.mastering.spring.springboot.Application;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class BasicControllerIT {

	@LocalServerPort
	private int port;

	private URL base;
	private TestRestTemplate template;

	@Before
	public void setUp() throws Exception {
		this.base = new URL("http://localhost:" + port);
		template = new TestRestTemplate();
	}

	@Test
	public void welcome() throws Exception {
		ResponseEntity<String> response = template.getForEntity(base.toString() + "/welcome", String.class);
		assertThat(response.getBody(), equalTo("Hello World"));
	}

	@Test
	public void welcomeWithObject() throws Exception {
		ResponseEntity<String> response = template.getForEntity(base.toString() + "/welcome-with-object", String.class);
		assertThat(response.getBody(), containsString("Hello World"));
	}

	@Test
	public void welcomeWithParameter() throws Exception {
		ResponseEntity<String> response = template.getForEntity(base.toString() + "/welcome-with-parameter/name/Buddy",
				String.class);
		assertThat(response.getBody(), containsString("Hello World, Buddy"));
	}
}
```
\src\test\java\com\mastering\spring\springboot\controller\BasicControllerTest.java
```
package com.mastering.spring.springboot.controller;

import static org.hamcrest.Matchers.containsString;
import static org.hamcrest.Matchers.equalTo;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;

@RunWith(SpringRunner.class)
@WebMvcTest(BasicController.class)
public class BasicControllerTest {

	@Autowired
	private MockMvc mvc;

	@Test
	public void welcome() throws Exception {
		mvc.perform(MockMvcRequestBuilders.get("/welcome").accept(MediaType.APPLICATION_JSON))
				.andExpect(status().isOk()).andExpect(content().string(equalTo("Hello World")));
	}

	@Test
	public void welcomeWithObject() throws Exception {
		mvc.perform(MockMvcRequestBuilders.get("/welcome-with-object").accept(MediaType.APPLICATION_JSON))
				.andExpect(status().isOk()).andExpect(content().string(containsString("Hello World")));
	}

	@Test
	public void welcomeWithParameter() throws Exception {
		mvc.perform(MockMvcRequestBuilders.get("/welcome-with-parameter/name/Buddy").accept(MediaType.APPLICATION_JSON))
				.andExpect(status().isOk()).andExpect(content().string(containsString("Hello World, Buddy")));
	}
}
```
\src\test\java\com\mastering\spring\springboot\controller\TodoControllerIT.java
```
package com.mastering.spring.springboot.controller;

import static org.hamcrest.Matchers.containsString;
import static org.junit.Assert.assertThat;

import java.net.URI;
import java.net.URL;
import java.util.Date;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.skyscreamer.jsonassert.JSONAssert;
import org.springframework.boot.context.embedded.LocalServerPort;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.web.client.RestTemplate;

import com.mastering.spring.springboot.Application;
import com.mastering.spring.springboot.bean.Todo;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class TodoControllerIT {

	@LocalServerPort
	private int port;

	private URL base;
	private RestTemplate template;

	@Before
	public void setUp() throws Exception {
		this.base = new URL("http://localhost:" + port);
		template = new RestTemplate();
	}

	@Test
	public void retrieveTodos() throws Exception {
		// When strict is set to false (recommended),
		// it forgives reordering data and extending results
		// (as long as all the expected elements are there),
		// making tests less brittle.
		String expected = "[" + "{id:1,user:Jack,desc:Learn Spring MVC,done:false}" + ","
				+ "{id:2,user:Jack,desc:Learn Struts,done:false}" + "]";

		ResponseEntity<String> response = template.getForEntity(base.toString() + "/users/Jack/todos", String.class);
		JSONAssert.assertEquals(expected, response.getBody(), false);
	}

	@Test
	public void retrieveTodo() throws Exception {
		String expected = "{id:1,user:Jack,desc:Learn Spring MVC,done:false}";
		// _links:{"self":{"href":"http://localhost:56283/users/Jack/todos"}}
		ResponseEntity<String> response = template.getForEntity(base.toString() + "/users/Jack/todos/1", String.class);
		JSONAssert.assertEquals(expected, response.getBody(), false);
	}

	@Test
	public void addTodo() throws Exception {
		Todo todo = new Todo(-1, "Jill", "Learn Hibernate", new Date(), false);

		URI location = template.postForLocation(base.toString() + "/users/Jill/todos", todo);
		assertThat(location.getPath(), containsString("/users/Jill/todos/4"));
	}

}
```
\src\test\java\com\mastering\spring\springboot\controller\TodoControllerTest.java
```
package com.mastering.spring.springboot.controller;

import static org.hamcrest.Matchers.containsString;
import static org.mockito.Matchers.any;
import static org.mockito.Matchers.anyBoolean;
import static org.mockito.Matchers.anyInt;
import static org.mockito.Matchers.anyString;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.header;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import java.util.Arrays;
import java.util.Date;
import java.util.List;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.skyscreamer.jsonassert.JSONAssert;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;

import com.mastering.spring.springboot.bean.Todo;
import com.mastering.spring.springboot.service.TodoService;

@RunWith(SpringRunner.class)
@WebMvcTest(TodoController.class)
public class TodoControllerTest {

	private static final int CREATED_TODO_ID = 4;

	@Autowired
	private MockMvc mvc;

	@MockBean
	private TodoService service;

	@Test
	public void retrieveTodos() throws Exception {
		List<Todo> mockList = Arrays.asList(
				new Todo(1, "Jack", "Learn Spring MVC",
						new Date(), false),
				new Todo(2, "Jack", "Learn Struts",
						new Date(), false));

		when(service.retrieveTodos(anyString()))
				.thenReturn(mockList);

		MvcResult result = mvc
				.perform(MockMvcRequestBuilders
						.get("/users/Jack/todos")
						.accept(MediaType.APPLICATION_JSON))
				.andExpect(status().isOk()).andReturn();

		String expected = "["
				+ "{id:1,user:Jack,desc:Learn Spring MVC,done:false}"
				+ ","
				+ "{id:2,user:Jack,desc:Learn Struts,done:false}"
				+ "]";

		JSONAssert.assertEquals(expected,
				result.getResponse().getContentAsString(),
				false);
	}

	@Test
	public void retrieveTodo() throws Exception {
		Todo mockTodo = new Todo(1, "Jack",
				"Learn Spring MVC", new Date(), false);

		when(service.retrieveTodo(anyInt()))
				.thenReturn(mockTodo);

		MvcResult result = mvc
				.perform(MockMvcRequestBuilders
						.get("/users/Jack/todos/1")
						.accept(MediaType.APPLICATION_JSON))
				.andExpect(status().isOk()).andReturn();

		String expected = "{id:1,user:Jack,desc:Learn Spring MVC,done:false}";

		JSONAssert.assertEquals(expected,
				result.getResponse().getContentAsString(),
				false);

	}

	@Test
	public void createTodo() throws Exception {
		Todo mockTodo = new Todo(CREATED_TODO_ID, "Jack",
				"Learn Spring MVC", new Date(), false);
		String todo = "{\"user\":\"Jack\",\"desc\":\"Learn Spring MVC\",\"done\":false}";
		when(service.addTodo(anyString(), anyString(),
				any(Date.class), anyBoolean()))
						.thenReturn(mockTodo);
		mvc.perform(MockMvcRequestBuilders
				.post("/users/Jack/todos").content(todo)
				.contentType(MediaType.APPLICATION_JSON))
				.andExpect(status().isCreated())
				.andExpect(header().string("location",
						containsString("/users/Jack/todos/"
								+ CREATED_TODO_ID)));
	}

	@Test
	public void createTodo_withValidationError()
			throws Exception {
		Todo mockTodo = new Todo(CREATED_TODO_ID, "Jack",
				"Learn Spring MVC", new Date(), false);
		String todo = "{\"user\":\"Jack\",\"desc\":\"Learn\",\"done\":false}";
		when(service.addTodo(anyString(), anyString(),
				any(Date.class), anyBoolean()))
						.thenReturn(mockTodo);
		MvcResult result = mvc
				.perform(MockMvcRequestBuilders
						.post("/users/Jack/todos")
						.content(todo).contentType(
								MediaType.APPLICATION_JSON))
				.andExpect(status().is4xxClientError())
				.andReturn();
		System.out.println(
				result.getResponse().getContentAsString());
	}
}
```
