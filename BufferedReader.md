StringBuilder。java的处理字符串的可变类

MutableInteger   int的可变类



### `BufferedReader` 是 Java 中用于读取字符流的缓冲输入流。它继承自 `Reader` 类，提供了一些缓冲功能，能够一次读取一行或指定数量的字符，以提高读取效率。

主要特点和用法包括：

1. **缓冲功能：** `BufferedReader` 在内部维护了一个缓冲区，允许一次读取多个字符，而不是每次都从底层输入流中读取一个字符。这提高了读取效率。

2. **逐行读取：** `BufferedReader` 提供了 `readLine()` 方法，用于逐行读取文本文件。这对于读取文本文件中的每一行数据非常方便。

3. **构造方法：** 可以通过给 `BufferedReader` 构造方法传递一个 `Reader` 对象来创建实例。例如：

   ```
   javaCopy code
   BufferedReader bufferedReader = new BufferedReader(new FileReader("example.txt"));
   ```

4. **读取方法：** 除了 `readLine()` 外，还可以使用 `read(char[] cbuf, int off, int len)` 等方法读取指定数量的字符到字符数组中。

5. **关闭资源：** 使用完 `BufferedReader` 后，应该调用 `close()` 方法关闭资源，释放相关的系统资源。





### `StringUtils` 是一个常见的字符串操作工具类，它并不是 Java 标准库的一部分，而是由 Apache Commons Lang 库提供。该工具类包含了很多用于处理字符串的方法，提供了更便捷的字符串操作功能。

以下是 `StringUtils` 类的一些常见方法：

1. **`isEmpty` 和 `isBlank`：**
   - `StringUtils.isEmpty(CharSequence str)`：检查字符串是否为 `null` 或者空字符串。
   - `StringUtils.isBlank(CharSequence str)`：检查字符串是否为 `null` 或者空白字符串。空白字符串是指只包含空格、制表符、换行符等不可见字符的字符串。
2. **`trim`：**
   - `StringUtils.trim(String str)`：去除字符串两端的空白字符。
3. **`equals` 和 `equalsIgnoreCase`：**
   - `StringUtils.equals(CharSequence str1, CharSequence str2)`：比较两个字符串是否相等，避免 `NullPointerException`。
   - `StringUtils.equalsIgnoreCase(CharSequence str1, CharSequence str2)`：比较两个字符串是否相等，忽略大小写。
4. **`join`：**
   - `StringUtils.join(Object[] array, char separator)`：将数组中的元素用指定的分隔符连接成一个字符串。
5. **`substring`：**
   - `StringUtils.substring(String str, int start, int end)`：截取字符串的子串。
6. **`replace`：**
   - `StringUtils.replace(String text, String searchString, String replacement)`：替换字符串中的指定部分。
7. **`split`：**
   - `StringUtils.split(String str, char separatorChar)`：使用指定的字符分隔符将字符串拆分成数组。
8. **`defaultString`：**
   - `StringUtils.defaultString(String str, String defaultStr)`：如果字符串为 `null`，则返回默认值。

这只是 `StringUtils` 提供的一部分方法，实际上还有很多其他方法可以用于处理字符串。如果你的项目使用了 Apache Commons Lang 库，你可以方便地使用 `StringUtils` 类来执行字符串操作。