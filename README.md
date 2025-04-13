# Spring Boot on AWS Lambda

This project demonstrates how to deploy a Spring Boot application to AWS Lambda by using `aws-serverless-java-container-springboot3` and expose it through API Gateway.

---
## 🛠 Prerequisites

- Java 11 or later (this example uses Java 21)
- Maven or Gradle
- Lambda Execution Role with basic permissions
---

## 📦 Dependencies

Add the following to your `pom.xml`:

```xml
       <dependency>
            <groupId>com.amazonaws.serverless</groupId>
            <artifactId>aws-serverless-java-container-springboot3</artifactId>
            <version>2.1.2</version>
        </dependency>
```

---

## 💡 Lambda Handler Example

```java
public class StreamLambdaHandler implements RequestStreamHandler {
    private static SpringBootLambdaContainerHandler<AwsProxyRequest, AwsProxyResponse> handler;
    static {
        try {
            handler = SpringBootLambdaContainerHandler.getAwsProxyHandler(Application.class);
        } catch (ContainerInitializationException e) {
            // if we fail here. We re-throw the exception to force another cold start
            e.printStackTrace();
            throw new RuntimeException("Could not initialize Spring Boot application", e);
        }
    }

    @Override
    public void handleRequest(InputStream inputStream, OutputStream outputStream, Context context)
            throws IOException {
        handler.proxyStream(inputStream, outputStream, context);
    }
}
```
---

## 🚀 Deployment Process

1. **Build the JAR or zip**
   ```bash
   mvn clean package
   ```
![Screenshot 2025-04-13 233147](https://github.com/user-attachments/assets/ffdd7d22-0f41-47d2-b6f3-f0b5054c8112) 

2. **Create Lambda Function (AWS Console)**
   - In the AWS Console, create a function as per your project requirements.
   - For this project, it is named `DemoLambda` and uses Java 21.
   - Upload the ZIP file created from your project (`.zip` containing compiled JAR and dependencies).
   - Edit the runtime to Java 21 (or your target version).
   - Set the handler as per your package and class (e.g., `org.example.StreamLambdaHandler::handleRequest`).

3. **Test the API**
   - Use the built-in test functionality in the Lambda console.
![Screenshot 2025-04-13 234742](https://github.com/user-attachments/assets/81ddd099-5ea1-4b65-8630-68577318fbdc)


4. **Expose via API Gateway**
   - Create a REST API in API Gateway.
   - Use Lambda Proxy integration.
   - Deploy the API to a stage (e.g., `Demoapi`).
![Screenshot 2025-04-13 235112](https://github.com/user-attachments/assets/d410d1a3-3d0f-4876-9856-4af57ea0be41)

---

## 🔗 Live API Endpoint

The deployed API is accessible at:

👉 [`https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping`](https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping)
![Screenshot 2025-04-13 235042](https://github.com/user-attachments/assets/119c1615-1daa-4592-a39f-eac737ab78b8)



Test it with:

```bash
curl https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping
```
![Screenshot 2025-04-14 001425](https://github.com/user-attachments/assets/830a0ea8-dd4b-4ba9-a3a4-9f3466994273)

