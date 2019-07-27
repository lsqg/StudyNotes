# 策略模式
> 定义：策略模式定义了算法族，分别封装起来，让它们之间可以相互替换，此模式让算法的变化独立于使用算法的客户。

我认为定义种的算法族，也可以理解成为业务实现代码族。

### 代码例子
```java
/**
 * 鸭子的超类
 */
public abstract class Duck {
    // 这是两个行为接口，所有鸭子子类（同一package下）都继承这两个行为接口
    FlyBehavior flyBehavior;
    QuackBehavior quackBehavior;
    
    public abstract void display();
    public void swim() {
        sout("i can swim");
    }
    
    public void performFly() {
        flyBehavior.fly();   //委托给行为类
    }

    public void performQuack() {
        quackBehavior.quack();    //委托给行为类
    }
    
    // 这两个方法可以给鸭子设定行为
    public void setFlyBehavior(FlyBehavior fb) {
        flyBehavior = fb;
    }
    
    public void setQuackBehavior(quackBehavior qb) {
        quackBehavior = qb;
    }
}
```
---
```java
/**
 * 飞行行为接口
 */
public interface FlyBehavior {
    public void fly();
}

/**
 * 具体的飞行行为
 * 用翅膀飞
 */
public class FlyWithWings implements FlyBehavior {
    public void fly() {
        sout("fly with wings");
    }
}

/**
 * 具体的飞行行为
 * 不会飞
 */
public class FlyNoWay implements FlyBehavior {
    public void fly() {
        sout("can't fly");
    }
}
```
---
```java
/**
 * 叫行为接口
 */
public interface QuackBehavior {
    public void quack();
}

/**
 * 具体的叫行为
 */
public class Quack implements QuackBehavior {
    public void quack() {
        sout("quack");
    }
}

/**
 * 具体的叫行为
 */
public class MuteQuack implements QuackBehavior {
    public void quack() {
        sout("silence");
    }
}
```

```java
public class ModelDuck extends Duck {
    public ModelDuck() {
        flyBehavior = new FlyNoWay();
        quackBehavior = new Quack();
    }
    
    public void display(){
        sout("i am a model duck")
    }
}

```
---
```java
/**
 * 测试类
 */
public class Test {
    public static void main(String[] args) {
        Duck modelDuck = new ModelDuck();
        modelDuck.performFly(); //调用这个方法时，会委托flyBehavior对象，调用flyBehavior对象的fly方法，在这里flyBehavior对象就是FlyNoWay对象
        modelDuck.setFlyBehavior(new FlyWithWings()); //动态的给鸭子设置行为，flyBehavior对象变为FlyWithWings对象
        modelDuck.performFly(); //调用这个方法时，会委托flyBehavior对象，调用flyBehavior对象的fly方法，在这里flyBehavior对象是FlyWithWings对象
    }
}
```
