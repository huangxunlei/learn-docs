## 使用 Gson 将 JSON 字符串转换为 `List` 集合

#### 1. 转为自定义对象集合（最常用）

如果你的 JSON 是一个对象数组，如 `[{"name":"张三","age":18}, {"name":"李四","age":20}]`：

```java
import com.google.gson.Gson;
import com.google.gson.reflect.TypeToken;
import java.lang.reflect.Type;
import java.util.List;

// 1. 初始化 Gson 实例
Gson gson = new Gson();

// 2. 你的 JSON 字符串
String jsonStr = "[{\"name\":\"张三\",\"age\":18}, {\"name\":\"李四\",\"age\":20}]";

// 3. 使用 TypeToken 定义泛型类型
Type listType = new TypeToken<List<User>>() {}.getType();

// 4. 解析
List<User> userList = gson.fromJson(jsonStr, listType);

```

### 2.转为基础类型集合（如 String、Integer）

```java
String jsonStr = "[\"Apple\", \"Banana\", \"Orange\"]";

// 使用 TypeToken 指定 List<String>
Type listType = new TypeToken<List<String>>() {}.getType();

List<String> fruits = new Gson().fromJson(jsonStr, listType);

```

## 3.先转为数组，再转为 List（无需 TypeToken 的替代方案）

```java
import java.util.Arrays;
import java.util.List;

String jsonStr = "[{\"name\":\"张三\"},{\"name\":\"李四\"}]";

// 1. 先解析为 User[] 数组
User[] userArray = new Gson().fromJson(jsonStr, User[].class);

// 2. 转换为 List (注意：此 List 长度固定，不能进行 add/remove 操作)
List<User> userList = Arrays.asList(userArray);

// 如果需要对 List 进行增删修改，用下面的方式包裹：
// List<User> modifiableList = new ArrayList<>(Arrays.asList(userArray));

```

