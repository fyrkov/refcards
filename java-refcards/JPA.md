Main doc: https://en.wikibooks.org/wiki/Java_Persistence

# JPA-cheatsheet

#### Entity state transitions in JPA:
![1_entity_2_tables](JPA_files/jpaentitystates.png)

#### 1. Creating a JPA entity
```java
@Entity
@Table(name="EMPLOYEE") // this may be omitted if the class name is the same as the table name
public class Employee {
    ...
}
```
Sometimes an entity is stored in several tables:

![1_entity_2_tables](JPA_files/JPA_entity_in_2_tables.png)
```java
@Entity
@Table(name="EMPLOYEE")
@SecondaryTable(name="EMP_DATA",
                // may be omitted if both tables have "id"
                pkJoinColumns = @PrimaryKeyJoinColumn(name="EMP_ID", referencedColumnName="ID"))
public class Employee {
    ...
}
```

#### 2. @Basic
A basic attribute for simple types such as `String, Number, Date` or a primitive. The following table summarizes the basic types and the database types they map to:

Java type | Database type
--------- | -------------
String (char, char[]) | VARCHAR (CHAR, VARCHAR2, CLOB, TEXT)
Number (BigDecimal, BigInteger, Integer, Double, Long, Float, Short, Byte), int, long, float, double, short, byte	|NUMERIC (NUMBER, INT, LONG, FLOAT, DOUBLE)
boolean (Boolean)	| BOOLEAN (BIT, SMALLINT, INT, NUMBER)
java.util.Date	| TIMESTAMP (DATE, DATETIME)
java.sql.Date	| DATE (TIMESTAMP, DATETIME)
java.sql.Time	| TIME (TIMESTAMP, DATETIME)
java.sql.Timestamp	| TIMESTAMP (DATETIME, DATE)
java.lang.Enum	| NUMERIC (VARCHAR, CHAR)

Any un-mapped field will be automatically mapped as `@basic` and column name defaulted.

By default all Basic mappings are `EAGER`, though `fetch` attribute can be set on a Basic mapping to use `LAZY` fetching:
`@Basic(fetch=FetchType.LAZY)`.

A Basic attribute can have `optional` which defines whether the value of the field may be null. By default everything is assumed to be optional, except for an `@Id`.

`@Basic(optional = false)` vs `@Column(nullable = false)` difference:
The definition of `optional` talks about property and field values and suggests that this feature should be evaluated within the runtime. `nullable` is only in reference to database columns.

#### 3. @Column
Annotation is used to specify a mapped column for a persistent property or field. If no `@Column` is specified, the default values are applied.

There are various attributes on the `@Column` annotation:
```java
@Column(name="SSN", unique=true, nullable=false, description="description")
private long ssn;
@Column(name="F_NAME", length=100)
private String firstName;
@Column(name="SALARY", scale=10, precision=2)
private BigDecimal salary;       
@Column(name="S_TIME", columnDefinition="TIMESTAMPTZ")
private Calendar startTime;
```
`@Column` defines `insertable` and `updatable` options. These allow for this column to be omitted from the SQL INSERT or UPDATE statement. Setting both insertable and updatable to false, effectively mark the attribute as read-only.

#### 4. @Transient
JPA's `@Transient` annotation is used to indicate that a field is not to be persisted in the database:
```java
@Entity
public class Employee {
  // Non-persistent fields must be marked as transient.
  @Transient
  private EmployeeService service;
  ...
}
```

#### 5. @Enumerated
By default type Enum will be stored as a Basic to the db, using the integer Enum values as codes (i.e. 0, 1). JPA also defines an `@Enumerated` annotation to define an Enum attribute. This can be used to store the Enum as the STRING value of its name (i.e. "MALE", "FEMALE"):
```java
public enum Gender {
    MALE,
    FEMALE
}

@Entity
public class Employee {
    ...
    @Basic
    @Enumerated(EnumType.STRING)
    private Gender gender;
    ...
}
```

#### 6. ID generation
JPA provides several strategies for id generation: 
- TABLE uses sequence table in db 
- SEQUENCE special database objects to generate ids. Supported in Oracle and Postgres.
- IDENTITY uses special `IDENTITY` columns in db. Identity sequencing requires the insert to occur before the id can be assigned, so it is not assigned on persist like other types of sequencing. One must either call `commit()` on the current transaction, or call `flush()` on the EntityManager.
```java
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
```
#### 7. Entity relationships
Possible types of associations are:
- `OneToOne` - unique reference from one object to another
- `ManyToOne` - reference from one object to another
- `OneToMany` - Collection or Map of objects, inverse of a ManyToOne
- `ManyToMany` - Collection or Map of objects
- `Embedded` - reference to a object that shares the same table of the parent
- `ElementCollection` - Collection or Map of Basic or Embeddable objects, stored in a separate table

