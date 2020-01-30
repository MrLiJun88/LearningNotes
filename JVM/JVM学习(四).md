# JVM学习(四)

## 1. Java中的四种引用类型

> 1. Strong Reference(强引用)
>
>    * 我们日常开发中所遇到的绝大多数引用均是强引用
>
>    * 如果对象拥有强引用，就表示它是可达的，那么垃圾收集器就不会将其回收
>
>    * 如果将某个强引用显式置为null，就表示该引用不再指向对象，若该对象没有其他引用指向它，那么在**适当时机**就会被垃圾收集器所回收
>
>    * 参考ArrayList源码
>
>      ```java
>      public void clear() {
>              modCount++;
>      
>              // clear to let GC do its work
>              for (int i = 0; i < size; i++)
>                  elementData[i] = null;
>      
>              size = 0;
>          }
>      ```
>
>    * 110