# datus——一个流畅的 java 数据映射库

> 原文：<https://dev.to/roookeee/datus-a-fluent-java-data-mapping-library-2l3c>

[datus](https://github.com/roookeee/datus) 让您使用易于使用的 fluent builder 系统定义数据转换，以处理额外的处理或边缘情况。不再有易变的锅炉板工厂类:

```
class Person {
    //getters + setters omitted for brevity
    private String firstName;
    private String lastName;
}

class PersonDTO {
    //getters + setters + empty constructor omitted for brevity
    private String firstName;
    private String lastName;
}

//the mutable API defines a mapping process by multiple getter-setter steps
Mapper<Person, PersonDTO> mapper = Datus.forTypes(Person.class, PersonDTO.class)
    .mutable(PersonDTO::new)
    .from(Person::getFirstName).into(PersonDTO.setFirstName)
    .from(Person::getLastName)
        .given(Objects::nonNull, ln -> ln.toUpperCase()).orElse("fallback")
        .into(PersonDTO::setLastName)
    .from(/*...*/).into(/*...*/)
    .build(); 
```

和不可变类型:

```
class Person {
    //getters + constructor omitted for brevity
    private final String firstName;
    private final String lastName;

}

class PersonDTO {
    //getters omitted for brevity
    private final String firstName;
    private final String lastName;

    public PersonDTO(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}

//define a building process for each constructor parameter, step by step
//the immutable API defines constructor parameters in their declaration order
Mapper<Person, PersonDTO> mapper = Datus.forTypes(Person.class, PersonDTO.class)
    .immutable(PersonDTO::new)
    .from(Person::getFirstName).to(ConstructorParameter::bind)
    .from(Person::getLastName)
        .given(Objects::nonNull, ln -> ln.toUpperCase()).orElse("fallback")
        .to(ConstructorParameter::bind)
    .build(); 
```

易于与 Spring 等集成。-在 20 分钟内阅读完[完整的使用指南](https://github.com/roookeee/datus/blob/master/USAGE.md)。