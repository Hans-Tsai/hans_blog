Java
===

## 基本概念

### 資料型別

- 原生型別 (Primitive Data Type)
  -  boolean, char, int, short, byte, long, float, and double
- 參考型別 (Reference Data Types)
  - **Integer**, **String**, Class, Interface, **Array** ...等

> **Java 使用 Unicode 系統**; 而不像 C++ 使用 ASCII code 系統
>
> - 因此，Java 的 char 型別需要 2 bytes 才足夠

![data_types](../assets/pics/java/data_types.png)

### 迴圈 (for loop & for-each loop)
- for loop

  - 基本範例
    ```java
    for (int i = 0; i < arr.length; i++) {
      System.out.println("Element at index " + i + " : "+ arr[i]); 
    }
    ```

- for-each loop

  - 常用於 Array, Collection 類別

  - 優點

    - **在 loop body 中，可直接使用 element**，而不是用 index 來存取元素

  - 限制
    
    - 僅能 **往前迭代(iterate forward)**，且 **一次前進一格(single step)**
    
      ```java
      // cannot be converted to a for-each loop
      for (int i=numbers.length-1; i>0; i--) 
      {
            System.out.println(numbers[i]);
      }
      ```
    
    - **無法** 修改原陣列中的元素值
    
      ```java
      for (int num: marks)
      {
          // only changes num, not the array element
          num = num*2;
      }
      ```
    
    - **無法** 追蹤 index
    
      ```java
      for (int num: numbers) 
      { 
          if (num == target) 
          {
              return ???;   // do not know the index of num
          }
      }
      ```
    
    - **無法** 同時存取兩個不同的 Array (因為無法追蹤 index)
    
      ```java
      // cannot be easily converted to a for-each loop 
      for (int i=0; i<numbers.length; i++) 
      {
          if (numbers[i] == arr[i]) 
          { ...
          } 
      }
      ```
    
  - 基本範例
    ```java
    for (int element: arr) {
      System.out.println(element);
    }
    ```

## Array
### 基本概念

- 在記憶體中連續儲存 (store a collection of elements sequentially)
- 使用 index 來隨機存取元素 (randomly access)

  ![array_basic_concept](../assets/pics/java/array_basic_concept.png)

- 可儲存原生型別 or 物件型別，但各元素需為相同型別

- 一旦宣告後，為固定大小 (fixed-size)

- Array 類別 **繼承 Object 類別**

- Array 類別 **實作 Cloneable & java.io.Serializable 介面**

### 宣告(declaring) & 初始化 (initializing)

- 宣告 (有 2 種寫法)

  - 原生型別

    - ```java
      int[] arr; // 推薦這個寫法，較直覺
      ```
    - ```java
      int arr[];
      ```
  - 參考型別

    - ```java
      Student[] arr = new Student[5]; // student is a user-defined class
      ```
  - 多維陣列 (e.g. 2D Array)

    ```java
    int[][] arr = new int[3][3];
    ```

- 初始化 (分配記憶體給該 Array)

  ```java
  arr = new int[5];
  ```

- 同時宣告 + 初始化

  ```java
  int[] arr = new int[5];
  ```

### 取得陣列容量(capacity)

- `arr.length` 屬性
  - 適用於 int[], double[], String[]
  
  ```java
  int[] arr = new int[5]; // 宣告一個含有 5 格容量(capacity)的 Array
  System.out.println(arr.length); // 5
  
  String[] strArr = { "GEEKS", "FOR", "GEEKS" };
  System.out.println(strArr.length); // 3
  ```

### 存取元素

- 視情況可用 for loop, for-each loop 來存取 Array 元素
  - for loop 的 Array index 從 0 (首個元素) ~ -1 (最末項元素) 
- 若存取合法 index 範圍外的元素 => JVM 會拋出 **ArrayIndexOutOfBoundsException**

### 複製陣列

- 1D Array

  - 採用 deep copy: 另外複製一個含有相同元素們的陣列

    ![1d_array_clone](../assets/pics/java/1d_array_clone.png)

  - 基本範例

    ```java
    int arr[] = { 1, 2, 3 };
    int cloneArr[] = arr.clone();
    System.out.println(intArr == arr); // false
    ```


