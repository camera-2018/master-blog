---
title: java答案2
date: 2022-9-19 23:05:44
tags:
---
### 选择

DDBDB  BDCAA  ADAAA



### 填空

4-1
    BufferedInputStream

4-2
    OutputStream
    Reader

### 程序填空题

5-1
    throw  new
    IllegalAccessException e

5-2
    int t a
    t

5-3
    f[i]=f[i-1]+f[i-2]
    int i=0
    (i+1)%4==0

### 编程题
7-1

    public class Main {
        public static void main(String[] args) {
            Dog animal = new Dog();
            animal.shout();
            animal.run();
        }
    }
    class Animal {
        void shout() {
            System.out.println("animal shout！");
        }
    }
    class Dog extends Animal {
        void shout() {
            super.shout();
            System.out.println("wangwang……");
        }

        void run() {
            System.out.println("Dog is running");
        }
    }


7-2

    public class Main {
        public static void main(String[] args) {
            Son son = new Son();
            son.method();
        }
    }
    class Parent {
        Parent() {
            System.out.println("Parent's Constructor without parameter");
        }

        Parent(boolean b) {
            System.out.println("Parent's Constructor with a boolean parameter");
        }

        public void method() {
            System.out.println("Parent's method()");
        }
    }
    class Son extends Parent {
        Son() {
            super(true);
            System.out.println("Son's Constructor without parameter");

        }
        public void method() {
            System.out.println("Son's method()");
            super.method();
        }
    }

7-3

    import java.util.Scanner;
    public class Main{

        public static void main(String[] args) {
            Scanner in=new Scanner(System.in);
            int n=in.nextInt();
            int sum1=0,sum2=0;
            int []s=new int[n];
            for(int i=0;i<n;i++) {
                int m=in.nextInt();
                if(m>100||m<0) {
                    System.out.println(m+"invalid!");
                    i--;
                    continue;
                }
                else
                    s[i]=m;
                if(m<60) sum1+=1;
                else sum2+=1;
            }
            System.out.println(sum2);
            System.out.println(sum1);
        }

    }

7-4

    import java.util.Arrays;
    import java.util.Scanner;
    class Main {
        public static void main(String[] args) {
            Scanner sc = new Scanner(System.in);
            int n = sc.nextInt();
            int[] arr = new int[n];
            for (int i = 0; i < n; i++) {
                arr[i] = sc.nextInt();
            }
            double sum = 0;
            for (int i = 0; i < n; i++) {
                sum += arr[i];
            }
            double mean = sum / n;
            arr = Arrays.stream(arr).sorted().toArray();
            double median = 0;
            if (n % 2 == 0) {
                median = (arr[n / 2 - 1] + arr[n / 2]) / 2.0;
            }
            else {
                median = arr[n / 2];
            }
            System.out.print("mean=" + String.format("%.2f", mean) + ",median=" + String.format("%.1f", median));
        }
    }

7-5

    import java.util.Arrays;
    import java.util.Scanner;
    public class Main {
        public static void main(String[] args) {
            Scanner scanner = new Scanner(System.in);
            int n = scanner.nextInt();
            int[] arr = new int[n];
            for (int i = 0; i < n; i++) {
                try{
                    String str = scanner.next();
                    arr[i] = Integer.parseInt(str);
                }catch (Exception e){
                    System.out.println(e);
                    i--;
                }
            }
            System.out.println(Arrays.toString(arr));
        }
    }
