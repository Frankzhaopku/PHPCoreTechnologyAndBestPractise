##相关知识点总结：
###对象
* PHP中对象的序列化存储与数组类似，只存储了对象属性，另外多一个指向该类的指针，以可以访问该对象对应的类函数，函数被加载在共享的类代码空间中，由所有该类的对象共享，故反序列化对象时需要该对象类的定义，否则无法返回正确的结果。
* PHP中的变量在PHP源码中以_zvalue_value的union定义在zend.h中。
* PHP中的类是_zvalue_value中的zend_object_value，采用“属性数组+方法数组”来实现，zend_object_value中包含了类入口zend_class_entry指针、指向属性数组的properties的HastTable指针以及一个用于阻止递归调用的guards指针。序列化时仅包含了属性数组内容与指向类的指针，通过该指针可以找到该类的类属性、静态属性、类常量、标准方法、魔术方法、自定义方法列表等。
* 除了stdClass以外每个对象一定有一个类对其对应。
  
###魔术方法
* 以“__”开头的内置PHP方法称为魔术方法，有构造函数__construct，析构函数__destruct，获取private或不存在属性的函数__get，设置private或不存在属性的函数__set，调用private或者不存在方法的函数__call，调用private或者不存在静态方法的函数__callStatic。
* 注意PHP中的构造方法可以带有参数，但是不可以被重载，只能存在一个__construct方法。
* 所有的魔术方法都应该是public的，其中__callStatic应该是static的，但即便声明为private或者__callStatic没有声明为static的依然可以正常运行，但是会得到一个warning级别的错误，但使用反射查看类的内部时发现其魔法方法依然是private的。
* 后四种魔术方法只有在获取或设置的属性或方法是private或者不存在时才会触发，而若已经可以获取到的public属性或方法并不触发魔法方法。
* 魔法方法的作用并不是在获取或调用不存在的属性方法时不报错，而是提供了为对象动态创建属性与方法的途径，这有时是非常有用的，例如在数据库操作中创建动态代理，代码会变得非常简单。
  
###