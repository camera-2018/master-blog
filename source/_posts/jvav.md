---
title: java答案
date: 2022-9-19 23:04:59
tags:
---
### 选择

BDCDA CABCC

### 程序填空

5-1


    str.toCharArray()
    i<x.length
    ch>='a'&&ch<='z' ||ch>='A'&&ch<='Z'
    ch>='0'&&ch<='9'
    c++


5-2


    sc.nextLine()
    str.charAt(i)
    String.valueOf(curChar).toUpperCase()
    String.valueOf(curChar).toLowerCase()
    sb.append(s1)
    sb.toString()




5-3


    super(a);
    this.a=super.a*10;
    super.a
    this.a


### 编程题

7-1



    import java.util.Scanner;

    class Rectangle {
        double width = 1;
        double height = 1;

        Rectangle() {
        }

        Rectangle(double width, double height) {
            this.width = width;
            this.height = height;
        }

        double getArea() {
            return width * height;
        }

        double getPerimeter() {
            return 2 * (width + height);
        }
    }
    class Main{
        public static void main(String[] args) {
            Scanner scanner = new Scanner(System.in);
            double width = scanner.nextDouble();
            double height = scanner.nextDouble();
            Rectangle rectangle1 = new Rectangle(width, height);
            System.out.println(rectangle1.width + " " + rectangle1.height + " " + rectangle1.getArea() + " " + rectangle1.getPerimeter());
            width = scanner.nextDouble();
            height = scanner.nextDouble();
            Rectangle rectangle2 = new Rectangle(width, height);
            System.out.print(rectangle2.width + " " + rectangle2.height + " " + rectangle2.getArea() + " " + rectangle2.getPerimeter());
        }
    }


7-2



    import java.util.Scanner;

    interface ICompute{
        int computer(int n, int m);
    }

    class Add implements ICompute{
        public int computer(int n, int m){
            return n+m;
        }
    }

    class Sub implements ICompute{
        public int computer(int n, int m){
            return n-m;
        }
    }

    class Main{
        public static void main(String[] args){
            Scanner sc = new Scanner(System.in);
            int a = sc.nextInt();
            int b = sc.nextInt();
            ICompute add = new Add();
            ICompute sub = new Sub();
            System.out.println(add.computer(a, b));
            System.out.println(sub.computer(a, b));
        }
    }


7-3


    import java.util.Scanner;

    class Circle{
        private double radius;
        Circle(double radius){
            this.radius = radius;
        }
        Circle(){
            this.radius =0;
        }
        void setRadius(double r){
            this.radius = r;
        }
        double getRadius(){
            return this.radius;
        }
        double getArea(){
            return Math.PI * this.radius * this.radius;
        }
        double getPerimeter(){
            return Math.PI * this.radius * 2;
        }
        public String toString(){
            return"Circle(r:"+this.radius+")";
        }
    }

    class Cylinder{
        private double height;
        private Circle circle;
        Cylinder(double height,Circle circle){
            this.height = height;
            this.circle = circle;
        }
        Cylinder(){
            this.height = 0;
            this.circle = new Circle();
        }
        void setHeight(double height){
            this.height = height;
        }
        double getHeight(){
            return this.height;
        }
        void setCircle(Circle circle){
            this.circle = circle;
        }
        Circle getCircle(){
            return this.circle;
        }
        double getArea(){
            return 2 * this.circle.getArea() + this.circle.getPerimeter() * this.height;
        }
        double getVolume(){
            return this.circle.getArea() * this.height;
        }
        public String toString(){
            return "Cylinder(h:"+this.height+","+this.circle+")";
        }

    }

    public class Main{
        public static void main(String args[]) {
            Scanner input = new Scanner(System.in);
            int n = input.nextInt();
            for(int i = 0; i < n; i++) {
                String str = input.next();
                if(str.equals("Circle")) {
                    Circle c = new Circle(input.nextDouble());
                    System.out.println("The area of " + c.toString() + " is " + String.format("%.2f",c.getArea()));
                    System.out.println("The perimeterof " + c.toString() + " is "+ String.format("%.2f",c.getPerimeter()));
                } else if(str.equals("Cylinder")) {
                    Cylinder r = new Cylinder(input.nextDouble(), new Circle(input.nextDouble()));
                    System.out.println("The area of " + r.toString() + " is " + String.format("%.2f",r.getArea()));
                    System.out.println("The volume of " + r.toString() + " is " + String.format("%.2f",r.getVolume()));
                }
            }
        }
    }


