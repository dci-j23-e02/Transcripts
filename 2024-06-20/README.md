### Java Class Transcript Summary (2024-06-20)

### Main Topics and Ideas

1. **Introduction and Attendees**
   - The meeting started with a list of attendees and a brief introduction.
   - Attendees: Ahmad Sharif, Chizuru Yamauchi, Ioannis Laventzakis, Michelle Janer, Mikel Bano, Muyang Zander, Rene Heyer, Safwan Kh, Shoghik Kachatryan, Terissa Gwendjo, Youssef ElKhoury, Zuehre Turhan.

2. **Password Reset Feature**
The password reset feature is designed to enhance the security and user experience of the application. It allows users to reset their passwords if they forget them, ensuring that only legitimate users can perform the reset.

**Key Points:**
- **Security Questions:** Users need to answer predefined security questions to verify their identity before resetting their password.
- **Service and Repository Methods:** Implementation includes creating a service to handle password updates and repository methods to interact with the database.
- **Endpoint:** A specific endpoint is created for users to request a password reset. This endpoint will handle the verification of security questions and the actual password update process.

**Live Coding Example:**
```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {

    @Autowired
    private UserService userService;

    @PostMapping("/reset-password")
    public ResponseEntity<String> resetPassword(@RequestBody PasswordResetRequest request) {
        boolean isReset = userService.resetPassword(request);
        if (isReset) {
            return ResponseEntity.ok("Password reset successfully.");
        } else {
            return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("Failed to reset password.");
        }
    }
}
```

**Explanation:**
- **PasswordResetRequest:** A DTO (Data Transfer Object) that contains the necessary information for resetting the password, such as the username, security question answers, and the new password.
- **UserService:** The service layer that contains the business logic for resetting the password. It verifies the security questions and updates the password in the database.

3. **Live Coding Session**
  The live coding session focused on implementing the connection to an external API for currency conversion and setting up role-based access control.

**Key Points:**
- **External API Connection:** Demonstrated how to connect to a currency converter API using Spring Boot.
- **Role-Based Access Control:** Implemented roles (user, admin) to restrict access to certain features based on the user's role.

**Live Coding Example:**
```java
@Service
public class CurrencyConverterService {

    @Value("${rate.api.key}")
    private String apiKey;

    private final RestTemplate restTemplate = new RestTemplate();

    public ConversionResponse convertCurrency(String from, String to, double amount) {
        String url = String.format("https://api.exchangerate-api.com/v4/latest/%s?apikey=%s", from, apiKey);
        ResponseEntity<ConversionResponse> response = restTemplate.getForEntity(url, ConversionResponse.class);
        ConversionResponse conversionResponse = response.getBody();
        if (conversionResponse != null) {
            double rate = conversionResponse.getRates().get(to);
            double convertedAmount = rate * amount;
            return new ConversionResponse(from, to, rate, amount, convertedAmount);
        }
        throw new RuntimeException("Failed to convert currency");
    }
}
```

**Explanation:**
- **RestTemplate:** A synchronous client to perform HTTP requests. It simplifies the communication with external APIs by providing methods like `getForEntity`, `postForEntity`, etc.
- **@Value:** Annotation used to inject values from properties files into fields in Spring-managed beans.
- **ResponseEntity:** Represents the entire HTTP response, including status code, headers, and body. It is used to handle responses from RESTful web services.

#### 4. **Spring Boot and Security Configuration**
The discussion covered the necessary dependencies and configurations required for implementing security in a Spring Boot application.

**Key Points:**
- **Dependencies:** Spring Boot, Spring Security, JPA, Hibernate, and validation libraries.
- **Security Configuration:** Setting up security configurations to handle authentication and authorization.

**Live Coding Example:**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
            .authorizeRequests()
            .antMatchers("/api/auth/**").permitAll()
            .antMatchers("/api/currency-converter/**").hasRole("ADMIN")
            .anyRequest().authenticated()
            .and()
            .formLogin().permitAll()
            .and()
            .logout().permitAll();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

