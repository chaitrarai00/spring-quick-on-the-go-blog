---
title: Spring JDBC
date: "2021-03-28T22:12:03.284Z"
description: "Spring JDBC"
---

Spring JDBC
Spring Jdbc is a mechanism to connect to database and execute queries using spring jdbc modules.
Spring jdbc module:
• JdbcTemplate: this consists of methods which consists of various database operations that a developer might need to use. Handles closing and opening connections

• Datasource: this is an interface that provides information regarding the database to Jdbctemplate like url, username, driver etc(classes that implement DriverManagerDataSource )
![Spring JDBC Template](./SpringJDBCTemplate.png)
Query methods:
• Execute: select
• Execute update: insert, update, delete
Add dependencies in pom file:

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.15</version>
</dependency>
```

Note :Change dependencies based on your configuration needs

Selecting data using Spring JDBC:

```java
public T queryForObject(String sql, RowMapper<T> rowMapper, Object args)
public List<T> query(String sql, RowMapper<T> rowMapper)
```

RowMapper is an Interface that lets you converts resultset to object of entity form ( no need of a resultSet) and create a user implementation using maprow() method

Include jdbc template and DAO classes either by Autoriiring or xml file:
If one uses xml files mention property > with ref attribute in config.xml the other autoring has to be done in create a config class with @configuration asnd @bean and return driverMangareDataSOurce type, and set drivercclassname url password etc, Bean for JdbcTemplate to get template object
To summarize in xml configure by <bean> and set by <property> and ref attritube and name for values and for annotation use @bean with respective setters
I personally recommend using annotation because I feel they are easier and more usr dev friendly (this is just my opinion)
correctrtion autoriwing can be done with @Component and @Autowired andf annotation based with config class for tAutowiring use or mention @ComponentScan for reference in autowiring

Note: DOA classes also needs to be configured in config files(class or xml)

{
WHEN CREATING H2 DATABASE CREATE SCHEMA.SQL FOR CREATION AND DATA.SQL FOR UPDATION ALSO MENTION APPLICATION.PROPERTIES FILE
#enable h2 console
spring.h2.console.enabled=true
#mention the platform you are working with
spring.datasource.platform=h2
#specify the url
spring.datasource.url=jdbc:h2:mem:candystore
}

```java
public interface EmployeeDAO {

	//create
	public void save(Employee employee);

	//retrieve/read
	public Employee get(int id);

	//update
	public void update(Employee employee);

	//delete object by accessing via id
	public void deletebyid(int id);

	//access all objects
	public List<Employee> getAll();
}

```

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

import javax.sql.DataSource;

public class EmployeeDAOImpl implements EmployeeDAO{
	private DataSource datasource;

	public void setDatasource(DataSource datasource) {
		this.datasource=datasource;
	}

	@Override
	public void save(Employee employee) {
		String query="insert into Employee (id,name,role) values (?,?,?)";
		Connection con=null;
		PreparedStatement ps=null;
		try {
			con=datasource.getConnection();
			ps=con.prepareStatement(query);
			ps.setInt(1, employee.getId());
			ps.setString(2, employee.getName());
			ps.setNString(3, employee.getRole());
			int out=ps.executeUpdate();
			if(out!=0) {
				System.out.println("Employee saved with id"+employee.getId());
			}
			else {
				System.out.println("Employee failed to be saved with id"+employee.getId());
			}
		}catch(SQLException e) {
			e.printStackTrace();
		}
		try {
			ps.close();
			con.close();
		}catch(SQLException e) {
			e.printStackTrace();
		}
	}

	@Override
	public Employee get(int id) {
		String query="select name, role from Employee where id = ?";
		Employee emp=null;
		Connection con=null;
		PreparedStatement ps=null;
		ResultSet rs=null;
		try {
			con=datasource.getConnection();
			ps=con.prepareStatement(query);
			ps.setInt(1, id);
			rs=ps.executeQuery();
			if(rs.next()) {
				emp=new Employee();
				emp.setId(id);
				emp.setName(rs.getString("name"));
				emp.setRole(rs.getNString("role"));
				System.out.println("employee found"+emp);
			}
			else {
				System.out.println("Employee not found");
			}
		}catch(SQLException e) {
			e.printStackTrace();
		}
		try {
			rs.close();
			ps.close();
			con.close();
		}
		catch(SQLException e) {
			e.printStackTrace();
		}
		return emp;
	}

	@Override
	public void update(Employee employee) {
		String query="Update Employee set name=?,role=? where id=?";
		Connection con=null;
		PreparedStatement ps=null;
		try {
			con=datasource.getConnection();
			ps=con.prepareStatement(query);
			ps.setString(1, employee.getName());
			ps.setString(2, employee.getRole());
			ps.setInt(3, employee.getId());
			int out=ps.executeUpdate();
			if(out!=0) {
				System.out.println("Employee Updated with id:"+employee.getId());
			}
			else {
				System.out.println("Employee cound not be Updated with id:"+employee.getId());
			}
		}catch(SQLException e) {
		e.printStackTrace();
		}
		try {
			ps.close();
			con.close();
		}catch(SQLException e) {
			e.printStackTrace();
		}
	}

	@Override
	public void deletebyid(int id) {
		String query="delete from employee where id=?";
		PreparedStatement ps=null;
		Connection con=null;
		try {
			con=datasource.getConnection();
			ps=con.prepareStatement(query);
			ps.setInt(1, id);
			int out=ps.executeUpdate();
			if(out!=0) {
				System.out.println("Employee Deleted with id:"+id);
			}
			else {
				System.out.println("Employee could not be deleted with id:"+id);
			}
		}catch (SQLException e) {
			e.printStackTrace();
		}
		try {
			ps.close();
			con.close();
		}catch(SQLException e) {
			e.printStackTrace();
		}
	}

	@Override
	public List<Employee> getAll() {
		String query="select name,role,id from Employee";
		ArrayList<Employee> emp_list=new ArrayList<Employee>();
		Connection con=null;
		PreparedStatement ps=null;
		ResultSet rs=null;
		try {
			con=datasource.getConnection();
			ps=con.prepareStatement(query);
			rs=ps.executeQuery();
			while(rs.next()) {
				Employee e=new Employee();
				e.setId(rs.getInt("id"));
				e.setName(rs.getString("name"));
				e.setRole(rs.getNString("role"));
				emp_list.add(e);
			}
		}catch (SQLException e) {
			e.printStackTrace();
		}
		try {
			rs.close();
			ps.close();
			con.close();
		}catch(SQLException e) {
			e.printStackTrace();
		}
		return emp_list;
	}

}
```

