# 观察者模式

> 定义：在对象之间定义一对多的依赖，这样一来，当一个对象改变状态，依赖它的对象都会收到通知，并自动更新。

主题定义了一对多的依赖，当主题更新时，依赖它的观察者会被通知，可以选择由主题***push***信息，或者观察者主动***pull***信息，文中指出"push"更正确。

Java中有对观察者模式的内置支持，它支持push也支持pull。java.util.Obervable（主题或可观察者）和java.util.Oberver（观察者），前者是一个类，
后者是一个接口。

### 代码例子：
这里我们自己写观察者模式，不用java内置的Observable和Observer，java内置的观察者模式的支持与这大同小异
```java
    // 主题接口
    public interface Subject {
        // 这里的Observer观察者是用来被注册或删除的
        public void registerObserver(Observer o);
        public void removeObserver(Observer o);
        // 通知Observer
        public void notifyObservers();
    }

    // 观察者接口
    public interface Observer{ 
        // 当主题对象状态改变时，会通知观察者对象（调用update传数据给观察者）
        public void update(float temp, float humidity, float pressure);
    }
    
    // 显示接口
    public interface DisplayElement{
        // 展示数据
        public void display();
    }
```

主题类
```java
    // 这是个用观察者设计的WeatherData类
    public class WeatherData implements Subject {
        // 用来记录观察者的容器
        private ArrayList observers;
        private float temperature;
        private float humidity;
        private float pressure;
        
        // 构造方法中建立一个用观察者容器
        public WeatherData() {
            observers = new ArrayList();
        }
        
        // 观察者会调用这个方法来注册自己到主题中去
        public void registerObserver(Observer o) {
            observers.add(o);
        }
        
        // 观察者会调用这个方法来从主题中注销自己
        public void removeObserver(Observer o) {
            int i = observers.indexOf(o);
            if (i >= 0) {
                observers.remove(i);
            }
        }
        
        // 主题逐个通知观察者，调用了观察者update方法
        public void notifyObservers() {
            for (int i = 0; i < observers.size(); i++) {
                Observer observer = (Observer)observers.get(i);
                observer.update(temperature, humidity, pressure);
            }
        }
        
        // 知道数据改变了，去通知观察者们
        public void measurementsChanged() {
            notifyObservers();
        }
        
        // 这个方法属于测试方法，用来模拟主题对象状态改变，从而去通知观察者的这个过程
        public void setMeasurements(float temperature, float humidity, float pressure) {
            // 状态改变了
            this.temperature = temperature;
            this.humidity = humidity;
            this.pressure = pressure;
            // 快去通知观察者
            measurementsChanged();
        }
        
        // WeatherData的其他方法，getter，setter之类的
    }
```

观察者类
```java
    public class CurrentConditionDisplay implements Observer, DisplayElement {
        private float temperature;
        private float humidity;
        private Subject weatherData;
        
        // 这里观察者需要用到主题对象，来注册自己
        public CurrentConditionDisplay(Subject weatherData) {
            this.weatherData = weatherData;
            weatherData.registerObserver(this);
        }
        
        // update被调用时，保存数据，然后展示数据
        public void update(float temperature, float humidity, float pressure) {
            this.temperature = temperature;
            this.humidity = humidity;
            display();
        }
        
        public void display() {
            sout(temperature+","+humidity);
        }
    }
```

测试类
```java
    public class WeatherStation {
        public static void main(String[] args) {
            // 首先创建一个主题
            WeatherData weatherData = new WeatherData();
            
            // 创建了一个观察者，并且在主题中注册了自己，现在自己就可以收到主题发送的通知了
            CurrentConditionDisplay currentDisplay = new CurrentConditionDisplay(weatherData);
            
            // 模拟状态改变
            weatherData.setMeasurements(80, 65, 30.4f);
            weatherData.setMeasurements(82, 67, 28.4f);
        }
    }
```
