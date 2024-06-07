# Java Class Transcript (2024-06-03)

## Attendees
- Ahmad Sharif
- Chizuru Yamauchi
- Ioannis Laventzakis
- Michelle Janer
- Mikel Bano
- Muyang Zander
- Rene Heyer
- Safwan Kh
- Shoghik Kachatryan
- Terissa Gwendjo
- Youssef El Khoury
- Zuehre Turhan

## Transcript

### Introduction
**Safwan Kh:** Good morning, everyone. We are almost all here, just waiting for a couple more people. Let's get started.

### Recap of Previous Sessions
**Safwan Kh:** Last time, we worked on the online banking system. We covered the second application workshop and started using security dependencies. Today, we'll continue from where we left off.

### Project Overview
**Safwan Kh:** We are working on an online banking system. The current version is available on our GitHub organization. It includes all the updates I've added. You can find the link on the Slack channel.

### Dependencies
**Safwan Kh:** Our project uses the Spring framework. We haven't added security dependencies yet, but we will do that today. We will add Spring Security and a password hashing dependency.

### Application Properties
**Safwan Kh:** Let's review the application properties. We are using PostgreSQL with typical configurations. We have set the server port to 8080 and configured Hibernate to show SQL in the terminal.

### Model Layer
**Safwan Kh:** We have an `Account` entity with fields like `id`, `accountNumber`, `accountType`, and `balance`. We also have a `User` entity with fields like `id`, `username`, and `password`.

### Repository Layer
**Safwan Kh:** The `AccountRepository` and `UserRepository` extend `JpaRepository`. We have custom methods like `findByAccountNumber` and `findByUsername`.

### Service Layer
**Safwan Kh:** We have a `BankingService` interface and its implementation. The service methods include `openAccount`, `depositMoney`, `withdrawMoney`, and `transferMoney`. We use `@Transactional` and `@Modifying` annotations for transaction management.

### Controller Layer
**Safwan Kh:** The `BankingController` handles endpoints for opening accounts, depositing money, withdrawing money, and transferring money. Each service has a corresponding form and endpoint.

### Security Configuration
**Safwan Kh:** We will now add Spring Security to our project. First, we need to add the Spring Security dependency to our `pom.xml` file.

### Adding Spring Security Dependency
1. **Search for Spring Security Dependency:**
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
   ```

2. **Add to `pom.xml`:**
   ```xml
   <dependencies>
       <!-- Other dependencies -->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-security</artifactId>
       </dependency>
   </dependencies>
   ```

### User Service Implementation
**Safwan Kh:** We need to update the `UserService` interface and its implementation. The `UserService` should extend `UserDetailsService` and include methods like `saveUser`, `findByUsername`, and `checkPassword`.

### Security Configuration Class
**Safwan Kh:** Create a `SecurityConfig` class to configure Spring Security. Use annotations like `@Configuration` and `@EnableWebSecurity`. Define beans for `BCryptPasswordEncoder` and `SecurityFilterChain`.

### Controller for Authentication
**Safwan Kh:** Create a `UserController` to handle authentication endpoints. Add `@GetMapping` for login and signup forms.

### Task for Students
**Safwan Kh:** Your task is to create the HTML templates for the login and signup forms. Use Thymeleaf for rendering the forms. If you have any questions, feel free to ask in breakout rooms.

### Conclusion
**Safwan Kh:** We'll continue with the security configuration and controller implementation tomorrow. Make sure to complete the HTML templates for the login and signup forms. See you all tomorrow!

---

**Meeting ended at 05:12:46**
