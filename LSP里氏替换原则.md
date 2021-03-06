**定义**:所有引用基类的地方必须能透明的使用其子类的对象。通俗的讲就是只要父类能出现的地方子类就可以出现，而且替换为子类也不会产生任何错误和异常。

通过里氏替换原则定义,其中包含了4层含义:  
1. **子类必须完全实现父类的方法。**  
   定义一个接口或者抽象类,然后编码实现。调用类则直接传入接口或者抽象类,这其实已经使用了里氏替换原则。代码如下：
   ```cpp
   //枪支抽象类
   class AbstractGun{
      public:
      AbstractGun(){};
      virtual ~AbstractGun(){}; 
      virtual void Shoot()=0;//挂电话 
   }
   
   //手枪
   class Handgun :public AbstractGun{
      public:
      void Shoot(){
        print("the Handgun shoot");
      }
   }
   
   //步枪
   class Rifle :public AbstractGun{
      public:
      void Shoot(){
        print("the Rifle shoot");
      }
   }
   
   //机枪
   class MachineGun :public AbstractGun{
      public:
      void Shoot(){
        print("the MachineGun shoot")
      }
   }
   
   //士兵类
   class Soldier {
      public:
      void setGun(AbstractGun _gun){
          this.gun = _gun;
      }
      void killEnemy(){
          print("the Soldier begin shoot");
          gun.Shoot();
      }
      private:
      AbstractGun gun;
   };
   
    int main(){
        Soldier someone = new Soldier();
        someone.setGun(new Handgun());
        someone.killEnemy()
        return  0;
    }
   ```  
   在Soldier类我们创建了AbstractGun类型的成员变量gun。它可以代表其任意子类。如果我们需要士兵使用不同的类型的枪,给其传入对应的枪械类型即可。
   而不用去动Soldier类中的代码逻辑。  
   如果此时有把玩具枪,但是它并没有射击方法。但是可能继承抽象类AbstractGun中的其他方法。让玩具枪继承AbstractGun代码如下:
   ```cpp
   //机枪
   class ToyGun :public AbstractGun{
      public:
      void Shoot(){
        print("the ToyGun canot shoot")
      }
   }
   
   int main(){
        Soldier someone = new Soldier();
        someone.setGun(new ToyGun());
        someone.killEnemy()
        return  0;
    }
   ```
   通过ToyGun去调用Shoot()函数,导致业务已经不能正常的运行。由此可以看出在常规设计中按照继承规则，ToyGun继承AbstractGun没有问题。但是在本例具体应用场景
   中，子类并不能完全实现父类的业务。导致了业务调用失败的情况。因此如果子类不能完整的实现父类的方法,或者父类的某些方法在子类中已经发生"畸变"。则需要断开
   继承关系。采用依赖,聚集，组合的方式代替继承。
   
2. **子类可以有自己的个性。**  
   在子类出现的地方,父类未必就可以胜任。里氏替换原则不可反过来使用。代码如下
   ```cpp
   //狙击枪类
   class AUG :public Rifle{
      public:
      void ZoomOut(){
         print("open zoomout")
      }
      
      void Shoot(){
        print("the AUG shoot")
      }
   }
   
   //狙击手类
      class Snipper {
      public:
      void killEnemy(AUG aug){
          print("the aug begin shoot");
          aug.ZoomOut();
          aug.Shoot();
      }

   };
   
   int main(){
        Snipper someone = new Snipper();
        someone.killEnemy(new AUG())
        return  0;
    }
   
   ```
   狙击手依赖狙击枪,我们给其传入AUG类型实例,业务能正常的运行。如果将AUG类型替换为父类Rifle呢？代码如下:
   ```cpp
    int main(){
        Snipper someone = new Snipper();
        someone.killEnemy(new Rifle())
        return  0;
    }
   ```
   在Rifle类中是没有ZoomOut函数的，程序不能正常的运行。子类是对基类的属性或者方法的扩展和补充，因此子类拥有的属性和方法是大于等于基类的。在上述场景中由
   于基类没有对应子类的函数，导致程序运行失败。所以有子类出现的地方基类未必就可以出现。
   
3. **覆盖或者实现父类的方法输入的参数可以放大。**  
   ```cpp
   class Father {
       public:
       virtual void DoSomething(HashMap map){
         print("in Father class");
       };
   }
   
   class Son : public Father {
       public:
       void DoSomething(Map map){
         print("in Son class");
       };
   }
   
    int main(){
        Father f = new Father();
        HashMap map = new HashMap();
        f.DoSomething(map);
        return  0;
    }
   ```
   上述函数运行结果为"in Father class"。按照里氏替换原则,父类能出现的地方子类也能出现。将上述Father类改为Son类
   ```cpp
   int main(){
        Son f = new Son();
        HashMap map = new HashMap();
        f.DoSomething(map);
        return  0;
    }
   ```
   运行结果为"in Father class"。因为子类的参数Map类型比父类HashMap类范围大,这时子类代替父类传递到调用者中,子类的方法永远都不会执行。如果想让子类执行,    那么子类必须重写父类的方法。假如子类的参数类型范围比父类的参数类型范围小即Son类的参数为HashMap类型，父类的参数为Map类型。这样就会导致子类在没有重写
   父类的函数前提下，子类的方法被执行了,从而引起业务逻辑混乱。
   
4. **覆盖或者实现父类的方法输出的结果可以被缩小。**   
   父类的一个函数的返回值是一个类型T,子类相同的函数(重写或者重载)的返回值是S。里氏替换原则就要求S必须小于或等于T,即S要么与T是同一类型，要么S是T的子类。
   如果是重写,子类与父类的同名函数输入的参数是相同的，两个函数的返回值S小于或等于T,这是重写的要求。如果是重载,两个函数参数不同,根据里氏替换原则子类的参    数类型范围要大于父类的参数类型范围,那么子类的函数将不会被执行。
   
**总结**:在实际项目中,通过子类来对应不同的业务含义，使用父类作为参数，传递不同的子类完成不同的业务逻辑。   
   

