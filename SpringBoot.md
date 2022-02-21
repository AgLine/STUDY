# annotation
@ResponseBody

@RequestMapping("/url")

주소창에서 바로 controller 호출 가능

@ResponseBody
	
@PostMapping("/url")

jsp 파일에서 post로 호출 주소창에 직접 호출할 경우 

Exception Processing ErrorPage[errorCode=0, location=/error] 에러 발생

# Web Appilcation Server


```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@EnableAutoConfiguration(exclude = { DataSourceAutoConfiguration.class })
@SpringBootApplication
@EnableAspectJAutoProxy
public class NamingApplication extends SpringBootServletInitializer {// 상속 후

	public static void main(String[] args) {
		SpringApplication.run(NamingApplication.class, args);
	}

	@Override // 오버라이드 추가
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(NamingApplication.class);
	}

}
```