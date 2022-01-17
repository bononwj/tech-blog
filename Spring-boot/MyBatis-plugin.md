# MyBatis Plugin

## How to create a MyBatis plugin

This example is that *Setting create_time automatically when commit update or insert commands to MySql*

These are key abilities we should use:

- Annotation, label a propert of a class then the propert will be changed to Date when the class was instance and insert or update to MySql.
- Interceptor, MyBatis' interceptor can change some behaviors when inserting or updating to MySql.
- Executor, MyBatis's executor is the key component in MyBatis that executing the query, insert and update commands to MySql. 
- SqlCommandType, MyBatis's SqlCommandType can indicate which behavior will be intercepted, inserting or updating.
- Field, a propert of a object.

### 1. Create a Annotation

> annotation.CreateTime
``` java
@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.FIELD })
public @interface CreateTime {
    String value() default "";
}
```

> annotation.UpdateTime
``` java
@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.FIELD })
public @interface UpdateTime {
    String value() default "";
}
```

### 2. Create a Interceptor

> intercept.GenerateTimeInterceptor
``` java
import com.bonon.myblog.common.annotation.CreateTime;
import com.bonon.myblog.common.annotation.UpdateTime;
import org.apache.ibatis.executor.Executor;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.mapping.SqlCommandType;
import org.apache.ibatis.plugin.*;
import org.springframework.stereotype.Component;

import java.lang.reflect.Field;
import java.util.*;

@Component
@Intercepts({ @Signature(type = Executor.class, method = "update", args = { MappedStatement.class, Object.class })})
public class GenerateTimeInterceptor implements Interceptor {

  @SuppressWarnings({ "unchecked", "rawtypes" })
  @Override
  public Object intercept(Invocation invocation) throws Throwable {
    // do something that finding the Fields and changing the creatTime
    return invocation.proceed();
  }

  @Override
  public Object plugin(Object target) {
    if (target instanceof org.apache.ibatis.executor.Executor) {
      return Plugin.wrap(target, this);
    }
    return target;
  }

  @Override
  public void setProperties(Properties properties) {}
}
```

### 3. Find the Fields

> intercept.GenerateTimeInterceptor
``` java
// do something that getting the parameter that will be committed.
if (parameter instanceof Map) {
    Set<Map.Entry<String, Object>> entrySet = ((Map<String, Object>) parameter).entrySet();
    for (Map.Entry<String, Object> set : entrySet) {
        Object value = set.getValue();
        if (value instanceof List) {
            List tempList = (List) value;
            for (Object obj : tempList) {
                Field[] declaredFields = obj.getClass().getDeclaredFields();
                // do something that changing the creatTime
                // fillTime(declaredFields, sqlCommandType, obj);
            }
        } else {
            Field[] declaredFields = value.getClass().getDeclaredFields();
            // do something that changing the creatTime
            // fillTime(declaredFields, sqlCommandType, value);
        }
    }
} else {
    Field[] declaredFields = parameter.getClass().getDeclaredFields();
    // do something that changing the creatTime
    // fillTime(declaredFields, sqlCommandType, parameter);
}
```

### 4. Get the parameter
> intercept.GenerateTimeInterceptor
``` java
MappedStatement mappedStatement = (MappedStatement) invocation.getArgs()[0];
SqlCommandType sqlCommandType = mappedStatement.getSqlCommandType();
Object parameter = invocation.getArgs()[1];
```

### 5. Change the `createTime`
> intercept.GenerateTimeInterceptor
``` java
public void fillTime(Field[] declaredFields, SqlCommandType sqlCommandType, Object parameter) throws Throwable {
  for (Field field : declaredFields) {
    if (field.getAnnotation(CreateTime.class) != null) {
      if (SqlCommandType.INSERT.equals(sqlCommandType)) {
        field.setAccessible(true);
        field.set(parameter, new Date());
      }
    }
    if (field.getAnnotation(UpdateTime.class) != null) {
      if (SqlCommandType.INSERT.equals(sqlCommandType) || SqlCommandType.UPDATE.equals(sqlCommandType)) {
        field.setAccessible(true);
        field.set(parameter, new Date());
      }
    }
  }
}
```

### 6. Set the Interceptor
> config.MybatisConfiguration

``` java
@Bean
public GenerateTimeInterceptor generateTimeIntercepter() {
  return new GenerateTimeInterceptor();
}
```