- Multi-D Array

  - 採用 shallow copy: 僅複製原始物件的最外層結構(外殼)，裡面的元素仍是指向原資料的記憶體位置

    ![multi_d_array_clone](../assets/pics/java/multi_d_array_clone.png)

  - 基本範例

    ```java
    int intArray[][] = {{ 1, 2, 3 }, { 4, 5 }};
    int cloneArr[] = arr.clone();
    
    System.out.println(intArray == cloneArray); // false
    System.out.println(intArray[0] == cloneArray[0]); // true (sub-arrays are shared)
    System.out.println(intArray[1] == cloneArray[1]); // true (sub-arrays are shared)
    ```

### 參考資料

- [GeeksForGeeks-Arrays in Java](https://www.geeksforgeeks.org/arrays-in-java/?ref=lbp)

## ArrayList

### 基本概念

- ArrayList 類別 **繼承 Collection 類別**，且屬於 java.util package 的其中一個類別
  - ArrayList 類別 **實作 List 介面**
  - 類似 C++ 的 vector
  
  ![arraylist_extends_collection](../assets/pics/java/arraylist_extends_collection.png)
  
- 使用 index 來隨機存取元素 (randomly access)
  ![arraylist_basic_concept](../assets/pics/java/arraylist_basic_concept.png)

- 可變大小 (dynamic size): 表示新增, 刪除元素會自動調整 ArrayList.size()

- ArrayList 僅能裝 wrapper class，而不能裝原生型別

- 非 Synchronized: 當 Multi-thread 同時修改同一個 ArrayList 時，無法保證 thread-safe (數據一致性)

### 宣告(declaring) & 初始化 (initializing)

- 基本範例

  ```java
  ArrayList<Integer> arrL = new ArrayList<Integer>(2);
  ```

### 基本操作

- 取得 ArrayList 的元素數

  - `ArrayList.size()` 方法

  ```java
  int sz = arrL.size();  // 4
  ```

- 新增元素

  - 若 ArrayList 的 size 滿了，預設會自動 doubled size 來儲存更多的元素
  - ArrayList 可以接受 null, 重複值

  ```java
  ArrayList<String> arrL = new ArrayList<>();
  // 法 1: 正常加入元素
  arrL.add("Alice");
  arrL.add("Bob");
  // 法 2: 指定 index，插入元素
  arrL.add(1, "and")
  System.out.println(al); // [Alice, and, Bob]
  ```

- 設定/更新元素值

  ```java
  arrL.set(2, "John");
  System.out.println(arrL); // [Alice, and, John]
  ```

- 刪除元素

  ```java
  // 法 1: 刪除指定元素 (若 ArrayList 中存在相同的物件，則刪除第一個出現的物件)
  arrL.remove("John");
  // 法 2: 刪除指定 index 上的元素
  arrL.remove(0);
  System.out.println(arrL); [and]
  ```

- 迭代元素

  ```java
  ArrayList<String> arrL = new ArrayList<>();
  // 初始化時，賦值多個元素值 (Java 9+)
  ArrayList<String> arrL = new ArrayList<>(List.of("Alice", "and", "Bob"));
  
  // 法 1: 使用 for loop
  for (int i = 0; i < arrL.size(); i++) {
  	System.out.print(al.get(i) + ", "); // Alice, and, Bob
  }
  // 法 2: 使用 for-each loop
  for (String str: arrL) {
  	System.out.print(str + ", "); // Alice, and, Bob 
  }
  ```

- 排列元素

  ```java
  ArrayList<Integer> arrL = new ArrayList();
  list.add(2);
  list.add(4);
  list.add(3);
  list.add(1);
  
  System.out.println("Before sorting list:");
  System.out.println(arrL);
  // 運用 Collection 類別的 sort() 方法
  Collections.sort(arrL); // [2, 4, 3, 1]
  
  System.out.println("after sorting list:");
  System.out.println(arrL); // [1, 2, 3, 4]
  ```

- 取得元素

  ```java
  Integer n = list.get(1); // and
  ```


### 參考資料

- [GeeksForGeeks-ArrayList in Java](https://www.geeksforgeeks.org/arraylist-in-java/)
- [GeeksForGeeks-Array vs. ArrayList in Java](https://www.geeksforgeeks.org/array-vs-arraylist-in-java/)
- [GeeksForGeeks-How to find length or size of an Array in Java?](https://www.geeksforgeeks.org/how-to-find-length-or-size-of-an-array-in-java/)
