# 已修复:Java 中的麻烦请帮忙，我的老师已经忽略我的问题快一周了

> 原文：<https://dev.to/drakendel/troubles-in-java-please-help-my-teacher-has-been-ignoring-my-questions-for-almost-a-week-3i8h>

包 com . eclipse . chance walker；

公有类 rectangleCW {
私有静态双宽度；
私静双高；

```
public rectangleCW() {
    width = 1;
    height = 1;
}

public rectangleCW(double w, double h) {
    width = w;
    height = h;
}

public double getArea() {
    return width * height;
}

public double getPerimeter() {
    return width + height + width + height;
} 
```

## }

包 com . eclipse . chance walker；

public class rectangleTestCW {
public static void main(String[]args){

```
 rectangleCW r1 = new rectangleCW(4, 40);
    rectangleCW r2 = new rectangleCW(3.5, 35.9);

    System.out.println("The height for rectangle 1 is: " + 40);
    System.out.println("The width for rectangle 1 is: " + 4);
    System.out.println("The area for rectangle 1 is: " + r1.getArea());
    System.out.println("The perimeter for rectangle 1 is: " + r1.getPerimeter());

    System.out.println("The height for rectangle 2 is: " + 359);
    System.out.println("The width for rectangle 2 is: " + 3.5);
    System.out.println("The area for rectangle 2 is: " + r2.getArea());
    System.out.println("The perimeter for rectangle 2 is: " + r2.getPerimeter());
    } 
```

}