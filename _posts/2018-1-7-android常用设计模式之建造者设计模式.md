>定义：建造者设计模式属于创建型设计模式。将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

##### 建造者结构图：
![builder模式结构图.png](http://upload-images.jianshu.io/upload_images/2229793-bb88b3611c343cbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在建造者模式中有如下角色：
- Product：产品类
- Builder：build建造者类，通过它构建产品类

##### demo&代码

```
public class Computer {
    private String cpu;
    private String ram;
    private String mainboard;
    private Computer(Builder builder) {
        setCpu(builder.cpu);
        setRam(builder.ram);
        setMainboard(builder.mainboard);
    }
    public String getCpu() {
        return cpu;
    }
    public void setCpu(String cpu) {
        this.cpu = cpu;
    }
    public String getRam() {
        return ram;
    }
    public void setRam(String ram) {
        this.ram = ram;
    }
    public String getMainboard() {
        return mainboard;
    }
    public void setMainboard(String mainboard) {
        this.mainboard = mainboard;
    }
    @Override
    public String toString() {
        return (cpu!=null?("cpu:" + cpu):"") + (ram!=null?(" ,ram" + ram):"") + (mainboard!=null?(" ,mainboard" + mainboard):"");
    }

    static class Builder {
        private String cpu;
        private String ram;
        private String mainboard;
        public Builder cpu(String val) {
            cpu = val;
            return this;
        }
        public Builder ram(String val) {
            ram = val;
            return this;
        }
        public Builder mainboard(String val) {
            mainboard = val;
            return this;
        }
        public Computer build() {
            return new Computer(this);
        }
    }
}
```

##### 使用场景
- 当创建复杂对象的算法应该独立于该对象的组成部分以及它们的装配方式时。
- 遇到多个构造器参数时要考虑用构建器。静态工厂和构造器有个共同的局限性：它们都不能很好地扩展到大量的可选参数。
##### 优点
- 使用建造者模式可以使客户端不必知道产品内部组成细节。
- 具体的建造者之间是互相独立的， 容易扩展。
- 由于具体的建造者是独立的， 因此可以对建造过程逐步细化，而不对其他的模块产生任何影响。

##### 缺点
- 产生多余的Build对象

**代码已上传**[github](https://github.com/zyl409214686/DesignPatterns)
