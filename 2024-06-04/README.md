# Java Class Transcript (2024-06-04)

## Attendees
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
**Safwan Kh:** Good morning, everyone. I forgot to send you the transcript for yesterday, so I'll do it today during lunch. I'll also send today's transcript. Good morning, Teresa. Good morning, everyone.

### Recap and Announcements
**Safwan Kh:** We are still on the security topic. As Teresa asked last time, after we finish this application, we will revisit the assessment you have already been assigned. You have time to update your submissions until we start with it, which will likely be next week. This is not mandatory, but it can increase your grades and your level of understanding, which is more important. It can also be part of your portfolio on GitHub.

**Youssef ElKhoury:** Good morning, Safwan. Can we also add the login and other features to the Tracker app to make it a full app?

**Safwan Kh:** Sure, my plan is to add security features like login and registration to the expense tracker. We will also cover topics like different roles and email verification.

### Security Features
**Safwan Kh:** Today, we will continue covering the controller. I have already added the templates for login and sign up. We will debug any errors that might appear. After we have a stable system, I will explain the security configuration step by step.

### Debugging and Enhancements
**Safwan Kh:** Let's start by running the system to see what features we are missing. We need to add a sign-in and sign-up feature to the homepage. Currently, the system forces us to log in immediately, which we will change.

### Adding Post Mapping for Sign Up
**Safwan Kh:** Let's add the post mapping for the sign-up feature. We will handle the endpoint and save the user in the database. After signing up, we will redirect to the login page.

### Debugging Issues
**Youssef ElKhoury:** I'm facing an error after pulling your code. Can you help?

**Safwan Kh:** Sure, let me see your code. It looks like a port issue. Change the port to 8080 and try again.

**Youssef ElKhoury:** Thank you, Safwan. It works now.

### Post Mapping for Login
**Safwan Kh:** Let's add the post mapping for the login feature. We will check if the user exists and if the password is correct. If both are true, we will redirect to the homepage; otherwise, we will show an error message.

### Debugging Login Issues
**Safwan Kh:** We are facing issues with the login feature. It seems like the user data is not correctly retrieved. We need to debug this further.

### Lunch Break
**Safwan Kh:** Let's have a lunch break and come back to debug the login issue.

### Post-Lunch Debugging
**Safwan Kh:** Welcome back. I found the solution during the break. We need to remove the `BCryptPasswordEncoder` bean from the security config to avoid a circular dependency. We will create a new configuration class for the password encoder.

### Final Changes
**Safwan Kh:** I have made the necessary changes. The login and sign-up features are now working. I have also added buttons for login and sign-up on the homepage. You can now log in and sign up successfully.

### Task for Students
**Safwan Kh:** Your task is to add the login and sign-up logic to the expense tracker application. This will help you practice the security features we have covered. If you have time, you can also try to implement the logout feature, but it's optional.

### Conclusion
**Safwan Kh:** Tomorrow, we will cover the logout feature and connect users with accounts. Each user can have multiple accounts, but each account can have only one user. Have a nice rest of the day, and see you tomorrow.

---

**Meeting ended at 06:59:40**
