# Java Class Transcript (2024-06-06)

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
- Youssef ElKhoury
- Zuehre Turhan

## Transcript

### Introduction
**Safwan Kh:** Good morning, everyone.

**Zuehre Turhan:** Good morning.

**Safwan Kh:** Good morning, Teresa. Today is Thursday, the last day of the week. Happy Thursday! I recommend and encourage you to have a morning check-in, just to greet each other.

**Youssef ElKhoury:** Good morning, everyone.

**Terissa Gwendjo:** Good morning, good morning.

**Safwan Kh:** Good morning, everyone. We are about to start the API modules. Our focus will be on our endpoints and how to communicate with external APIs. Security will be a very important part of it. We need to secure every endpoint that we develop ourselves and be aware of how secure the APIs we connect to are.

### Recap and Announcements
**Safwan Kh:** We had an agenda from yesterday about relationships between tables. We have two entities currently in our online banking system: a user and an account. I look forward to seeing the same implemented in your expense tracker system. Starting from next week, I will live code the expense tracker system. Monday morning will be the last time you can update and submit your updates.

### Security Configuration
**Safwan Kh:** Let's review the security configuration. We decoupled the encoder config and the security config. Why did we do that? What was the exception we had previously?

**Terissa Gwendjo:** Was it the user not found exception?

**Safwan Kh:** Yes, it was related to user not found exception. We had a problem where there were no helpful messages or warnings. We figured out that it was not loading the user from the database. We had a circular dependency error. We decoupled the encoder config to avoid this issue.

### Explanation of Security Config
**Safwan Kh:** The security config class is responsible for security configurations and settings in our Spring application. The `@Configuration` annotation indicates that this class contains configuration settings. The `@EnableWebSecurity` annotation enables Spring Security's web security.

**Michelle Janer:** The `@Bean` annotation is used to indicate that a method produces a bean managed by the Spring container.

**Safwan Kh:** Correct. The `@Bean` annotation tells Spring that the method will return an object that should be registered as a bean in the Spring application context. This allows the object to be used as a dependency to be injected into other classes.

### User Details Service
**Safwan Kh:** The `userDetailsService` method returns an instance of `UserServiceImplementation`, which is also an instance of `UserDetailsService`. This is necessary for the security configuration to authenticate users.

### Security Filter Chain
**Safwan Kh:** The `securityFilterChain` method configures a chain of security filters applied to incoming HTTP requests using the `HttpSecurity` class. It configures URL-based authorization, form-based authentication, and logout functionality.

### Configure Global Method
**Safwan Kh:** The `configureGlobal` method is used for global authentication configuration. It sets the `UserDetailsService` implementation and the password encoder for the authentication manager.

### Short Break
**Safwan Kh:** Let's take a short break for 15 minutes and then come back to continue.

### Post-Break Session
**Safwan Kh:** Welcome back. Let's continue with the explanation. The `EncoderConfig` class is responsible for providing the `BCryptPasswordEncoder` bean. This class is necessary to avoid circular dependencies.

### Implementing Relationships
**Safwan Kh:** Our goal for today is to add a relationship between the `User` entity and the `Account` entity. One user can have many accounts, and one account can have only one user. This will be a one-to-many and many-to-one relationship.

### Updating Entities
**Safwan Kh:** Let's start with the `Account` entity. We need to add a `ManyToOne` annotation and a `JoinColumn` annotation for the `user` field. We also need to update the `User` entity with a `OneToMany` annotation for the `accounts` list.

### Updating Services
**Safwan Kh:** We need to update the `BankingService` interface and its implementation to include the `userId` when opening a new account. We also need to update the `UserServiceImplementation` to handle saving a user with accounts.

### Task for Students
**Safwan Kh:** Your task is to update the controller and the Thymeleaf templates to add the relationships in the complete system. Also, ensure that your expense tracker system has the sign-up, login, and logout features implemented by Monday morning.

### Conclusion
**Safwan Kh:** Let's have our lunch break. After lunch, continue working on the expense tracker system and submit the last working version by Monday morning. I will be available for support in breakout rooms. See you after lunch.

---

**Meeting ended at 06:50:15**
