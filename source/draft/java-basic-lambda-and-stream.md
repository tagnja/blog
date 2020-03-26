Java Lambda and Stream

### what is lambda expression?

Implements anonymous inner class of interface that has a single method and have annotation `@FuncationalInterface` can using lambda expression.

Example 1

```java
Thread thread = new Thread(new Runnable(){
    void run(){
        System.out.println("annoymous inner class");
    }
});
```

```java
Thread thread = new Thread(()->{
    System.out.println("lambda expression");
});
```

Example 2

```java
File dir = new File("{dir_path}");
File[] files = dir.listFiles(new FileFilter(){
    @Override
    public boolean accept(File file){
        return file.getName().endsWith(".log");
    }
});
```

```java
File dir = new File("{dir_path}");
File[] files = dir.listFiles((file)->{
    return file.getName().endsWith(".log");
});
```

### What is Method Reference?