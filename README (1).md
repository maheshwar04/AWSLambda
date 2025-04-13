# Spring Boot on AWS Lambda

This project demonstrates how to deploy a Spring Boot application to AWS Lambda using the `aws-serverless-java-container` library and expose it through API Gateway.

---

## 🖼️ Architecture Diagram

![Architecture Diagram](path/to/your/image.png)

*Replace the image path with your actual image file or URL.*

---

## 🛠 Prerequisites

- Java 11 or later (this example uses Java 21)
- Maven or Gradle
- AWS CLI configured
- Lambda Execution Role with basic permissions
- Docker (optional)

---

## 📦 Dependencies

Add the following to your `pom.xml`:

```xml
<dependency>
  <groupId>com.amazonaws.serverless</groupId>
  <artifactId>aws-serverless-java-container-springboot2</artifactId>
  <version>1.8</version>
</dependency>
```

---

## 💡 Lambda Handler Example

```java
public class StreamLambdaHandler implements RequestHandler<AwsProxyRequest, AwsProxyResponse> {
    private static final SpringBootLambdaContainerHandler<AwsProxyRequest, AwsProxyResponse> handler;

    static {
        try {
            handler = SpringBootLambdaContainerHandler.getAwsProxyHandler(Application.class);
        } catch (ContainerInitializationException e) {
            throw new RuntimeException("Initialization failed", e);
        }
    }

    @Override
    public AwsProxyResponse handleRequest(AwsProxyRequest input, Context context) {
        return handler.proxy(input, context);
    }
}
```

---

## 🚀 Deployment Process

1. **Build the JAR**
   ```bash
   mvn clean package
   ```

2. **Create Lambda Function (AWS Console)**
   - In the AWS Console, create a function as per your project requirements.
   - For this project, it is named `DemoLambda` and uses Java 21.
   - Upload the ZIP file created from your project (`.zip` containing compiled JAR and dependencies).
   - Edit the runtime to Java 21 (or your target version).
   - Set the handler as per your package and class (e.g., `com.example.StreamLambdaHandler`).

3. **Test the API**
   - Use the built-in test functionality in the Lambda console.

4. **Expose via API Gateway**
   - Create a REST API in API Gateway.
   - Use Lambda Proxy integration.
   - Deploy the API to a stage (e.g., `Demoapi`).

---

## 🔗 Live API Endpoint

The deployed API is accessible at:

👉 [`https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping`](https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping)

Test it with:

```bash
curl https://l0lde21y33.execute-api.us-east-1.amazonaws.com/Demoapi/ping
```

---

## 📌 Notes

- Lambda cold starts may affect performance.
- Consider optimizing startup time and memory allocation.
- Use environment variables in Lambda config for flexibility.
- For large projects, explore AWS SAM or Terraform for deployment automation.

---

## 📜 License

MIT