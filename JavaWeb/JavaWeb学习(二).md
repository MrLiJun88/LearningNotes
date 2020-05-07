# JavaWeb学习(二)

## 1. Json在Java中的使用

> * 我们要使用 json 和 java 中使用，我们需要使用到一个第三方的包。它就是 gson.jar。
>
> * Gson 是 Google 提供的用来在 Java 对象和 JSON 数据之间进行映射的 Java 类库。可以将一个 JSON 字符串转成一个 Java 对象，或者反过来。
>
> * ```xml
>   		<dependency>
>               <groupId>com.google.code.gson</groupId>
>               <artifactId>gson</artifactId>
>               <version>2.8.5</version>
>           </dependency>
>   ```

### 1.1 Java对象与Json互相转换

> * ```java
>   Gson gson = new Gson();
>   //将对象转成json数据
>   String toJson = gson.toJson(person);
>   
>   //将json数据转成java对象
>   Person fromJson = gson.fromJson(toJson, Person.class);
>   ```

### 1.2 List对象与Json转换

> * ```java
>   	Gson gson = new Gson();
>     
>           //将list对象转成json数据
>           String toJson = gson.toJson(personList);
>           System.out.println(toJson);
>     
>           //将json数据转成list对象
>           List<Person> fromJson = gson.fromJson(toJson, new TypeToken<List<Person>>() {
>           }.getType());
>           System.out.println(fromJson);
>   ```

### 1.3 Map对象与Json转换

> * ```java
>   Gson gson = new Gson();
>           //将map对象转成json数据
>           String toJson = gson.toJson(map);
>           System.out.println(toJson);
>   
>           //将json数据转成map对象
>           HashMap<String,Person> fromJson = gson.fromJson(toJson, new TypeToken<HashMap<String,Person>>() {
>           }.getType());
>           fromJson.forEach((k,v) -> System.out.println(k + " " + v));
>   ```

