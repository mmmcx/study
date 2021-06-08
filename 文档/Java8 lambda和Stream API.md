## Java8 lambda和Stream API

```java
package io.toman.service;

import org.apache.commons.lang3.StringUtils;

import java.util.*;
import java.util.function.Consumer;
import java.util.function.Function;
import java.util.function.Predicate;
import java.util.function.Supplier;
import java.util.stream.Collectors;
import java.util.stream.IntStream;
import java.util.stream.Stream;

public class TestJava8 {

    static List<Product> pros = Arrays.asList(
            new Product("张三","男",18),
            new Product("张三","女",19),
            new Product("李四","男",28),
            new Product("丫丫","女",18),
            new Product("测试","男",24)
    );

    public static void main(String[] args) {


//        test();
//
//        test2();
//        test3();
        test4();
    }

    public static void test4(){
        /**
         * Stream 流操作
         *
         * 1.创建流
         * 2.中间操作
         * 3.终止流
         *
         */
        //创建流
        //1.通过Collection系列集合提供的stream() 或parallelStream()
        List<String > list = new ArrayList<>();
        Stream<String> stream = list.stream();
        //2.通过Arrays中的静态方法stream()获取数组流
        Product[] pros2 = new Product[10];
        Stream<Product> stream1 = Arrays.stream(pros2);
        //3.通过Stream类中的静态方法of()
        Stream<String> stream3 = Stream.of("aaa", "bbb", "ccc");
        //4.创建无限流
        //迭代
//        Stream<Integer> iterate = Stream.iterate(0, x -> x + 2);
//        iterate.forEach(System.out::println);
        //生成
//        Stream.generate(Math::random)
//                .limit(5).forEach(System.out::println);
    //中间操作
        /**
         * 中间操作--筛选与切片
         * filter --接收lambda，从流中排除某些元素
         * limit--截断流，使其元素不超过给定数量
         * skip(n)--跳过元素，返回一个扔掉了前n个元素的流，若流中元素不足n个，则返回一个空流，与limit(n)互补
         * distinct--筛选，通过流所生成元素的hashcode和equal去除重复元素
         *
         */
        Stream<Product> productStream = pros.stream()
                .filter(p -> {
                    System.out.println("中间过滤操作");
                    return p.getAge() > 18;
                });
        //终止操作：一次性执行全部内容，即惰性求值
        productStream.forEach(System.out::println);
        System.out.println("-----------");
        pros.forEach(System.out::println);
        System.out.println("-----------");
        pros.stream()
                .limit(2)
                .forEach(System.out::println);

        System.out.println("skip-----------");
        pros.stream().skip(1)
                .forEach(System.out::println);

        System.out.println("distinct-----------");
        pros.stream().distinct().forEach(System.out::println);
        System.out.println("map映射--------------------");
        /**
         * 中间操作--映射
         * map--接收lambda，将元素转换成其他形式或提取信息，
         *      接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素
         * flatMap -- 接收一个函数作为参数，将流中的每个值都换成另外一个流，然后把所有流连城一个流
         * map 和 flatMap的区别类似于list.add(Object o)和 list.addAll(Collection coll)
         */
        List<String> stringList = Arrays.asList("aaa", "bbb", "ccc", "ddd");
        stringList.stream()
                .map(String::toUpperCase)
                .forEach(System.out::println);
        pros.stream()
                .map(Product::getName)
                .forEach(System.out::println);
        System.out.println("------流中流");
        Stream<Stream<Character>> streamStream = stringList.stream()
                .map(TestJava8::filterCharacter);

        streamStream.forEach(sm -> sm.forEach(System.out::println));
        System.out.println("flatmap---------------");

        stringList.stream()
                .flatMap(TestJava8::filterCharacter)
                .forEach(System.out::println);

        /**
         * 中间操作--排序
         * sorted() --- 自然排序(comparable)
         * sorted(Comparator com)--- 定制排序(Comparator)
         */
        System.out.println("自然排序----------------");
        stringList.stream()
                .sorted()
                .forEach(System.out::println);
        System.out.println("定制排序-------------------");
        pros.stream()
                .sorted((p1,p2) -> {
                    if(p1.getAge().equals(p2.getAge())){
                        return p1.getName().compareTo(p2.getName());
                    }else{
                        return Integer.compare(p1.getAge(),p2.getAge());
                    }
                })
                .forEach(System.out::println);

        //终止操作
        /**
         * 查找与匹配
         * allMatch--检查是否匹配所有元素
         * anyMatch--检查是否至少匹配一个元素
         * noneMatch--检查是否没有匹配所有元素
         * findFirst--返回第一个元素
         * findAny--返回当前流中的任意元素
         * count-- 返回流中元素的总个数
         * max-- 返回流中最大值
         * min--返回流中最小值
         */

        boolean b = pros.stream()
                .allMatch(e -> e.getAge() > 20);
        System.out.println("allMatch: " + b);

        boolean b1 = pros.stream().anyMatch(e -> e.getAge() == 19);
        System.out.println("anyMatch: " + b1);

        boolean b2 = pros.stream().noneMatch(e -> e.getAge() == 19);
        System.out.println("noneMatch: " + b2);
        Optional<Product> first = pros.stream()
                .sorted((e1, e2) -> -Integer.compare(e1.getAge(), e2.getAge()))
                .findFirst();
        System.out.print("findFirst: ");
        first.ifPresent(System.out::println);
        System.out.println("findAny --------------");
        pros.stream()
                .filter(x-> "男".equals(x.getSex()) && x.getAge()==28)
                .findAny().ifPresent(System.out::println);
        //规约 reduce(T identity,BinaryOperator)
        List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
        Integer reduce = list1.stream()
                .reduce(0, Integer::sum);
        System.out.println(reduce);
        //收集
        /**
         * collect --将流转换为其他形式，接收一个Collector接口的实现，用于给Stream中元素做汇总的方法
         *
         */
        System.out.println("collector---------");
        pros.stream()
                .map(Product::getName)
                .collect(Collectors.toList())
                .forEach(System.out::println);

//        Map<String, Integer> collect = pros.stream()
//                .collect(Collectors.toMap(Product::getName, Product::getAge));
        Map<String, Product> collect1 = pros.stream()
                .collect(Collectors.toMap(Product::getName, Function.identity(), (k1, k2) -> k2 ));

        collect1.forEach((k,v) -> System.out.println(k+"--"+ v));

        //平均值
        Double avg = pros.stream()
                .collect(Collectors.averagingInt(Product::getAge));
        System.out.println(avg);
        //Collectors.summarizingInt按int类型统计，
        // 返回结果：IntSummaryStatistics{count=4, sum=89, min=18, average=22.250000, max=28}
        IntSummaryStatistics summaryStatistics = pros.stream()
                .collect(Collectors.summarizingInt(Product::getAge));
        System.out.println(summaryStatistics);
        //总和
        Integer sum = pros.stream().mapToInt(Product::getAge).sum();
        System.out.println(sum);
        System.out.println("------");
        pros.stream()
                .map(Product::getAge)
                .reduce(Integer::sum).ifPresent(System.out::println);
        //最大值
        //Optional<Product> max = pros.stream().max((p1, p2) -> -Integer.compare(p1.getAge(), p2.getAge()));
        Optional<Product> max = pros.stream()
                .collect(Collectors.maxBy((p1, p2) -> Integer.compare(p1.getAge(), p2.getAge())));

        max.ifPresent(System.out::println);
        System.out.println("---====");
//        Optional<Product> min = pros.stream().min((p1, p2) -> Integer.compare(p1.getAge(), p2.getAge()));
        Optional<Integer> min = pros.stream()
                .map(Product::getAge)
                .collect(Collectors.minBy(Integer::compare));
        System.out.println(min);

        //分组
        Map<Integer, List<Product>> map = pros.stream()
                .collect(Collectors.groupingBy(Product::getAge));
        map.forEach((k,v) -> System.out.println(k+"--"+ v));
        //多级分组
        System.out.println("多级分组--------------------------");
        Map<String, Map<String, Map<Object, List<Product>>>> groupMap = pros.stream()
                .collect(Collectors.groupingBy(Product::getName, Collectors.groupingBy(p -> {
                    if (p.getAge() < 18) {
                        return "未成年";
                    } else if (p.getAge() >= 18 && p.getAge() < 25) {
                        return "青年";
                    } else {
                        return "老年";
                    }
                }, Collectors.groupingBy(e -> {
                    if ("男".equals(e.getSex())) {
                        return "男";
                    } else if ("女".equals(e.getSex())) {
                        return "女";
                    }
                    return "未知";
                }))));
        groupMap.forEach((k,v)->v.forEach((x,y)-> System.out.println(x+"--"+y)) );
        System.out.println("----------------------------------------------");
        groupMap.forEach((k,v) -> System.out.println(k+"---"+v));
        //分区
        System.out.println("分区-----------------------------------------------");
        Map<Boolean, List<Product>> partion = pros.stream()
                .collect(Collectors.partitioningBy(p -> p.getAge() < 20));
        partion.forEach((k,v) -> System.out.println(k+"--"+v));
        
        System.out.println("join ------------------------------------------------");
        //join操作
        String joinStr = pros.stream()
                .map(Product::getName)
                .collect(Collectors.joining());
        System.out.println("连接字符串: " + joinStr);
        String joinStr2 = pros.stream()
                .map(Product::getName)
                .collect(Collectors.joining(","));
        System.out.println("逗号分割："+ joinStr2);
        String joinStr3 = pros.stream()
                .map(Product::getName)
                .collect(Collectors.joining(",", "{", "}"));
        System.out.println("添加前后缀："+joinStr3);
        System.out.println("------------------------------------");

    }


    public static Stream<Character> filterCharacter(String str){
        List<Character> list = new ArrayList<>();
        for(Character ch : str.toCharArray()){
            list.add(ch);
        }
        return list.stream();
    }





    public static void test(){

        Runnable r = new Runnable() {
            @Override
            public void run() {
                System.out.println("test java8");
            }
        };
        r.run();

        System.out.println("-------------------------");
        Runnable r1 = () -> System.out.println("java8 run ");
        r1.run();
    }

    public static void test2(){

        Consumer<String> con = System.out::println;
        con.accept("我是Java8");
        pros.sort((p1, p2) -> {
            if (p1.getAge().equals(p2.getAge())) {
                return p1.getName().compareTo(p2.getName());
            } else {
                return Integer.compare(p1.getAge(), p2.getAge());
            }
        });
        pros.forEach(System.out::println);

    }

    public static void test3(){
        /**
         * Java8 内置四大核心函数式接口
         * Consumer<T> :消费型接口
         *          void accpet(T t)
         *
         * Supplier<T> :供给型接口
         *          T get()
         * Function<T,R> : 函数型接口
         *          R apply(T t)
         * Predicate<T>: 断言型接口
         *          boolean test(T t)
         *
         */
        //Consumer<T> accpet(T t)
        happy(1000, x -> System.out.println("消费" + x));
        //Suppiler<T> T get()
        getNumberList(10, () -> (int) (Math.random() * 100)).forEach(System.out::println);

        //Function<T,R> R apply(T t)
        String handleStr = handleStr("abscdf", String::toUpperCase);
        System.out.println(handleStr);

        //Predicate<T> boolean test(T t)
        List<String> strings = filterStr(Arrays.asList("asd", "weqrrr", "sdfffff", "nbfjfn"), (x) -> x.length() > 3);
        strings.forEach(System.out::println);
        //方法引用，若lambda体中的内容有方法已经实现了，我们可以使用方法引用

    }

    public static List<String> filterStr(List<String> strList, Predicate<String> pre){
        List<String> list = new ArrayList<>();

        for (String str : strList){
            if(pre.test(str)){
                list.add(str);
            }
        }
        return list;
    }


    public static String handleStr(String str ,Function<String,String> fun){
        return fun.apply(str);
    }

    public static List<Integer> getNumberList(int num, Supplier<Integer> sup){
        List<Integer> list = new ArrayList<>();
        for(int i =0 ; i< num ;i ++){
            list.add(sup.get());
        }
        return list;
    }

    public static void happy(double moeny , Consumer<Double> con){
        con.accept(moeny);
    }

    static class Product{

        private String name;

        private String sex;

        private Integer age;

        public Product(){}

        public Product(String name, String sex, Integer age) {
            this.name = name;
            this.sex = sex;
            this.age = age;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getSex() {
            return sex;
        }

        public void setSex(String sex) {
            this.sex = sex;
        }

        public Integer getAge() {
            return age;
        }

        public void setAge(Integer age) {
            this.age = age;
        }

        @Override
        public String toString() {
            return "Product{" +
                    "name='" + name + '\'' +
                    ", sex='" + sex + '\'' +
                    ", age=" + age +
                    '}';
        }
    }

}

```