_Spring Batch_

```java

import java.util.Date;

import javax.xml.bind.annotation.XmlAttribute;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement
public class Report {
	private int id;
	private String firstname;
	private String lastname;
	private Date Date;
	@XmlAttribute(name="id")
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	@XmlElement(name="firstname")
	public String getFirstname() {
		return firstname;
	}
	public void setFirstname(String firstname) {
		this.firstname = firstname;
	}
	@XmlElement(name="lastname")
	public String getLastname() {
		return lastname;
	}
	public void setLastname(String lastname) {
		this.lastname = lastname;
	}
	@XmlElement(name="dob")
	public Date getDate() {
		return Date;
	}
	public void setDate(Date date) {
		Date = date;
	}
	@Override
	public String toString() {
		return "Report [id=" + id + ", firstname=" + firstname + ", lastname=" + lastname + ", Date=" + Date + "]";
	}

}
```

reports.csv

```text
1001,Tom,Moody, 29/7/2013
1002,John,Parker, 30/7/2013
1003,Henry,Williams, 31/7/2013
1004,HARRY,POTTER, 1/8/2013
```

application.properties

```text
file.input=report.csv
```

```java
import javax.sql.DataSource;

import org.springframework.batch.core.Job;
import org.springframework.batch.core.Step;
import org.springframework.batch.core.configuration.annotation.EnableBatchProcessing;
import org.springframework.batch.core.configuration.annotation.JobBuilderFactory;
import org.springframework.batch.core.configuration.annotation.StepBuilderFactory;
import org.springframework.batch.core.launch.support.RunIdIncrementer;
import org.springframework.batch.item.database.BeanPropertyItemSqlParameterSourceProvider;
import org.springframework.batch.item.database.JdbcBatchItemWriter;
import org.springframework.batch.item.database.builder.JdbcBatchItemWriterBuilder;
import org.springframework.batch.item.file.FlatFileItemReader;
import org.springframework.batch.item.file.builder.FlatFileItemReaderBuilder;
import org.springframework.batch.item.file.mapping.BeanWrapperFieldSetMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.ClassPathResource;

@Configuration
@EnableBatchProcessing
public class BatchConfiguration {
	@Autowired
	public JobBuilderFactory jobBuilderFactory;
	//to define the jobs
	@Autowired
	public StepBuilderFactory stepBuilderFactory;
	//to define the steps
	@Value("${file.input}")
	private String fileinput;
	/*
	 * Defining Writer Reader and processors
	 */
	@Bean
	public FlatFileItemReader<Report> reader(){
		return new FlatFileItemReaderBuilder<Report>().
				name("reportItemReader")
				.resource(new ClassPathResource("report.csv"))
				.delimited()
				.names(new String[] {"firstname","lastname"})
				.fieldSetMapper(new BeanWrapperFieldSetMapper<Report>() {
			{
				setTargetType(Report.class);
			}
		}).build();
	}

	@Bean
	public ReportItemProcessor processor() {
		return new ReportItemProcessor();
	}

	@Bean
	public JdbcBatchItemWriter<Report> writer(DataSource datasource){
		return new JdbcBatchItemWriterBuilder<Report>()
				.itemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<Report>())
				.sql("INSERT INTO report (first_name, last_name) VALUES (:firstname, :lastname)")
				.dataSource(datasource)
				.build();
	}
	/*
	 * Job Configuration
	 * define job and each steps
	 */
	@Bean
	public Job importuserJob(JobCompletionNotificationListener listener, Step step1) {
		return jobBuilderFactory.get("importuserJob")
				.incrementer(new RunIdIncrementer())
				.listener(listener)
				.flow(step1)
				.end()
				.build();
	}

	@Bean
	public Step step1(JdbcBatchItemWriter<Report> writer) {
		return stepBuilderFactory.get("step1")
				.<Report,Report>chunk(10)
				.reader(reader())
				.processor(processor())
				.writer(writer)
				.build();
	}


}
```