7-4

	


    import java.util.Scanner;

    class Shape{
        double PI=3.1415926;
        public double area(){
            return 0;
        }
        public double perimeter(){
            return 0;
        }
    }

    class Oval extends Shape{
        private double a;
        private double b;
        public Oval(double a,double b){
            this.a=a;
            this.b=b;
        }
        public Oval(){
            this.a=0;
            this.b=0;
        }
        public double area(){
            return PI*a*b;
        }
        public double perimeter(){
            return 2*PI*Math.sqrt((a*a+b*b)/2);
        }
        public String toString(){
            return "Oval(a:"+a+",b:"+b+")";
        }
    }

    public class Main {
        public static void main(String[] args) {
            Scanner sc=new Scanner(System.in);
            double a=sc.nextDouble();
            double b=sc.nextDouble();
            Oval o=new Oval(a,b);
            System.out.println("The area of "+o.toString()+" is "+o.area());
            System.out.println("The perimeterof "+o.toString()+" is "+o.perimeter());
        }
    }


7-5

	
    import java.util.Scanner;

    public class Main {
        public static void main(String[] args) {
            String statement;
                Scanner sc =new Scanner(System.in);
                statement=sc.nextLine();
                String word1=sc.next();
                String word2=sc.next();//用word2替换word1

                String words[]=statement.split(" ");
            /* for(int i=0;i<words.length;i++)
                    System.out.print(words[i]+"\t");*/
                for(int i=0;i<words.length;i++) {//将句子分割成单词
                    if(word1.equals(words[i])) {
                        words[i]=word2;
                    }
                }
            StringBuffer stb=new StringBuffer();
            String sta=null;
            for(int i=0;i<words.length;i++) {//数组转换为句子
                if(i==words.length-1)stb.append(words[i]);
                else	stb.append(words[i]+" ");
            }
            sta=stb.toString();
            System.out.println(sta);
                sc.close();
        }

    }



7-6


    import java.util.Scanner;

    interface Shape{
        double length();
    }

    class Triangle implements Shape{
        double a;
        double b;
        double c;

        Triangle(){
            this.a = this.b = this.c = 0;
        }

        Triangle(double a){
            this.a = this.b = this.c = 0;
        }

        Triangle(double a, double b){
            this.a = this.b = this.c = 0;
        }

        Triangle(double a, double b, double c){
            this.a = a;
            this.b = b;
            this.c = c;
            if(a <= 0 || b <= 0 || c <= 0 || !(a + b > c && a + c > b && b + c > a)){
                this.a = this.b = this.c = 0;
            }
        }

        @Override
        public double length() {
            return a + b + c;
        }
    }

    class Rectangle implements Shape{
        double a, b;

        Rectangle(){
            this.a = this.b = 0;
        }

        Rectangle(double a){
            this.a = this.b = 0;
        }

        Rectangle(double a, double b){
            this.a = a;
            this.b = b;
            if(a <= 0 || b <= 0){
                this.a = this.b = 0;
            }
        }

        @Override
        public double length() {
            return 2 * (a + b);
        }
    }

    class Circle implements Shape{
        double r;

        Circle(){
            r = 0;
        }

        Circle(double r){
            this.r = r;
            if(r <= 0) this.r = 0;
        }

        @Override
        public double length() {
            return 2 * 3.14 * r;
        }
    }

    public class Main {
        public static void main(String[] args) {
            Scanner sc = new Scanner(System.in);

            while(sc.hasNextInt()) {
                double[] l = new double[5];

                String s;
                s = sc.nextLine();
                String[] str = s.split(" ");

                int cnt = 0;
                for (String value : str) {
                    l[cnt++] = Double.parseDouble(value);
                }


                if(cnt == 1){
                    Circle C = new Circle(l[0]);
                    System.out.printf("%.2f\n", C.length());
                }

                else if (cnt == 2){
                    Rectangle r = new Rectangle(l[0], l[1]);
                    System.out.printf("%.2f\n", r.length());
                }
                else{
                    Triangle t = new Triangle(l[0], l[1], l[2]);
                    System.out.printf("%.2f\n", t.length());
                }
            }
        }
    }