#### 8. Fetching
Lazy fetching allows to defer the fetching of a relationship until it is accessed.
The default fetch type is `LAZY` for all relationships except for `OneToOne` and `ManyToOne`.
```java
@OneToOne(fetch=FetchType.LAZY)
```
For collection relationships sending `size()` is normally the best way to ensure a lazy relationship is instantiated. For `OneToOne` and `ManyToOne` normally just accessing the relationship is enough (i.e. `employee.getAddress()`).
Another solution is to use the JPQL `JOIN FETCH` for the relationship when querying the objects.

#### 9. Cascading
Relationships can have a `cascade` option that allows to model dependent relationships such as `Order` -> `OrderLine`. Cascading such relationship allows for the Order's -> OrderLines to be persisted, removed, merged along with their parent.
The following operations can be cascaded, as defined in the `CascadeType` enum:
- `PERSIST` - Cascaded the `EntityManager.persist()` operation. If `persist()` is called on the parent, and the child is also new, it will also be persisted. If it is existing, nothing will occur, although calling `persist()` on an existing object will still cascade the persist operation to its dependents.
- `REMOVE` - If `EntityManager.remove()` is called on the parent then the child will also be removed. This should only be used for dependent relationships. If one removes a dependent object from a `OneToMany` collection it will not be deleted, JPA requires that you explicitly call `remove()` on it.
- `MERGE` - Cascaded the `EntityManager.merge()` operation.
- `REFRESH` - Cascaded the `EntityManager.refresh()` operation. Be careful enabling this for all relationships, as it could cause changes made to other objects to be reset.
- `ALL` - Cascaded all the above operations.
```java
@Entity
public class Employee {
  ...
  @OneToOne(cascade={CascadeType.ALL})
  @JoinColumn(name="ADDR_ID")
  private Address address;
  ...
}
```
#### 10. Maps
JPA allows a Map to be used for any collection mapping including `OneToMany`, `ManyToMany` and `ElementCollection`.
`@MapKeyColumn` annotation is used to define a map relationship where the key is a Basic value, the `@MapKeyJoinColumn` annotation is used to define a map relationship where the key is an Entity value. There are also `@MapKeyJoinColumns`, `@MapKeyEnumerated` and `@MapKeyTemporal`.

![JPA_map_example.png](JPA_files/JPA_map_example.png)

```java
@Entity
public class Employee {
  ...
  @OneToMany(mappedBy="owner")
  @MapKeyColumn(name="PHONE_TYPE")
  private Map<String, Phone> phones;
  ...
}
```

#### 11. Join fetch
Join fetching is a query optimization technique for reading multiple objects in a single database query. It involves joining the two object's tables in SQL and selecting both object's data.

Join fetching is one solution to the ORM n+1 problem. The issue is if you select n Employee objects, and access each of their addresses, in basic ORM you will get 1 select for the Employee objects, and then n selects, one for each Address object. Join fetching solves this issue by only requiring one select, and selecting both the Employee and its Address.

JPA supports join fetching through JPQL using the `JOIN FETCH` syntax:\
`SELECT emp FROM Employee emp JOIN FETCH emp.address`

If your relationship allows null or an empty collection, then you can use outer join:\
`SELECT emp FROM Employee emp LEFT JOIN FETCH emp.address`

#### 12. OneToOne
In `OneToOne` relationship a foreign key may either be in the master object's table or the dependant object's table. If the fk is in the dependant object's table JPA requires that the relationship be bi-directional (must be defined in both objects), and the master object must use the `mappedBy` attribute to define the mapping.
A `OneToOne` relationship typically requires a `@JoinColumn`:
```java
@Entity
public class Employee {
  ...
  @OneToOne(fetch=FetchType.LAZY)
  @JoinColumn(name="ADDRESS_ID")
  private Address address;
}

@Entity
public class Address {
  ...
  @OneToOne(fetch=FetchType.LAZY, mappedBy="address")
  private Employee owner;
  ...
}
```
In some data models, you may have a `OneToOne` relationship defined through a join table
```java
  @OneToOne(fetch=FetchType.LAZY)
  @JoinTable(
      name="EMP_ADD",
      joinColumns=
        @JoinColumn(name="EMP_ID", referencedColumnName="EMP_ID"),
      inverseJoinColumns=
        @JoinColumn(name="ADDR_ID", referencedColumnName="ADDRESS_ID"))
  private Address address;
  ...
```