**Explanation:**
- **AuthenticationManagerBuilder:** Configures authentication by specifying the user details service and password encoder.
- **HttpSecurity:** Configures HTTP security, including CSRF protection, URL authorization, form login, and logout.
- **PasswordEncoder:** Defines the password encoding mechanism, in this case, BCrypt.


5. **Role-Based Access Control**
Role-based access control (RBAC) restricts access to certain features based on the user's role. This ensures that only authorized users can access specific endpoints.

**Key Points:**
- **Roles:** Defined roles such as user and admin.
- **Annotations:** Used annotations like `@ElementCollection`, `@CollectionTable`, and `@JoinColumn` to map roles to users.

**Live Coding Example:**
```java
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
    private String password;

    @ElementCollection(fetch = FetchType.EAGER)
    @CollectionTable(name = "user_roles", joinColumns = @JoinColumn(name = "user_id"))
    @Column(name = "role")
    private Set<String> roles = new HashSet<>();

    // Getters and setters
}
```

**Explanation:**
- **@ElementCollection:** Defines a collection of basic types (e.g., strings) associated with an entity.
- **@CollectionTable:** Specifies the table that will store the collection of elements.
- **@JoinColumn:** Specifies the foreign key column used to join the collection table with the user table.

6. **Currency Converter API Integration**
   The integration of a currency converter API allows the application to convert currencies using real-time exchange rates.

**Key Points:**
- **API Key:** Obtained from the currency converter API provider.
- **RestTemplate:** Used to perform HTTP requests to the API.

**Live Coding Example:**
```java
@Service
public class CurrencyConverterService {

    @Value("${rate.api.key}")
    private String apiKey;

    private final RestTemplate restTemplate = new RestTemplate();

    public ConversionResponse convertCurrency(String from, String to, double amount) {
        String url = String.format("https://api.exchangerate-api.com/v4/latest/%s?apikey=%s", from, apiKey);
        ResponseEntity<ConversionResponse> response = restTemplate.getForEntity(url, ConversionResponse.class);
        ConversionResponse conversionResponse = response.getBody();
        if (conversionResponse != null) {
            double rate = conversionResponse.getRates().get(to);
            double convertedAmount = rate * amount;
            return new ConversionResponse(from, to, rate, amount, convertedAmount);
        }
        throw new RuntimeException("Failed to convert currency");
    }
}
```

**Explanation:**
- **API Key:** A unique key provided by the API provider to authenticate requests.
- **RestTemplate:** A Spring class used to perform HTTP requests and handle responses.


7. **Q&A and Discussion**
  The Q&A session allowed attendees to ask questions and clarify doubts about the implementation and concepts discussed.

**Key Points:**
- **RESTful API Principles:** Detailed explanation of RESTful API principles, including statelessness, client-server architecture, caching, uniform interface, and layered system.
- **Role-Based Access Control:** Clarification on how roles are implemented and managed in the system.
- **Security Configuration:** Discussion on the importance of security configurations and how to set them up in a Spring Boot application.

**Some of the Questions and Answers:**
1. **What is the purpose of the password reset feature?**
   - The password reset feature allows users to reset their passwords by answering security questions, ensuring that only the legitimate user can perform the reset.

2. **How does role-based access control work in this system?**
   - Role-based access control restricts access to certain features based on the user's role (e.g., admin, user). Specific endpoints are accessible only to users with the appropriate role.

3. **What are the key dependencies required for this implementation?**
   - Key dependencies include Spring Boot, Spring Security, JPA, Hibernate, and validation libraries.

4. **What are the main principles of RESTful APIs?**
   - The main principles include statelessness, client-server architecture, caching, uniform interface, and layered system.

5. **How does the `@ElementCollection` annotation work?**
   - The `@ElementCollection` annotation is used to define a collection of basic types (e.g., strings) associated with an entity. It helps in mapping collections in JPA.

6. **What is the significance of the `restTemplate` in Spring?**
   - The `restTemplate` is a synchronous client used to perform HTTP requests, simplifying communication with external APIs.


