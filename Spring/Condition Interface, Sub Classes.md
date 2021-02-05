2021.01.12

### Condition Interface, Sub Classes

해당 어노테이션들의 동작도 ConfigurationClassParser를 통해 이루어진다.

1. ConfigurationClassParser 의 process 메서드 호출 
2. 추가가 될 수 있는 모든 그룹(**AutoConfiguration.class**)에 대한 정보를 저장해온다.
3. processGroupImports()가 호출되고 앞서 저장된 정보들을 순회하면서 각각의 ConfigurationClass 정보를 가져온 뒤 processImports() 메서드를 호출한다.
4. 저장된 정보들은 Configuration Class들 이기 때문에 processConfigurationClass() 를 호출한다.
5. 최상단에 shouldSkip 메서드를 호출한 뒤 몇몇 조건을 검증한다.
    1. MetaData가 Null 이거나 Conditional class 유형의 어노테이션이 없는 경우 

        → 아무것도 하지않고 false를 Return 한다.

    2. ConfigurationPhase phase가 Null 인 경우

        → 해당 MetaData가 AnnotationMetadata이고 Annotation이라면, 혹은 Method 단위에 Annotation이 붙어있다면 shouldSkip 메서드를 호출하며 ConfigurationPhase를 PARSE_CONFIGURATION로 설정한다.

        → 위의 조건이 아니라 Interface이거나 메서드 단위의 Annotation이 작성되지 않았다면 shouldSkip 메서드를 호출하며 REGISTER_BEAN로 설정한다.

        **ConfigurationPhase 의 속성 정보**

        - **PARSE_CONFIGURATION**

            @Configuration Annotation을 파싱할 때 작성된 조건식을 평가한다.

        - **REGISTER_BEAN**

            ConfigurationClassBeanDefinitionReader이 Bean을 등록할 때 조건식을 평가한다.

6. 이후 MetaData 정보에서 Condition 정보가 존재하는지 확인하고 리스트에 저장한다.
7. 저장된 정보를 순회하며, ConfigurationPhase requiredPhase 만든 뒤 조건을 검증한다.
    1. requiredPhase 가 Null 이거나 (||) 메서드를 호출될 때 넘겨받은 phase 속성 값과 같고 condition 검증 조건이 false인 경우 true를 반환한다.

        → true를 전달받은 경우 5번 절차에 조건식을 만족함으로 메서드 실행이 종료된다.

    이때 호출되는 matches 메서드가 Condition 인터페이스를 구현한 SpringBootCondition의 matches 메서드이다.

    해당 메서드는 ConditionContext 객체와 AnnotationType의 Metadata를 넘기면서 하위 어노테이션들의 getMatchOutcome()을 호출한다.

    → 해당 메서드는 정해진 조건식을 검증하고 그에 따른 결과로 boolean 값을 전달한다.

<br/>

### **Auto-Configuration Conditions**

주어지는 여러가지 조건들을 이용하여 Spring Application 실행시 사용할 Bean Definition을 선택할 수 있도록하는 Annotation들의 집합이다. 

- **@ConditionalOnClass**

    어노테이션의 인자로 받은 클래스가 존재할 때, 해당 주석이 적용된 Configuration class의 Java기반 Bean Definition들을 등록한다.

- **@ConditionalOnMissingClass**

    어노테이션의 인자로 받은 클래스가 존재하지 않을 때, 해당 주석이 적용된 Configuration class의 Java기반 Bean Definition들을 등록한다.

- **@ConditionalOnBean**

    어노테이션의 인자로 받은 Bean ID가 존재할 때 해당 주석이 적용된 Configuration class의 Java기반 Bean Definition들을 등록한다.

- **@ConditionalOnMissingBean**

    어노테이션의 인자로 받은 Bean ID가 존재하지 않을 때 해당 주석이 적용된Configuration class의 Java기반 Bean Definition들을 등록한다.

- **@ConditionalOnProperty**

    [application.properties](http://application.properties) 구성 정보와 값에 따라 특정한 Bean을 등록하는데 사용된다.

    *ex) Prod 용 DataSource, Test 용 DataSource 등*

    - **value**

        속성의 이름을 나타내는 필드이다. (ex) lob)

    - **prefix**

        각 속성에 적용되는 접두사를 정의한다. 기본 값이 지정되지 않으면 .을 포함한다.

    - **name**

        검증될 속성의 이름을 나타낸다. (prifix + value ex) : .lob)

    - **havingValue**

        해당 속성에 대한 예상 값을 나타내며, 값이 지정된 경우 다른 값이여야 한다. 기본적으로 빈 문자열 로 정의된다.

    - **matchIfMissing**

        해당 속성이 설정되지 않은 경우 정의한 조건을 사용할지에 대해 정의한다. 기본적으로 false로 정의되어 있다.

- **@ConditionalOnResource**

    특정한 리소스가 존재할 때 해당 Class의 Definition을 사용하게 한다.

    ex) (resources = "classpath:application.properties")

- **@ConditionalOnWebApplication**

    현재 Application이 Web Application이라면 해당 Definition을 사용한다.

- **@ConditionalOnNotWebApplication**

    현재 Application이 Web Application이 아니라면 해당 Definition을 사용한다.

- **@ConditionalExpression**

    SpEL 표현식을 통해 작성된 검증로직이 true인 경우 해당 Definition을 사용한다.

- **@Conditional**

    Custom된 지정 조건 Class를 만든 뒤 해당 어노테이션에 인자로 넘긴 뒤 true인 경우 해당 Definition을 사용한다.



**참고자료**

- [https://www.baeldung.com/spring-conditionalonproperty](https://www.baeldung.com/spring-conditionalonproperty)
- [https://www.baeldung.com/spring-boot-custom-auto-configuration](https://www.baeldung.com/spring-boot-custom-auto-configuration)