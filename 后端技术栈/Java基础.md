#### 一、设计模式

##### 1. 常用的设计模式
- 工厂模式：Spring IOC容器就是一个大的工厂，所有的Bean实例都是放在Spring容器里面；
- 单例模式：Spring Bean 默认的作用域都是单列
- 代理模式：Spring AOP功能的实现就是用到了代理模式，Spring AOP生成一些代理对象，做一定的增强，然后我们对目标对象的访问基于这个代理对象去访问；
- 模板方法模式：Spring中的jdbcTemplate、hibernateTemplate就是使用的模板方法模式；
- 观察者模式：Spring事件驱动模型就是使用了观察者模式；
- 包装器设计模式：我们的项目需要连接多个数据，
- 适配器模式：Spring AOP的增强或通知（Advice）使用到了设配器、Spring MVC中也用到了适配器模式Controller
- 装饰者模式：
- 策略设计模式：Spring框架中资源访问接口就是基于策略设计模式实现的。

##### 2. 设计模式常用场景
- **策略模式**
>假如我们需要对前端上传的文件做类型匹配嘛， 传统做法就是if else 做分支判断，执行相对应得文件解析。
>**会出现的问题**：分支变多了，代码就会变得臃肿，难以维护，可读性变低。如果需要接入一种新类型得话，就得修改原有代码。违背了面向对象编程的开闭原则以及单一原则。
>**开闭原则**（对扩展开放，对修改关闭）：增加或删除某个逻辑，都需要修改到原来的代码
>**单一原则**（规定一个类应该只有一个变化的原因）：修改任何类型的分支逻辑代码，都需要改动当前类的代码。
>可以使用策略模式，可以编写一个接口，接口包含一个判断文件类型的字段，一个对应文件算法处理抽象方法，具体实现交由子类去实现。
>**略模式的使用**：借助Bean的生命周期，使用ApplicationContextAware接口，把对应的策略放入到map里面，然后堆外提供resolveFile方法。

- 责任链模式
> 假如商品下单， 我们回去校验参数、做非空判断、然后黑名单、规则拦截。平常操作是采用if，然后抛异常什么来做处理。可以使用策略模式，编写一个接口或者抽象类，抽象类种需要，一个指向下一个策略的属性，一个设置下一个对象的set方法，一个给子类实现差异化的方法，一个具体参数拦截逻辑

```java
public abstract class AbstractHandler {
    //责任链中的下一个对象
    private AbstractHandler nextHandler;
    /**
     * 责任链的下一个对象
     */
    public void setNextHandler(AbstractHandler nextHandler){
        this.nextHandler = nextHandler;
    }
    /**
     * 具体参数拦截逻辑,给子类去实现
     */
    public void filter(Request request, Response response) {
        doFilter(request, response);
        if (getNextHandler() != null) {
            getNextHandler().filter(request, response);
        }
    }
    public AbstractHandler getNextHandler() {
        return nextHandler;
    }
     abstract void doFilter(Request filterRequest, Response response);
}
```
子类实现差异化
```java
/**
 * 参数校验对象
 **/
@Component
@Order(1) //顺序排第1，最先校验
public class CheckParamFilterObject extends AbstractHandler {
    @Override
    public void doFilter(Request request, Response response) {
        System.out.println("非空参数检查");
    }
}
/**
 *  安全校验对象
 */
@Component
@Order(2) //校验顺序排第2
public class CheckSecurityFilterObject extends AbstractHandler {
    @Override
    public void doFilter(Request request, Response response) {
        //invoke Security check
        System.out.println("安全调用校验");
    }
}
```
将对象链接起来，形成一个条责任链
```java
@Component("ChainPatternDemo")
public class ChainPatternDemo {
    //自动注入各个责任链的对象
    @Autowired
    private List<AbstractHandler> abstractHandleList;
    private AbstractHandler abstractHandler;
    //spring注入后自动执行，责任链的对象连接起来
    @PostConstruct
    public void initializeChainFilter(){
        for(int i = 0;i<abstractHandleList.size();i++){
            if(i == 0){
                abstractHandler = abstractHandleList.get(0);
            }else{
                AbstractHandler currentHander = abstractHandleList.get(i - 1);
                AbstractHandler nextHander = abstractHandleList.get(i);
                currentHander.setNextHandler(nextHander);
            }
        }
    }
    //直接调用这个方法使用
    public Response exec(Request request, Response response) {
        abstractHandler.filter(request, response);
        return response;
    }
    public AbstractHandler getAbstractHandler() {
        return abstractHandler;
    }
    public void setAbstractHandler(AbstractHandler abstractHandler) {
        this.abstractHandler = abstractHandler;
    }
}
```

##### 3. 设计模式的七大原则
- **单一职责原则**：
- **接口隔离原则**：使用多个隔离的接口来降低耦合度
- **依赖倒转原则**：这个是开闭原则的基础，对接口编程，依赖与抽象而不是以来具体
- **里氏替换原则**：只有当衍生类可以替换掉基类，软件单位的功能不受到影响时，基类才能真正被复用，而衍生类也能够在基类的基础上增加新的行为。
- **开闭原则ocp**：对扩展开放，对修改关闭
- **迪米特法则（最少知道原则）**：一个实体应当尽量少的与其他实体之间发生相互作用，是的系统功能模块相对独立。
- **合成复用原则**：尽量使用合成/聚合的方式，而不是使用继承。继承实际上是破坏了类的封装性，超类的方法可能会被子类破坏。