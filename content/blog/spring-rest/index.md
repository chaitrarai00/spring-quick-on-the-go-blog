---
title: Spring REST
date: "2021-07-20T22:12:03.284Z"
description: "Spring REST"
---

*RE*presentational *S*tate *T*ransfer

_Java JSON Data Binding_
Process of converting Json Data to a Java POJO

Java offers Jackson for the Data Binding, Jackson will call appropriate getter/setter method of POJO internally for processing

##JSON to POJO Conversion

> Create Object Mapper

```java
ObjectMapper mapper= new ObjectMapper();
```

> read JSON and map/convert to POJO(done by Jackson on using methods it has)

```java
Student myStudent=mapper.readValue(new File("data/sample.json"),Student.class);
```

> You can access and see POJO are populated. Print and Check!!

```java
System.out.println("Student firstname: "+myStudent.getFirstname);
System.out.println("Student lastname: "+myStudent.getLastname);
```

##POJO to JSON Conversion

```java
ObjectMapper mapper=new ObjectMapper();
mapper.enable(SerializationFeature.INDENT_OUPUT)//just get an indented json output
mapper.writeValue(new File("data/output.json"),myStudent);
//json would be written to output.json
```

##Spring Jackson Support

> While using SPring REST to build application Spring automatically handles Jackson Integration

> JSON Data being passed to REST Controller is converted to POJO

> Java Object returned from REST Controller is converted to JSON

*If someone adds a new property to JSON and the business usecase is to not have issues while trying to get the POJO and not throw the exceoption i.e. not throw an exception: Ignore by @JsonIgnoreProperties(ignoreUnknown=true)*

```java
@JsonIgnoreProperties(ignoreUnknown=true)
public class Student {


	private int studentid;

	private String username;
	
	private List<Course> courses;
	public int getStudentid() {
		return studentid;
	}
	public void setStudentid(int studentid) {
		this.userid = studentid;
	}
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}

}
```

*@RestController is a Extension of Controller with request response support. Spring REST will convert Java POJOs to JSON as long as Jackson is in classpath*


```java
@RestController
@RequestMapping("/test")
public class HomeController{
      //other request mapping methods
      @RequestMapping(value="/hello",method=RequestMethod.GET)
      public String gethello(){
            Hello hello=new Hello("Draco");
            return Hello;
      }
 }
}
```

new Mapping can use GetMapping or Postmapping

```java
@RestController
@RequestMapping("/test")
public class HomeController{
      //other request mapping methods
      @GettMapping("/hello")
      public String gethello(){
            Hello hello=new Hello("Draco");
            return Hello;
      }
 }
}
```

*Using @Pathvariable binds the variable that comes in url with the method mapping*

```java
@RestController
@RequestMapping("/api")
public class HomeController{
  @GetMapping("/hello/{name}")
  public Student gethello(@PathVariable  String name){
    //..
  }
}
```
*Spring Rest Exception Handling*
##We will create a custome error response in JSON

>Create a custom error response class which will be sent back as JSON to client

```java
public class StudentErrorResponse{
  private int status;
  private String message;
  private long timestamp;
  //constructor
  //getters and setters
}
```

>Create a custom exception class: usecase StudentNotFoundException
```java
public class StudentNotFoundException extends RuntimeException{
  public StudentNotFoundException(String message){
    super(message);
  }
}
```

>Update REST service to throw exception accordingly
```java
@RestController
@RequestMapping("/api")
public class StudentRestController{
  @GetMapping("/students/{studentId}")
  public Student getStudent(@PathVariable int studentid){
    //check studentid against list
    if((studentid>=Studentlist.size) || (studentid<0))
    throw new StudentNotFoundException("Student id not Found "+studentid);
  }
}
```

>Add Exception Handler Method: define exception handler method with @ExceptionHandler annotation returning a ResponseEntity(wrapper for Http response object)

```java
@RestController
@RequestMapping("/api")
public class StudentRestController{
  @ExceptionHandler
  public ResponseEntity<StudentErrorResponse> handleException(StudentNotFoundException exp){
    StudentErrorResponse error= new StudentErrorResponse();
    error.setStatus(HttpStatus.NOT_FOUND.value());
    error.setMessage(exp.getMessage());
    error.setTimeStamp(System.currentTimeMillis());
    return ResponseEntity<>(error,HttpStatus.NOT_FOUND);
  }
}
```

>@ExceptionHandler has the method defined returning a ResponseEntity of type of Response StudentErrorResponse and setting respective values for handling the exception StudentNotFoundException inside it

>@ExceptionHandler is only for specific controller and cannot be used by other controllers so we might need a global exception handlers: @ControllerAdvice

>@ControllerAdvice is a real time use of AOP

```java
@ControllerAdvice
public class ExceptionHandlingClass {
	
	@ExceptionHandler
	public String exceptionHandler(NullPointerException exp) {
	ErrorResponse error= new ErrorResponse();
    error.setStatus(HttpStatus.NOT_FOUND.value());
    error.setMessage(exp.getMessage());
    error.setTimeStamp(System.currentTimeMillis());
    return ResponseEntity<>(error,HttpStatus.NOT_FOUND);
	}
}
```
