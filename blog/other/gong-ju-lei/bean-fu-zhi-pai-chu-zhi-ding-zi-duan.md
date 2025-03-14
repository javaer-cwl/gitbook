# Bean复制排除指定字段

## 场景描述

&#x20;       我在开发中发现一个场景，两个对象需要将object1复制到object2中，用于冗余使用，但是不能把所有的属性全部复制到object2中，而使用spring自带的BeanUtil 会全部覆盖，而且为null的值也会覆盖。

## 1. ClassUtil.java

&#x20;       用于获取属性名称

```java
import cn.hutool.core.util.StrUtil;

import java.io.Serializable;
import java.lang.invoke.SerializedLambda;
import java.lang.reflect.Method;
import java.util.function.Function;

public class ClassUtils {

    private static final String FIELD_METHOD_PREFIX_GET = "get";
    private static final String FIELD_METHOD_PREFIX_IS = "is";
    @FunctionalInterface
    public interface FieldFunction<T, R> extends Function<T, R>, Serializable {}

    /**
     * 获取属性名
     *
     * @param <T> 类
     * @param fn  属性 Getter
     * @return 属性名
     */
    public static <T> String getFieldName(FieldFunction<T, ?> fn) {
        try {
            Method method = fn.getClass().getDeclaredMethod("writeReplace");
            method.setAccessible(true);
            SerializedLambda serializedLambda = (SerializedLambda) method.invoke(fn);
            String implMethodName = serializedLambda.getImplMethodName();
            if (implMethodName.startsWith(FIELD_METHOD_PREFIX_GET)) {
                implMethodName = implMethodName.substring(3);
            } else if (implMethodName.startsWith(FIELD_METHOD_PREFIX_IS)) {
                implMethodName = implMethodName.substring(2);
            }
            if (StrUtil.isNotBlank(implMethodName)) {
                return implMethodName.substring(0, 1).toLowerCase() + implMethodName.substring(1);
            }
        } catch (ReflectiveOperationException e) {
            throw new RuntimeException(e);
        }
        return "";
    }
}

```

## 2. ObjectConverter.java

&#x20;       excludeFields用于排除字段，如果source中属性为null则不会覆盖

```java
import java.lang.reflect.Field;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class ObjectConverter {

    public static <S, T> T convert(S source, T target, List<String> excludeFields) {
        if (source == null || target == null) {
            throw new IllegalArgumentException("Source and target objects must not be null.");
        }
        Set<String> excludeFieldSet = new HashSet<>(excludeFields);
        copyMatchingFields(source, target, excludeFieldSet);
        return target;
    }

    private static void copyMatchingFields(Object source, Object target, Set<String> excludeFields) {
        Class<?> sourceClass = source.getClass();
        Class<?> targetClass = target.getClass();

        for (Field sourceField : sourceClass.getDeclaredFields()) {
            String fieldName = sourceField.getName();
            if (excludeFields.contains(fieldName)) {
                continue;
            }
            try {
                Field targetField = targetClass.getDeclaredField(fieldName);
                if (isTypeCompatible(sourceField.getType(), targetField.getType())) {
                    sourceField.setAccessible(true);
                    targetField.setAccessible(true);
                    Object value = sourceField.get(source);
                    if (value != null) {
                        targetField.set(target, value);
                    }
                }
            } catch (NoSuchFieldException ignored) {
                // Skip fields not found in the target class
            } catch (IllegalAccessException e) {
                throw new RuntimeException("Failed to copy field: " + fieldName, e);
            }
        }
    }

    private static boolean isTypeCompatible(Class<?> sourceType, Class<?> targetType) {
        if (sourceType.isPrimitive()) {
            sourceType = getWrapperType(sourceType);
        }
        if (targetType.isPrimitive()) {
            targetType = getWrapperType(targetType);
        }
        return targetType.isAssignableFrom(sourceType);
    }

    private static Class<?> getWrapperType(Class<?> primitiveType) {
        if (primitiveType == int.class) return Integer.class;
        if (primitiveType == long.class) return Long.class;
        if (primitiveType == boolean.class) return Boolean.class;
        if (primitiveType == char.class) return Character.class;
        if (primitiveType == byte.class) return Byte.class;
        if (primitiveType == short.class) return Short.class;
        if (primitiveType == float.class) return Float.class;
        if (primitiveType == double.class) return Double.class;
        return primitiveType;
    }
}
```

## 3. 验证

```java
public class Main {

    public static void main(String[] args) {
        User user1 = User.builder().id(1L).name("陈").age(24).remark("hello").build();


        User user2 = User.builder().id(2L).name("王").age(25).remark("apple").build();

        List<String> excludeFields = new ArrayList<>();
        excludeFields.add(ClassUtils.getFieldName(User::getId));

        ObjectConverter.convert(user1, user2, excludeFields);

        System.out.println(user2);

    }
}
```

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
