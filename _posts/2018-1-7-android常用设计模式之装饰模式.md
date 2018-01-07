>定义：装饰模式属结构型设计模式。动态地给一个对象添加一些额外的职责，就增加功能来说，装饰模式比生成子类更为灵活。

以下以王者荣耀中橘右京，普通攻击以及带有红BUFF的攻击为例。

## 装饰模式结构图
![装饰模式.jpg](http://upload-images.jianshu.io/upload_images/2229793-b4b6e9f206a76652.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在装饰模式中有如下角色：
- IAttack：攻击的接口类
- AbstractAttact：实现了攻击接口的抽象类，其持有被装饰类的引用
- RedBuff：红buff，AbstractAttact的实现类，装饰类
- OrangeYouJing：橘右京，被装饰类


## 代码：
```

public interface IAttack {
    void attack();
}

public abstract class AbstractAttact implements IAttack {
    private IAttack mAttact;

    public AbstractAttact(IAttack attack) {
        mAttact = attack;
    }

    @Override
    public void attack() {
        mAttact.attack();
    }
}

public class RedBuff extends AbstractAttact {
    public RedBuff(IAttack attack) {
        super(attack);
    }

    @Override
    public void attack() {
        super.attack();
        attackWithBuff();
    }

    private void attackWithBuff(){
        System.out.println("带有红buff的攻击");
    }
}

public class OrangeYouJing implements IAttack {

    @Override
    public void attack() {
        System.out.println("橘右京削橘子");
    }
}

public class DecoratorTest {
    private OrangeYouJing mOrangeYouJing;
    private RedBuff mRedBuff;
    @Before
    public void setUp() throws Exception {
        mOrangeYouJing = new OrangeYouJing();
        mRedBuff = new RedBuff(mOrangeYouJing);
    }

    @Test
    public void attack() throws Exception {
        mRedBuff.attack();
    }
}
```

## 使用场景
- 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。
- 需要动态地给一个对象添加功能，这些功能可以动态的撤销。
- 当不能采用集成的方式对系统进行扩充或者采用集成不利于系统拓展和维护时。

## 优点：
- 通过组合而非集成的方式，动态地扩展一个对象的功能，在运行时选择不同的装饰器，从而实现不同的行为。
- 有效避免了使用集成的方式拓展对象功能而带来的灵活性差，子类无限制扩张的问题。
- 具体组件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体组件类和具体装饰类，在使用时再对其进行组合，原有代码无需改变，符合“开放闭合原则”。

## 缺点：
- 比集成更加灵活激动的特性，也同时意味着装饰模式比集成更加易于出错，排错也很困难。对于多次装饰的对象，调试时宣召错误可能需要逐级排查，较为繁琐。所以，只在必要的时候使用装饰模式。
- 装饰层数不能过多，否则会影响效率。

**代码已上传**[github](https://github.com/zyl409214686/DesignPatterns)
