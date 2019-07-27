# 策略模式
> 定义：策略模式定义了算法族，分别封装起来，让它们之间可以相互替换，此模式让算法的变化独立于使用算法的客户。

我认为定义中的算法族，也可以理解成为业务实现代码族。

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

在这个例子中，ModelDuck继承了Duck超类，Duck类本身有游泳、展示、飞、叫的行为，但是有的鸭子不会飞、有的鸭子不会叫，
所以将这两个行为单独抽取出来，这里用到了***封装变化***的设计原则，又将这两个行为设计成成员变量，这里就是***组合***，
用了***多用组合少用继承***的设计原则，而这两行为成员变量，设计成接口，再由具体的行为实现类来实现这两个接口，
这里用了***针对接口编程，针对接口编程也可以说是针对超类型（supertype）编程***。

为什这样写呢？假如现在有新的需求出现了，有一只竹蜻蜓鸭子，他头顶竹蜻蜓，会狗叫，那么要怎么编码呢？

只需要简单的编写一个用竹蜻蜓飞行的行为实现类和狗叫的行为实现类分别实现飞行接口和叫接口，再给竹蜻蜓鸭设置这两个行为就好了。

### “有一个”可能比“是一个”更好
“有一个”的关系：每一个鸭子都有一个飞行行为和叫行为，好用来将飞行和嘎嘎叫委托给它们代为处理，当你将鸭子类和行为类结合起来用时，就是上面的关系，
***组合***（composition），这样子的做法，鸭子的行为不是继承来的，是和对象组合得来的。

那么当你的鸭子行为发生变化时，你不用去修改鸭子类，你只需要修改行为类，并且你可以去动态的赋予鸭子不同行为，提升了系统的弹性。
假设这里用继承得来鸭子行为，那么当鸭子的行为需要变化的时候，你需要修改鸭子类，如果要改很多处地方的话，就会相当的麻烦。

![duck](https://github.com/lsqg/StudyNotes/blob/master/Design%20Patterns/Strategy%20Pattern/duck.png)

这个技巧就是***多用组合，少用继承***，不过组合也有缺点
> ***不是只有继承才可以实现代码的复用。***
