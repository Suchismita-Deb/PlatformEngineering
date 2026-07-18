Linux understanding will guide us - The CPU issue and  why the service wont start.

The first part to understand the process and signal.

Real life cases - `CrashLoopBackOff` in the Kubernetes pod in an incident.

<details class="quiz-toggle">
<summary>CrashLoopBackOff</summary>
CrashLoopBackOff in Kubernetes means a container inside a Pod is repeatedly crashing and restarting with exponential delays. It’s not the root error itself but a symptom that something inside the container is failing — you must check logs and events to debug.
</details>

### Autowiring in Spring.
[//]: # (What is Autowiring in Spring?)
<div class="quiz-box">
<b>What is Autowiring in Spring?</b>
<details class="quiz-toggle">
<summary>Reveal Answer</summary>
Autowiring is a feature in the Spring Framework that enables the automatic injection of dependencies into a bean. Instead of explicitly configuring the dependencies in a Spring configuration file, the container automatically resolves and injects them based on a specified strategy.

**Uses**.

**Reduces Boilerplate Code** - Eliminates the need to manually specify bean dependencies.  
**Simplifies Configuration** - Container automatically manages relationships between beans.  
**Improves Readability** - Makes the code cleaner and easier to maintain.

Types of Autowiring Modes.

**no (Default)**  
No autowiring is performed. Dependencies must be explicitly defined using property or constructor-arg.
```xml
<bean id="userService" class="com.example.UserService">
<property name="userRepository" ref="userRepository" />
</bean>
```
**byName**  
Autowires a bean by matching its property name with a bean name in the configuration.
```xml
<bean id="userService" class="com.example.UserService" autowire="byName" />
<bean id="userRepository" class="com.example.UserRepository" />
```
**byType**  
Autowires a bean if a single bean of the matching type exists in the container.
```xml
<bean id="userService" class="com.example.UserService" autowire="byType" />
<bean id="userRepository" class="com.example.UserRepository" />
```
**constructor**  
Autowires dependencies by matching constructor parameters with bean types in the container.
```xml
<bean id="userService" class="com.example.UserService" autowire="constructor" />
<bean id="userRepository" class="com.example.UserRepository" />
```
**autodetect (Deprecated in Spring 4.3)**  
Spring attempts to autowire using constructor. If that fails, it falls back to byType

Modern Spring applications prefer annotations over XML for autowiring.

`@Autowired` - Automatically injects the required bean by type.  
`@Qualifier` - Resolves conflicts when multiple beans of the same type exist by specifying the bean name.  
`@Primary` - Marks a bean as the primary candidate for autowiring when multiple beans of the same type exist.

Tradeoffs of Autowiring.

**Ambiguities** - Can cause issues when multiple beans of the same type exist.  
**Hidden Dependencies** - Makes it harder to track bean relationships.  
**Testing Challenges** - Autowired dependencies may complicate unit testing.
</details>
</div>