8. **Upcoming Events and Announcements**
   - Announcement of an upcoming event on June 25th.
   - Discussion on a career fair on June 27th.

### Keywords
- External API
- Currency Converter
- Role-Based Access Control
- RESTful API
- Validation Dependency
- Client-Server Architecture
- Caching
- Uniform Interface
- Layered System

### Technical Terms Explained
- **RESTful API**: Representational State Transfer API, a web service that adheres to REST principles.
- **Annotations**: Metadata added to Java code to provide additional information to the compiler.
- **Client-Server Architecture**: A model where the client requests services and the server provides them.
- **Caching**: Storing data temporarily to reduce the need for repeated requests.
- **Uniform Interface**: Standardized methods (GET, POST, PUT, DELETE) used in RESTful APIs.
- **Layered System**: Dividing an application into layers (e.g., presentation, business logic, data access).

### Summary
The meeting focused on implementing a password reset feature and integrating a currency converter API into the system. The live coding session demonstrated how to connect to an external API and implement role-based access control using Spring Boot, Spring Security, JPA, and Hibernate. The importance of validation dependencies and RESTful API principles was discussed. Upcoming events and career fairs were also announced.

### Questions to Expand Understanding

1. **What is the purpose of the password reset feature?**
   - The password reset feature allows users to reset their passwords by answering security questions, ensuring that only the legitimate user can perform the reset.

2. **How does role-based access control work in this system?**
   - Role-based access control restricts access to certain features based on the user's role (e.g., admin, user). Specific endpoints are accessible only to users with the appropriate role.

3. **What are the key dependencies required for this implementation?**
   - Key dependencies include Spring Boot, Spring Security, JPA, Hibernate, and validation libraries.

4. **What are the main principles of RESTful APIs?**
   - The main principles include statelessness, client-server architecture, caching, uniform interface, and layered system.

5. **How does the `@ElementCollection` annotation work?**
   - The `@ElementCollection` annotation is used to define a collection of basic types (e.g., strings) associated with an entity. It helps in mapping collections in JPA.

6. **What is the significance of the `restTemplate` in Spring?**
   - The `restTemplate` is a synchronous client used to perform HTTP requests, simplifying communication with external APIs.

### Live Coding Example

```java
package com.example.expensetracker.service;

import java.util.List;
import java.util.stream.Collectors;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import java.util.Map;
import java.util.Set;

@Service
public class CurrencyConverterService {
  @Value("${exchangerate.api.key}")
  private String apiKey;

  private final RestTemplate restTemplate = new RestTemplate();

  public double convert(String fromCurrency, String toCurrency, double amount) {
    String urlString = String.format(
        "https://v6.exchangerate-api.com/v6/%s/pair/%s/%s",
        apiKey, fromCurrency, toCurrency
    );
    Map<String, Object> response = restTemplate.getForObject(urlString, Map.class);
    if (response != null && response.get("conversion_rate") != null) {
      double conversionRate = (double) response.get("conversion_rate");
      return amount * conversionRate;
    }
    throw new RuntimeException("Failed to get conversion rate");
  }

  public Set<String> getSupportedCurrencies() {
    String urlString = String.format(
        "https://v6.exchangerate-api.com/v6/%s/codes",
        apiKey
    );
    Map<String, Object> response = restTemplate.getForObject(urlString, Map.class);
    if (response != null && response.get("supported_codes") != null) {
      return ((List<List<String>>) response.get("supported_codes")).stream()
          .map(code -> code.get(0))
          .collect(Collectors.toSet());
    }
    throw new RuntimeException("Failed to get supported currencies");
  }
}
```

### Extra Explanation

- **RestTemplate**: A synchronous client to perform HTTP requests. It simplifies the communication with external APIs by providing methods like `getForEntity`, `postForEntity`, etc.
- **@Value**: Annotation used to inject values from properties files into fields in Spring-managed beans.
- **ResponseEntity**: Represents the entire HTTP response, including status code, headers, and body. It is used to handle responses from RESTful web services.

By following these principles and using the provided code example, you can effectively integrate external APIs and implement role-based access control in your Spring Boot application.
