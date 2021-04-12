---
title: "Spring Boot 자동 환경 설정"
date: 2021-04-12T11:26:38+09:00
categories:
    - Spring
tags:
    - Spring Boot
    - AutoConfiguration 
url: /2021/04/12/spring-boot-autoconfigure
featuredImage: /images/spring-boot.png
images:
    - /images/spring-boot.png
---
Spring Boot 자동 설정은 Web, H2, JDBC 등 100여 개의 자동 설정을 제공한다.
새로 추가되는 라이브러리(JAR)는 Spring Boot 자동 설정 의존성을 따라서 설정이 자동 적용된다.

자동 설정은 @EnableAutoConfiguration or @SpringBootApplication 중 하나를 사용하면 된다.
`@EnableAutoConfiguration 사용 시 @Configuration와 같이 사용해야 한다.`

Spring Boot Version은 2.4.4 버전이다.

## @SpringBootApplication
spring-boot-autoconfigure-2.4.4.jar/org/springframework/boot/autoconfigure/SpringBootApplication.java
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class), @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {}
```
* @SpringBootConfiguration : 스프링 부트의 설정을 나타내는 어노테이션.
* @EnableAutoConfiguration : 자동 설정의 핵심어노테이션.
* @ComponentScan : 특정 패키지 경로에서 @Component 설정 클래스를 찾는다. basePackages 프로퍼티 값에 경로를 지정하지 않으면 @ComponentScan을 선언한 위치가 basePackages로 지정된다.

## @EnableAutoConfiguration
spring-boot-autoconfigure-2.4.4.jar/org/springframework/boot/autoconfigre/EnableAutoConfiguration.java
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {}
```
EnableAutoConfiguration 클래스에서 자동 설정을 지원해주는 어노테이션은 `@Import(AutoConfigurationImportSelector.class)`부분이다.

## AutoConfigurationImportSelector class
해당 클래스에서 여러 필드와 메서드로 인해 간소화했다.
spring-boot-autoconfigure-2.4.4.jar/org/springframework/boot/autoconfigure/AutoConfigurationImportSelector.java
```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {}
```
# 
#
[DeferredImportSelector docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/springframework/context/annotation/DeferredImportSelector.html)
# 
#
AutoConfigurationImportSelector 클래스는 DeferredImportSelector 인터페이스에서 구현된 selectImports 메서드를 오버라이드한다.
selectImports 메서드에서 자동 설정할 bean을 결정한다.
DeferredImportSelector 인터페이스 내용은 위의 springframework docs으로 확인할 수 있다.
#
## selectImports method
```java
@Override
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    }
    AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
    return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
}
```
#
#
먼저 isEnabled 메서드로 EnableAutoConfiguration.ENABLED_OVERRIDE_PROPERTY가 설정되어 있는지 확인한다.
getAutoConfigurationEntry 메서드에서 bean을 확인한다.
#
#
```java
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    }
    AnnotationAttributes attributes = getAttributes(annotationMetadata);
    List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
    configurations = removeDuplicates(configurations);
    Set<String> exclusions = getExclusions(annotationMetadata, attributes);
    checkExcludedClasses(configurations, exclusions);
    configurations.removeAll(exclusions);
    configurations = getConfigurationClassFilter().filter(configurations);
    fireAutoConfigurationImportEvents(configurations, exclusions);
    return new AutoConfigurationEntry(configurations, exclusions);
}
```
#
```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
    + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```
getCandidateConfigurations 메서드에서 SpringFactoriesLoader.loadFactoryNames 메서드로 정의되어 있는 자동 설정할 클래스들을 불러온다. 정의된 클래스들은 spring-boot-autoconfigure-2.4.4.jar/META-INF/spring.factories에 정의되어 있다.
이 정의된 클래스들을 불러오면 여러 스프링 부트 스타터 등록으로 인해 중복된 bean이 자주 일어난다.
그래서 getAutoConfigurationEntry 메서드에서 removeDuplicates, getExclusions 메서드가 제외할 설정과 중복 설정을 제외시킨다.

## Bean 등록 및 자동 설정 파일
파일 위치 spring-boot-autoconfigure-2.4.4.jar/
* META-INF/spring.factories : 자동 설정할 정의된 클래스 목록
* META-INF/spring-configuration-metadata.json : 자동 설정에 사용할 프로퍼티가 정의된 목록

자동 설정에 사용할 프로퍼티 값을 추가하려면 application.properties나 application.yml에 추가해주면 된다.
#
#
[Application properties docs](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties)
#
프로퍼티 값은 위의 spring boot docs에서 확인할 수 있다.