It is also possible to use dependant object's PK as FK with `@MapsId`. Then in this case master and dependant have equal PKs:
```java
public class EmployeeContractEnd {

    @Id
    @Type(type="pg-uuid")
    private UUID id;

    @OneToOne
    @JoinColumn(name = "id")
    @MapsId
    private Employee employee;
```
  
#### 13. ManyToOne and OneToMany
In JPA a `ManyToOne` is specified through the `@ManyToOne` annotation and it is typically accompanied by a `@JoinColumn`which defines the name of the foreign key column.
 
If the reverse `OneToMany` relationship is specified in the target object, then the `@OneToMany` annotation in the target object must contain a `mappedBy` attribute to define this inverse relation.
```java
@Entity
public class Phone {
  ...
  @ManyToOne(fetch=FetchType.LAZY)
  @JoinColumn(name="OWNER_ID")
  private Employee owner;
  ...
}

@Entity
public class Employee {
  ...
  @OneToMany(mappedBy = "owner")
  private List<Phone> phones;
  ...
  ```
  
#### 14. ManyToMany
All ManyToMany relationships require a `@JoinTable` annotation. The JoinTable defines a foreign key to the source object's primary key (joinColumns), and a foreign key to the target object's primary key (inverseJoinColumns). Normally the primary key of the JoinTable is the combination of both foreign keys.
```java
@Entity
public class Employee {
 ...
 @ManyToMany
 @JoinTable(
   name="EMP_PROJ",
   joinColumns=@JoinColumn(name="EMP_ID", referencedColumnName="ID"),
   inverseJoinColumns=@JoinColumn(name="PROJ_ID", referencedColumnName="ID"))
 private List<Project> projects;
 .....
}
```
To make a Bi-directional ManyToMany one direction must be defined as the owner and the other must use the `mappedBy` attribute to define its mapping:
```java
public class Project {
 ...
 @ManyToMany(mappedBy="projects")
 private List<Employee> employees;
 ...
}
```

#### 15. Embeddable objects
In JPA a relationship where the target object's data is embedded in the source object's table is considered an embedded relationship, and the target object is considered an Embeddable object. Embeddable objects have different requirements and restrictions than Entity objects and are defined by the `@Embeddable` annotation.

An embeddable object cannot be directly persisted, or queried, it can only be persisted or queried in the context of the source object in which it is embedded. An embeddable object does not have an id or table.

Relationships to embeddable objects are defined through the `@Embedded` annotation.
```java
@Embeddable 
public class EmploymentPeriod {
  @Column(name="START_DATE")
  private java.sql.Date startDate;

  @Column(name="END_DATE")
  private java.sql.Date endDate;
  ....
}

@Entity
public class Employee {
  @Id
  private long id;
  ...
  @Embedded
  private EmploymentPeriod period;
  ...
}
```

#### 16. ElementCollection
An `ElementCollection` can be used to define a one-to-many relationship to an `Embeddable` object, or a `Basic` value (such as a collection of Strings).
In JPA an ElementCollection relationship is defined through the `@ElementCollection` annotation.

The ElementCollection values are always stored in a separate table. The table is defined through the `@CollectionTable` annotation. The CollectionTable defines the table's name and `@JoinColumn`.

There is no cascade option on an ElementCollection, the target objects are always persisted, merged, removed with their parent. ElementCollection still can use a fetch type and defaults to LAZY the same as other collection mappings.

```java
public class Employee {
  ...
  @ElementCollection
  @CollectionTable(
        name="PHONE",
        joinColumns=@JoinColumn(name="OWNER_ID")
  )
  @Column(name="PHONE_NUMBER")
  private List<String> phones;
  ...
}
```

#### 17. JPQL
JPQL is similar to SQL, but operates on objects, attributes and relationships instead of tables and columns. JPQL can be used for reading (SELECT), as well as bulk updates (UPDATE) and deletes (DELETE).

A JOIN clause can also be used in the FROM clause:\
`SELECT e FROM Employee e JOIN e.address a WHERE a.city = :city`\
or using ON in JPA 2.1:\
`SELECT e FROM Employee e LEFT JOIN e.address a ON a.city = :city`

The FETCH option can be used on a JOIN to fetch the related objects in a single query:\
`SELECT e FROM Employee e JOIN FETCH e.address`

By default all joins in JPQL are INNER joins. A join can be defined as an OUTER join using the LEFT options:\
`SELECT e FROM Employee e LEFT JOIN e.address a ORDER BY a.city`

JPA does not support sub-selects in the FROM clause.

ORDER BY allows the ordering of the results to be specified:\
`SELECT e FROM Employee e ORDER BY e.lastName ASC`

Aggregation functions include MIN, MAX, AVG, SUM, COUNT.

GROUP BY allows for summary information to be computed on a set of objects. GROUP BY is normally used in conjunction with aggregation functions:\
`SELECT e, COUNT(p) FROM Employee e LEFT JOIN e.projects p GROUP BY e`

The HAVING clause allows for the results of a GROUP BY to be filtered:\
`SELECT AVG(e.salary), e.address.city FROM Employee e GROUP BY e.address.city HAVING AVG(e.salary) > 100000`

JPA does not support the SQL UNION.

Possible expressions in WHERE clause:
- e.firstName = 'Bob'
- e.salary < 100000
- e.salary > :sal
- e.salary <= 100000
- e.salary >= :sal
- e.firstName LIKE 'A%'
- e.firstName BETWEEN 'A' AND 'C'
- e.endDate IS NULL
- e.firstName IN ('Bob', 'Fred', 'Joe')

The IN operation allows for a list of values or parameters, a single list parameter, or a sub-select:\
`e.firstName = (SELECT e2.firstName from Employee e2 WHERE e2.id = :id)`

JPA defines named parameters, and positional parameters:
```java
Query query = em.createQuery("SELECT e FROM Employee e WHERE e.firstName = :first");
query.setParameter("first", "Bob");
...
Query query = em.createQuery("SELECT e FROM Employee e WHERE e.firstName = ?");
query.setParameter(1, "Bob");
```

JPQL supported functions:
- ABS absolute value, e.g. `ABS(e.salary - e.manager.salary)`
- CASE defines a case statement, e.g. `CASE e.status WHEN 0 THEN 'active' WHEN 1 THEN 'consultant' ELSE 'unknown' END`
- COALESCE evaluates to the first non null argument value, e.g. `COALESCE(e.salary, 0)`
- CONCAT concatenates two or more string values, e.g. `CONCAT(e.firstName, ' ', e.lastName)`
- CURRENT_DATE the current date on the database	
- CURRENT_TIME the current time on the database	
- CURRENT_TIMESTAMP the current date-time on the database	
- LENGTH the character/byte length of the character or binary value, e.g. `LENGTH(e.lastName)`
- LOWER	convert the string value to lower case	
- SUBSTRING	the substring from the string, starting at the index, optionally with the substring size, e.g. `SUBSTRING(e.lastName, 0, 2)`
- UPPER	convert the string value to upper case	

#### 17. @Transactional

`@Transactional` on a method makes all the code in this method be executed in one transaction.
If an exceptions happens inside this code, then everything is rolled back.

Enabling TX annotations support:
```java
@EnableTransactionManagement
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
...
@Service
public class Service {

  @Transactional
  public void method() {

  }
}

```
Tips:
- `@EnableTransactionManagement` enables processing of `@Transactional` annotation
- :exclamation: By default processing is done with "Spring AOP Proxies", i.e. `@Transactional` will work only if a method called as method of some `@Component`, and will not work if is called internally, i.e. from this class where he is defined.
- :exclamation: Another limitation that comes form the previous one - `@Transactional` method can not be private
- It is possible to switch the processing of the `@Transactional` annotaions to "AspectJ" mode. This will enable "Byte code weaving" instead of "Spring AOP Proxies" and overvome the limitations 2) and 3)


By default, CRUD methods on repositories derived from `SimpleJpaRepository` are `@Transactional`.\
For read operations `readOnly=true` is set.\
But `@Transactional` is also required for modifying custom query repository methods, e.g.
```java
@Transactional(readOnly = true)
public interface UserRepository extends JpaRepository<User, Long> {

  @Modifying
  @Transactional
  @Query("delete from User u where u.active = false")
  void deleteInactiveUsers();
}
```

Mind also `@Modifying` here that which marks method as modifying and is only relevant in combination with the `@Query`.

#### Transactional settings

TBD
e.g. Propagations, Isolations
#### Manual tx management

TBD


#### Entity Manager

`entityManager.flush();`

`entityManager.clear();`

#### JPA Repositories

`repo.save()`

`repo.saveAndFlush()` - immediately attemmpts to commit changes to DB