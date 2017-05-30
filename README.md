Yii比较“全”。
就Web开发而言，无论是哪种类型的应用、无论是哪个开发阶段的常见问题，Yii都有成熟、高效、可靠的解决方案。 对于典型的Web开发而言，这已经是足够了。比如，伪静态化、国际化、RESTful等，Yii都有提供编程的框架。 但是，从规模上来讲，Yii还算不上一个大型框架。 个人对其的评价是一个中型偏轻点的框架，对于绝大多数的应用开发而言，肯定是充分、够用的了。
Yii比较“快”。
Yii官方把运行效率作为一个重要的特点来宣传。从实际使用看，在诸多PHP框架中，确实效率上具有一定优势。 但个人认为这点其实不是最重要的特点。对于框架的使用者，也就是开发人员来讲，更重要的是开发效率。 由于Yii架构合理，Web开发中常用的思路和模式都可以很顺地套上使用。 在Web开发中经常遇到的一些细节上的问题，Yii也提供了许多现成解决方案，拿来就可以使用，非常高效、方便。 开发效率高，对于开发者、开发团队而言，更为重要。
要感谢Yii开发团队精益求精的不懈努力，为广大Web开发者创造了如此优秀的框架。 本人自Yii1.1起就开始接触并使用Yii了，由于工作和爱好关系，也接触过一些框架了。总的说，至今对Yii很满意， 最最心仪的是两点：学了Yii，就学到了许多当下最流行、最成熟的东西；开发起快，改进来快。

1. 全
2. 快：速度快；开发效率快

在 Yii 基础 部分，依次介绍了 
	1. 属性（Property） ，
	2. 事件（Event） ，
	3. 行为（Behavior）
在 Yii 约定 部分，主要讲解了
	1. Yii应用的目录结构和入口脚本 ， 
	2. 别名(Alias) ， 
	3. Yii的类自动加载机制 ， 
	4. 环境和配置文件 ， 
	5. 配置项（Configuration）
在 Yii 模式 部分
	1. MVC ， 
	2. 依赖注入和依赖注入容器 ， 
	3. 服务定位器（Service Locator）

# 属性（Property）
属性与成员变量没有区别。成员变量是就类的结构构成而言的概念，而属性是就类的功能逻辑而言的概念。
	表示方式：$object->foo
	public field 即使成员变量，也是属性
	private field 就只是成员变量，不是属性
	get set 是属性
	
在Yii中，由 yii\base\Object 提供了对属性的支持，因此，如果要使你的类支持属性， 必须继承自 yii\base\Object 。

在读取和写入对象的一个不存在的成员变量时， __get() __set() 会被自动调用。 Yii正是利用这点，提供对属性的支持的。

因此，要实现属性，通常有三个步骤：

继承自 yii\base\Object 。
声明一个用于保存该属性的私有成员变量。
提供getter或setter函数，或两者都提供，用于访问、修改上面提到的私有成员变量。 如果只提供了getter，那么该属性为只读属性，只提供了setter，则为只写。

【还是C#的 public string name { get; set; } 这样的简洁直观】

【所谓的封装，就是声名 private 变量？】

> 使用 public $title 只是一时之快，看起来简单，但今后的修改是个麻烦事。 简直可以说是恶梦。这就是软件工程的意义所在，通过一定的方法，使代码易于维护、便于修改。 一时看着好像没必要，但实际上吃过亏的朋友或者被客户老板逼着修改上一个程序员写的代码，问候过他亲人的， 都会觉得这是十分必要的。
==> 看来，这么写虽然繁琐，但还是有意义的

> 由于 __get() 和 __set() 是在遍历所有成员变量，找不到匹配的成员变量时才被调用。 因此，其效率天生地低于使用成员变量的形式。

> 另外一个提高效率的小技巧就是：使用 $pro = $object->getPro() 来代替 $pro = $object->pro ， 用 $objcect->setPro($value) 来代替 $object->pro = $value 。 这在功能上是完全一样的效果，但是避免了使用 __get() 和 __set() ，相当于绕过了遍历的过程。
==> Java都是这么搞吧。PHP绕了一圈，感觉就是吃饱了撑得

> 在Yii的框架中，极少出现 $app->request 之类的代码，而是使用 $app->getRequest() 。

>* 由于自动调用 __get() __set() 的时机仅仅发生在访问不存在的成员变量时。 因此，如果定义了成员变量 public $title 那么，就算定义了 getTitle() setTitle() ， 他们也不会被调用。因为 $post->title 时，会直接指向该 pulic $title ， __get() __set() 是不会被调用的。从根上就被切断了。
>* 由于PHP对于类方法不区分大小写，即大小写不敏感， $post->getTitle() 和 $post->gettitle() 是调用相同的函数。 因此， $post->title 和 $post->Title 是同一个属性。即属性名也是不区分大小写的。
>* 由于 __get() __set() 都是public的， 无论将 getTitle() setTitle() 声明为 public, private, protected， 都没有意义，外部同样都是可以访问。所以，所有的属性都是public的。
由于 __get() __set() 都不是static的，因此，没有办法使用static 的属性。
==> 说明“坑”很多

那么，就只能一直保持一个风格，不要想着偷懒：
class Post extends yii\base\Object    // 第一步：继承自 yii\base\Object
{
    private $_title;                 // 第二步：声明一个私有成员变量

    public function getTitle()       // 第三步：提供getter和setter
    {
        return $this->_title;
    }

    public function setTitle($value)
    {
        $this->_title = trim($value);
    }
}

## Object和Component¶
yii\base\Component 继承自 yii\base\Object ，因此，他也具有属性等基本功能。

但是，由于Componet还引入了事件、行为，因此，它并非简单继承了Object的属性实现方式，而是基于同样的机制， 重载了 __get() __set() 等函数。

**官方将Yii定位于一个基于组件的框架。可见组件这一概念是Yii的基础。 如果你有兴趣阅读Yii的源代码或是API文档，你将会发现， Yii几乎所有的核心类都派生于（继承自） yii\base\Component**
==> 可以说，这是重点

> 在Yii1.1时，就已经有了component了，那时是 CComponent。Yii2将Yii1.1中的CComponent拆分成两个类： yii\base\Object 和 yii\base\Component 。


其中，Object比较轻量级些，通过getter和setter定义了类的属性（property）。 Component派生自Object，并支持事件（event）和行为（behavior）。因此，Component类具有三个重要的特性：

属性（property）
事件（event）
行为（behavior）
【顺口溜一下：Component三要素：属性、事件、行为】

> Component由于增加了event和behavior这两个特性， 在方便开发的同时，也牺牲了一定的效率。 如果开发中不需要使用event和behavior这两个特性，比如表示一些数据的类。 那么，可以不从Component继承，而从Object继承。
==> 典型的应用场景就是如果表示用户输入的一组数据，那么，使用Object。
==> 从效率来讲，Object更接近原生的PHP类，因此，在可能的情况下，应当优先使用Object。

## Object的配置方法
Yii提供了一个统一的配置对象的方式。即：$config 
==> 就是一个各种配置项的数组。

实现这一切的关键就在 yii\base\Object 定义的构造函数中:
public function __construct($config = [])
{
    if (!empty($config)) {
        Yii::configure($this, $config);
    }
    $this->init();
}
==> $config 非空，就 Yii::configure。这是针对所以Object的，当然也包括了Application

数组配置对象的秘密在 Yii::configure() 中
public static function configure($object, $properties)
{
    foreach ($properties as $name => $value) {
        $object->$name = $value;
    }

    return $object;
}
==> 就是把数组中的元素，赋给当前Object当作属性
【【那么，我用过“属性”吗？既用过，配置就是，也没用过】】

#事件（Event）
使用事件，可以在特定的时点，触发执行预先设定的一段代码，事件既是代码解耦的一种方式，也是设计业务流程的一种模式。
【【那么，我用过“事件”吗？应该没有！】】

==> 所谓事件，就是定义的监听服务、事件名称、触发条件、处理函数

Yii中的 yii\base\Event ，封装了与事件相关的有关数据，并提供一些功能函数作为辅助:
class Event extends Object
{
    public $name;               // 事件名
    public $sender;             // 事件发布者，通常是调用了 trigger() 的对象或类。
    public $handled = false;    // 是否终止事件的后续处理
    public $data;               // 事件相关数据

    private static $_events = [];

    public static function on($class, $name, $handler, $data = null,
        $append = true)
    {
        // ... ...
        // 用于绑定事件handler
    }

    public static function off($class, $name, $handler = null)
    {
        // ... ...
        // 用于取消事件handler绑定
    }

    public static function hasHandlers($class, $name)
    {
        // ... ...
        // 用于判断是否有相应的handler与事件对应
    }

    public static function trigger($class, $name, $event = null)
    {
        // ... ...
        // 用于触发事件
    }
}
==> 这里定义了，监听动作、解除动作、处理函数、触发动作

###事件handler
事件handler必须具有以下形式:

function ($event) {
    // $event 就是前面提到的 yii\base\Event
}

可以是以下的形式提供：

>* 一个PHP全局函数的函数名，不带参数和括号，光秃秃的就一个函数名。如 trim ，注意，不是 trim($str) 也不是 trim() 。
>* 一个对象的方法，或一个类的静态方法。如 $person->sayHello() 可以用为事件handler，但要改写成以数组的形式， [$person, 'sayHello'] ，而如果是类的静态方法，那应该是 ['namespace\to\Person', 'sayHello'] 。
>* 匿名函数。如 function ($event) { ... }

> 对于类自己的成员函数，这样 $this->on(EVENT_A, 'publicMethod') 是一个错误的写法

### 事件的解除
在解除时，就是使用 unset() 函数

public function off($name, $handler = null)
{
    $this->ensureBehaviors();
    if (empty($this->_events[$name])) {
        return false;
    }

    // $handler === null 时解除所有的handler
    if ($handler === null) {
        unset($this->_events[$name]);
        return true;
    } else {
        $removed = false;

        // 遍历所有的 $handler
        foreach ($this->_events[$name] as $i => $event) {
            if ($event[0] === $handler) {
                unset($this->_events[$name][$i]);
                $removed = true;
            }
        }
        if ($removed) {
            $this->_events[$name] = array_values($this->_events[$name]);
        }
        return $removed;
    }
}
>* 当 $handler 为 null 时，表示解除 $name 事件的所有handler。
==> 这可能就是一个坑，不小心就掉下来了。

### 事件的触发

>* 对于事件的定义，提倡使用const 常量的形式，可以避免写错。 trigger('Hello') 和 trigger('hello') 可是不同的事件哦。原因在于handler数组下标，就是事件名。 而PHP里数组下标是区分大小写的。所以，用类常量的方式，可以避免这种头疼的问题。
==> 这可能就是一个坑，const 常量就是避免方法

// 定义事件的关联数据
class MsgEvent extend yii\base\Event
{
    public $dateTime;   // 微博发出的时间
    public $author;     // 微博的作者
    public $content;    // 微博的内容
}

// 在发布新的微博时，准备好要传递给handler的数据
$event = new MsgEvent;
$event->title = $title;
$event->author = $auhtor;

// 触发事件
$msg->trigger(Msg::EVENT_NEW_MESSAGE, $event);
==> on 的时候传入的参数，和trigger 的时候传入的参数

### 事件的删除
>* 对于匿名函数来说，写了两个一模一样的匿名函数，你也没办法把你前面的匿名handler解除。从本质上来讲，两个匿名函数就是两个不同的存在，为了能够正确解除，需要先把匿名handler保存成一个变量，如上面的 $anonymousFunction ，然后再依次绑定、解除。但是，使用了变量后，就失去了匿名函数的一大心理上的优势，你本不用去关心他的，我的建议是在这种情况下，就不要使用匿名函数了。因此，在作为handler时，要慎重使用匿名函数。只有在确定不需要解除时，才可以使用。
==> 这可能就是一个坑。避免方法就是最好不用

### 事件的级别
#### 类级别事件
类级别事件的绑定需要使用 yii\base\Event::on()
Event::trigger($this, $name, $event);                // 触发类一级的事件
这个语句就触发了类级别的事件。类级别事件，总是在实例事件后触发。


从 yii\base\Event::trigger() 的参数列表来看，比 yii\base\Component::trigger() 多了一个参数 $class 表示这是哪个类的事件。因此，在保存 $_event[] 数组上， yii\base\Event 也比 yii\base\Component 要多一个维度:

// Component中的$_event[] 数组
$_event[$eventName][] = [$handler, $data];

// Event中的$_event[] 数组
$_event[$eventName][$calssName][] = [$handler, $data];

类级别事件的触发，应使用 yii\base\Event::trigger() 。

>* 对于类级别事件，有一个要格外注意的地方，就是他不光会触发自身这个类的事件，这个类的所有祖先类的同一事件也会被触发。但是，自身类事件与所有祖先类的事件，视为同一级别

// 最外面的循环遍历所有祖先类
do {
    if (!empty(self::$_events[$name][$class])) {
        foreach (self::$_events[$name][$class] as $handler) {
            $event->data = $handler[1];
            call_user_func($handler[0], $event);

            // 所有的事件都是同一级别，可以随时终止
            if ($event->handled) {
                return;
            }
        }
    }
} while (($class = get_parent_class($class)) !== false);
==> 这可以说是特性，也可以说是坑

>* 从机制上来讲，由于类级别事件会被类自身、类的实例、后代类、后代类实例所触发，所以，对于越底层的类而言，其类事件的影响范围就越大。因此，在使用类事件上要注意，尽可能往后代类安排，以控制好影响范围，尽可能不在基础类上安排类事件。

#### 全局事件
接下来再讲讲全局级别事件。上面提到过，所谓的全局事件，本质上只是一个实例事件罢了。他只是利用了Application实例在整个应用的生命周期中全局可访问的特性，来实现这个全局事件的。当然，你也可以将他绑定在任意全局可访问的的Component上。

全局事件一个最大优势在于：在任意需要的时候，都可以触发全局事件，也可以在任意必要的时候绑定，或解除一个事件:

Yii::$app->on('bar', function ($event) {
    echo get_class($event->sender);        // 显示当前触发事件的对象的类名称
});

Yii::$app->trigger('bar', new Event(['sender' => $this]));

#行为（Behavior）
>* 使用行为（behavior）可以在不修改现有类的情况下，对类的功能进行扩充。 通过将行为绑定到一个类，可以使类具有行为本身所定义的属性和方法，就好像类本来就有这些属性和方法一样。 而且不需要写一个新的类去继承或包含现有类。
==> 我写的微信后台的功能插件，用的就是这个东西。

Yii中的行为，其实是 yii\base\Behavior 类的实例， 只要将一个Behavior实例绑定到任意的 yii\base\Component 实例上， 这个Component就可以拥有该Behavior所定义的属性和方法了。

> Behavior只能与Component类绑定。需要使用到行为，那么就果断地继承自 yii\base\Component


// Step 1: 定义一个将绑定行为的类
class MyClass extends yii\base\Component
{
    // 空的
}

// Step 2: 定义一个行为类，他将绑定到MyClass上
class MyBehavior extends yii\base\Behavior
{
    // 行为的一个属性
    public $property1 = 'This is property in MyBehavior.';

    // 行为的一个方法
    public function method1()
    {
        return 'Method in MyBehavior is called.';
    }
}

$myClass = new MyClass();
$myBehavior = new MyBehavior();

// Step 3: 将行为绑定到类上
$myClass->attachBehavior('myBehavior', $myBehavior);

// Step 4: 访问行为中的属性和方法，就和访问类自身的属性和方法一样
echo $myClass->property1;
echo $myClass->method1();
==> 我认为这就是全部了，难道还有更深入的玩法吗？

>* 由于行为从本质来讲是一个PHP类，其方法就是类方法，就是成员函数。 所以，在行为的方法中， $this 引用的是行为本身， 试图通过 $this 来访问行为所依附的Component是行不通的。 正确的方法是通过 yii\base\Behavior::$owner 来访问Component。
==> 这也是一个坑。这就要求，开发人员必须知道。

## 行为所要响应的事件
行为与事件结合后，可以在不对类作修改的情况下，补充类在事件触发后的各种不同反应。
==> 即：这是很好的插件编写方式，也是很好的扩展系统方式

namespace app\Components;

use yii\db\ActiveRecord;
use yii\base\Behavior;

class MyBehavior extends Behavior
{
    // 重载events() 使得在事件触发时，调用行为中的一些方法
    public function events()
    {
        // 在EVENT_BEFORE_VALIDATE事件触发时，调用成员函数 beforeValidate
        return [
            ActiveRecord::EVENT_BEFORE_VALIDATE => 'beforeValidate',
        ];
    }

    // 注意beforeValidate 是行为的成员函数，而不是绑定的类的成员函数。
    // 还要注意，这个函数的签名，要满足事件handler的要求。
    public function beforeValidate($event)
    {
        // ...
    }
}
>*字符串，表示行为类的方法，如上面的例就是这种情况。这个是与事件handler不同的，事件handler中使用字符串时，是表示PHP全局函数，而这里表示行为类内部的方法。
==> 这也是一个坑，要求程序员必须知道的。
>* 一个对象或类的成员函数，以数组的形式，如 [$object, 'methodName'] 。这个与事件handler是一致的。
>* 一个匿名函数。
==> 这里没有全局函数？

#### 定义一个行为
定义一个行为，就是准备好要注入到现有类中去的属性和方法， 这些属性和方法要写到一个 yii\base\Behavior 类中。 所以，定义一个行为，就是写一个 Behavior的子类，子类中包含了所要注入的属性和方法:

namespace app\Components;

use yii\base\Behavior;

class MyBehavior extends Behavior
{
    public $prop1;

    private $_prop2;
    private $_prop3;
    private $_prop4;

    public function getProp2()
    {
        return $this->_prop2;
    }

    public function setProp3($value)
    {
        $this->_prop3 = $value;
    }

    public function foo()
    {
        // ...
    }

    protected function bar()
    {
        // ...
    }
}
>* 当这MyBehavior与一个Component绑定后， 绑定的Component也就拥有了 prop1 prop2 这两个属性和方法 foo() ，因为他们都是 public 的。 而 private 的 $_prop4 和 protected 的 bar 就得不到了。 至于原因么，后面讲行为注入的原理时，我们再解释。

#### 行为的绑定
1. 一种是静态的方法，静态的方法在实践中用得比较多一些。
静态绑定行为，只需要重载 yii\base\Component::behaviors() 就可以了。
namespace app\models;

use yii\db\ActiveRecord;
use app\Components\MyBehavior;

class User extends ActiveRecord
{
    public function behaviors()
    {
        return [
            // 匿名的行为，仅直接给出行为的类名称
            MyBehavior::className(),

            // 名为myBehavior2的行为，也是仅给出行为的类名称
            'myBehavior2' => MyBehavior::className(),

            // 匿名行为，给出了MyBehavior类的配置数组
            [
                'class' => MyBehavior::className(),
                'prop1' => 'value1',
                'prop3' => 'value3',
            ],

            // 名为myBehavior4的行为，也是给出了MyBehavior类的配置数组
            'myBehavior4' => [
                'class' => MyBehavior::className(),
                'prop1' => 'value1',
                'prop3' => 'value3',
            ]
        ];
    }
}
==> overwirte behaviors 这个函数，里面返回一个定义了behavior的数组

还有一个静态的绑定办法，就是通过配置文件来绑定:
[
    'as myBehavior2' => MyBehavior::className(),

    'as myBehavior3' => [
        'class' => MyBehavior::className(),
        'prop1' => 'value1',
        'prop3' => 'value3',
    ],
]

2. 另一种是动态的。动态绑定的方法主要是提供了更灵活的方式，但实际使用中并不多见。
动态绑定行为，需要调用 yii\base\Compoent::attachBehaviors():
$Component->attachBehaviors([
    'myBehavior1' => new MyBehavior,  // 这是一个命名行为
    MyBehavior::className(),          // 这是一个匿名行为
]);

在上面的这些例子中，以数组的键作为行为的命名，而对于没有提供键名的行为，就是匿名行为。

对于命名的行为，可以调用 yii\base\Component::getBehavior() 来取得这个绑定好的行为:

$behavior = $Component->getBehavior('myBehavior2');
对于匿名的行为，则没有办法直接引用了。但是，可以获取所有的绑定好的行为:

$behaviors = $Component->getBehaviors();

#### 行为的绑定与事件
public function attach($owner)
{
    $this->owner = $owner;
    foreach ($this->events() as $event => $handler) {
        $owner->on($event, is_string($handler) ? [$this, $handler] :
            $handler);
    }
}
==> 如果在 Behavior 里的events 数组非空，说明它就要打算响应某个事件
#### 绑定小结：
1. 绑定的动作从Component发起；
2. 静态绑定通过重载 yii\base\Componet::behaviors() 实现；
3. 动态绑定通过调用 yii\base\Component::attachBehaviors() 实现；
4. 行为还可以通过为 Component 配置 as 配置项进行绑定；
5. 行为有匿名行为和命名行为之分，区别在于绑定时是否给出命名。 命名行为可以通过其命名进行标识，从而有针对性地进行解除等操作；
6. 绑定过程中，后绑定的行为会取代已经绑定的同名行为；
7. 绑定的意义有两点，一是为行为设置 $owner 。二是将行为中拟响应的事件的handler绑定到类中去。

#### 解除行为
解除行为只需调用 yii\base\Component::detachBehavior() 就OK了:

$Component->detachBehavior('myBehavior2');
这样就可以解除已经绑定好的名为 myBehavior2 的行为了。 但是，对于匿名行为，这个方法就无从下手了。不过我们可以一不做二不休，解除所有绑定好的行为:

$Component->detachBehaviors();

#### 行为响应的事件实例

Yii费了那么大劲，主要就是为了将行为中的事件handler绑定到类中去。 在实际编程时，行为用得最多的，也是对于Compoent各种事件的响应。通过行为注入，可以在不修改现有类的代码的情况下，更改、扩展类对于事件的响应和支持。 


public function events()
{
    return array_fill_keys(array_keys($this->attributes),
        'evaluateAttributes');
}
==> 这里就用到了：array_keys了，上次面试就被问道这个问题，我一直以为这种函数没用。

### 行为的属性和方法注入原理
对于属性而言，是通过 __get() 和 __set() 魔术方法来实现的。 对于方法，是通过 __call() 方法
 
### 行为与继承和特性（Traits） 的区别
PHP中要达到这样的效果，可以使用继承呀，可以使用PHP新引入的特性（Traits）。

但是，行为具有继承和特性所没有的优点，从实际使用的角度讲，继承和特性更靠底层点。靠底层，就意味着开发效率低，运行效率高。行为的引入，是以可以接受的运行效率牺牲为成本，谋取开发效率大提升的一笔买卖。

#### 行为与继承
拿行为与继承比较，从逻辑上是不对的，这两者是在完全不同的层面上的事物，是不对等的。
从本质上来讲，行为只是一种设计模式，是解决问题的方法学。继承则是PHP作为编程语言所提供的特性，根本不在一个层次上。

相比较于使用继承的方式来扩充类功能，使用行为的方式，一是不必对现有类进行修改，二是PHP不支持多继承，但是Yii可以绑定多个行为，从而达到类似多继承的效果。

#### 行为与特性

特性是PHP5.4之后引入的一个新feature。

从实现效果看，行为与特性都达到把自身的public 变量、属性、方法注入到当前类中去的目的。

1. 倾向于使用行为的情况：
	>* 行为从本质上讲，也是PHP的类，因此一个行为可以继承自另一个行为，从而实现代码的复用。而特性只是PHP的一种语法，效果上类似于把特性的代码导入到了类中从而实现代码的注入，特性是不支持继承的。
	>* 行为可以动态地绑定、解除，而不必要对类进行修改。但是特性必须在类在使用 use 语句，要解除特性时，则要删除这个语句。换句话说，需要对类进行修改。
	>* 行为还以在在配置阶段进行绑定，特性就不行了。
	>* 行为可以用于对事件进行反馈，而特性不行。
	>* 当出现命名冲突时，行为会自行排除冲突，自动使用先绑定的行为。而特性在发生冲突时，需要人为干预，修改发生冲突的变量名、属性名、方法名。
2. 倾向于使用特性的情况：
	>* 特性比行为在效率上要高一点，因为行为其实是类的实例，需要时间和空间进行分配。
	>* 特性是PHP的语法，因此，IDE的支持要好一些。目前还没有IDE能支持行为。

# Yii 约定
这一部份主要讲的是Yii中以约定的方式来实现的功能，或者说是惯用的模式。
==> 风格、style、类似操作系统的什么文件放在什么位置。
## Yii应用的目录结构和入口脚本

以下是一个通过高级模版安装后典型的Yii应用的目录结构：

.
├── backend
├── common
├── console
├── environments
├── frontend
├── nbproject
├── tests
├── vendor
├── composer.json
├── composer.lock
├── init
├── init.bat
├── LICENSE.md
├── README.md
├── requirements.php
├── yii
└── yii.bat
==> 比Yii 1.0 多了frontend、nbproject、composer、requirements

对于高级应用而言，相当于有 backend frontend console 三个独立的Yii应用。 
==> 这就是说，前台和后台分离。

### 公共目录
.
├── config
├── mail
└── models
>* config 就是通用的配置，这些配置将作用于前后台和命令行。
>* mail 就是应用的前后台和命令行的与邮件相关的布局文件等。
>* models 就是前后台和命令行都可能用到的数据模型。 这也是 common 中最主要的部分。

### vendor 
各种第三方的程序。这是Composer安装的其他程序的存放目录，包含Yii框架本身，也放在这个目录下面。 如果你向 composer.json 目录增加了新的需要安装的程序，那么下次调用Composer的时候， 就会把新安装的目录也安装在这个 vendor 下面。

### 前台的目录结构
.
├── assets
├── config
├── controllers
├── models
├── runtime
├── views
├── web
└── widgets

>* assets 目录用于存放前端资源包PHP类。 这里不需要了解什么是前端资源包，只要大致知道是用于管理CSS、js等前端资源就可以了。
>* config 用于存放本应用的配置文件，包含主配置文件 main.php 和全局参数配置文件 params.php 。
>* models views controllers 3个目录分别用于存放数据模型类、视图文件、控制器类。这个是我们编码的核心，也是我们工作最多的目录。
>* widgets 目录用于存放一些常用的小挂件的类文件。
>* tests 目录用于存放测试类。
>* web 目录从名字可以看出，这是一个对于Web服务器可以访问的目录。 除了这一目录，其他所有的目录不应对Web用户暴露出来。这是安全的需要。
>* runtime 这个目录是要求权限为 chmod 777 ，即允许Web服务器具有完全的权限， 因为可能会涉及到写入临时文件等。 但是一个目录并未对Web用户可见。也就是说，权限给了，但是并不是Web用户可以访问到的。
==> 以前一般上传文件的upload，它这是命名为web了。

backend 目录与 frontend 的结构、内容是一样一样的。

### 入口文件index.php
```php
<?php
defined('YII_DEBUG') or define('YII_DEBUG', true);
defined('YII_ENV') or define('YII_ENV', 'dev');

require(__DIR__ . '/../../vendor/autoload.php');
require(__DIR__ . '/../../vendor/yiisoft/yii2/Yii.php');
require(__DIR__ . '/../../common/config/bootstrap.php');
require(__DIR__ . '/../config/bootstrap.php');

$config = yii\helpers\ArrayHelper::merge(
    require(__DIR__ . '/../../common/config/main.php'),
    require(__DIR__ . '/../../common/config/main-local.php'),
    require(__DIR__ . '/../config/main.php'),
    require(__DIR__ . '/../config/main-local.php')
);

$application = new yii\web\Application($config);
$application->run();
```
如果定义了 YII_DEBUG ， 那么表示当前为调试状态，应用在运行过程中，会有一些调试信息的输出。 在抛出异常时，也会有一个详细的调用栈的显示。

dev prod 是Yii安装后默认的两个环境，分别表示开发环境和最终的产品环境。 此外还有一个 test 环境，表示测试环境。

环境在代码中主要是影响配置文件。 YII_ENV 的 dev prod test 三种环境， 会分别使 YII_ENV_DEV YII_ENV_PROD YII_ENV_TEST 的值为 true 。 这样，在应用的配置中，特别是在相同的一个配置文件中，可以对不同环境作出不同的配置。

其实，这个 YII_ENV 的定义就是一个与 init 脚本环境切换的相互补充。
如果各环境比较明晰，用 init 来切换各种环境的配置是完全够用的。


函数 yii\helpers\ArrayHelper::merge() 作用在于合并参数所指定的各个数组。其中，后面的数组会把前面数组中相同下标的元素覆盖掉。 这个语句的作用，就是读取、合并应用的各配置文件并保存在 $config 变量中。 

### 命令行应用入口脚本
命令行应用的入口脚本是 path/to/digpage.com/yii 文件。这个文件被 init 脚本设为可执行的。 
```php
#!/usr/bin/env php
<?php

defined('YII_DEBUG') or define('YII_DEBUG', true);
defined('YII_ENV') or define('YII_ENV', 'dev');

// fcgi doesn't have STDIN and STDOUT defined by default
defined('STDIN') or define('STDIN', fopen('php://stdin', 'r'));
defined('STDOUT') or define('STDOUT', fopen('php://stdout', 'w'));

require(__DIR__ . '/vendor/autoload.php');
require(__DIR__ . '/vendor/yiisoft/yii2/Yii.php');
require(__DIR__ . '/common/config/bootstrap.php');
require(__DIR__ . '/console/config/bootstrap.php');

$config = yii\helpers\ArrayHelper::merge(
    require(__DIR__ . '/common/config/main.php'),
    require(__DIR__ . '/common/config/main-local.php'),
    require(__DIR__ . '/console/config/main.php'),
    require(__DIR__ . '/console/config/main-local.php')
);

$application = new yii\console\Application($config);
$exitCode = $application->run();
exit($exitCode);
```
对比于Web应用的 index.php 入口脚本， yii 并没有太多的新东西，其中核心的东西根本就没变。

define('STDIN') 和 define('STDOUT') 则为fcgi定义了标准输入和标准输出。

## 别名(Alias)

可以将别名视为特殊的常量变量，他的作用在于避免将一些文件路径、URL以硬编码的方式 写入代码中，或者多处出现一长串的文件路径、URL

### 预定义的别名
Yii中，别名以 @ 开头，以区别于正常的文件路径和URL。Yii中预定义了许多常用的 别名。别名的定义一般放在应用的最开始的阶段进行，比如引导阶段、初始化阶段等。 这样可以保证后续代码可以使用这些定义好的别名

<?php
Yii::setAlias('common', dirname(__DIR__));
Yii::setAlias('frontend', dirname(dirname(__DIR__)) . '/frontend');
Yii::setAlias('backend', dirname(dirname(__DIR__)) . '/backend');
Yii::setAlias('console', dirname(dirname(__DIR__)) . '/console');

上面的 bootstrap.php 文件定义了 @common ， @frontend ， @backend 和 @console 4个别名。

### Yii预定义的别名
有的别名相对比较固定，与Yii框架和应用自身息息相关。这类别名直接写到Yii的代码中去了。
这些预定义的别名，主要分布在 yii\BaseYii 和 yii\base\Application 等类中。

在 yii\BaseYii 中:
// 定义了 @yii 别名
public static $aliases = ['@yii' => __DIR__];

==> yii\BaseYii::$aliases 用于保存整个Yii应用的所有的别名
==> 这里默认地把 yii\BaseYii.php 所在的目录作为 @yii 别名。

public function preInit(&$config)
{
    ... ...

    // basePath必须在配置文件中给出，否则会抛出弃常
    if (isset($config['basePath'])) {
        // 这里会设置 @app
        $this->setBasePath($config['basePath']);
        unset($config['basePath']);
    } else {
        throw new InvalidConfigException(
        'The "basePath" configuration for the Application is required.');
    }

    // @vendor 如果配置文件中设置了 vendorPath 使用配置的值，否则使用默认的
    if (isset($config['vendorPath'])) {
        $this->setVendorPath($config['vendorPath']);
        unset($config['vendorPath']);
    } else {
        $this->getVendorPath();
    }

    // @runtime 如果配置文件中设置了 runtimePath ，就使用配置的值，否则使用默认的
    if (isset($config['runtimePath'])) {
        $this->setRuntimePath($config['runtimePath']);
        unset($config['runtimePath']);
    } else {
        $this->getRuntimePath();
    }

    ... ...
}

上面的代码中，预定义了5个别名： @app ， @vendor @bower @npm ， @runtime 。
上面的代码中， basePath 不是别名，但必须由开发者自己在配置文件中设定，表示应用的根目录。

定义 basePath 时，Yii顺便定义了 @app ，代码在 yii\base\Application::setBasePath() 中:

public function setBasePath($path)
{
    parent::setBasePath($path);
    Yii::setAlias('@app', $this->getBasePath());
}
可以看出， @app 与 basePath 是一致的。


@app ，必须由开发者在配置文件中提供，一般为配置文件的 dirname(__DIR__) 。 即 digpage.com/frontend 之类的目录。
@vendor ，一般定义为 @app/vendor ，高级模板中则定义为 @app/../vendor
@bower ，定义为 @vendor/bower
@npm ，定义为 @vendor/npm
@runtime ，定义为 @app/runtime

这里有一个比较特殊的，就是 @vendor 。 对于使用Yii基础模版创建的应用而言，会使用上面提到的 @app/vendor 。 但是，对于使用高级模版创建的应用，你会发现，vendor目录并不在 frontend 或 backend 目录下， 而是跟他们是兄弟目录。这是因为对于整个工程而言，这个vendor的内容是 frontend 和 backend等共用的。
==> 基础模板和高级模版 有啥区别？

在使用高级模板创建应用时， digpage.com/common/config/main.php 配置文件会重新设定 vendorPath

'vendorPath' => dirname(dirname(__DIR__)) . '/vendor'

对于Web应用， yii\base\Web\Application 中又定义了 @webroot 和 @web 2个别名:

protected function bootstrap()
{
    $request = $this->getRequest();
    Yii::setAlias('@webroot', dirname($request->getScriptFile()));
    Yii::setAlias('@web', $request->getBaseUrl());

    parent::bootstrap();
}

这里 @webroot 就是入口脚本 index.php 所在的目录。 
而 @web 则是URL别名，表示当前应用的根URL地址

当使用Composer安装扩展后，会向 @vendor/yiisoft/extensions.php 写入信息，其中就包含相应的别名。 只不过这些别名通常都是二级别名。然后，在 yii\base\Application::bootstrap() 中，将这些扩展的别名进行注册。

<?php

$vendorDir = dirname(__DIR__);

return array (
  'yiisoft/yii2-swiftmailer' =>
  array (
    'name' => 'yiisoft/yii2-swiftmailer',
    'version' => '9999999-dev',
    'alias' =>
    array (
      '@yii/swiftmailer' => $vendorDir . '/yiisoft/yii2-swiftmailer',
    ),
  ),

 ... ...

 'yiisoft/yii2-gii' =>
  array (
    'name' => 'yiisoft/yii2-gii',
    'version' => '9999999-dev',
    'alias' =>
    array (
      '@yii/gii' => $vendorDir . '/yiisoft/yii2-gii',
    ),
  ),
);

### 所有预定义的别名

>* @yii 表示Yii框架所在的目录，也是 yii\BaseYii 类文件所在的位置；
>* @app 表示正在运行的应用的根目录，一般是 digpage.com/frontend ；
>* @vendor 表示Composer第三方库所在目录，一般是 @app/vendor 或 @app/../vendor ；
>* @bower 表示Bower第三方库所在目录，一般是 @vendor/bower ；
>* @npm 表示NPM第三方库所在目录，一般是 @vendor/npm ；
>* @runtime 表示正在运行的应用的运行时用于存放运行时文件的目录，一般是 @app/runtime ；
>* @webroot 表示正在运行的应用的入口文件 index.php 所在的目录，一般是 @app/web；
>* @web URL别名，表示当前应用的根URL，主要用于前端；
>* @common 表示通用文件夹；
>* @frontend 表示前台应用所在的文件夹；
>* @backend 表示后台应用所在的文件夹；
>* @console 表示命令行应用所在的文件夹；
>* 其他使用Composer安装的Yii扩展注册的二级别名。

### 定义与解析别名

别名规范化
如果要定义的别名 $alias 并非以 @ 打头，自动为这个别名加上 @ 前缀。 总之，只要是别名，必然以 @ 打头。下面的两个语句，都定义了相同的别名 @foo

Yii::setAlias('foo', 'path/to/foo');

Yii::setAlias('@foo', 'path/to/foo');


获取根别名
$alias 的根别名，就是 @ 加上第一个 / 之间地内容，以 $root 表示。 这里可以看出，别名是分层次的。下面3个语句的根别名都是 @foo

Yii::setAlias('@foo', 'path/to/some/where');
Yii::setAlias('@foo/bar', 'path/to/some/where');
Yii::setAlias('@foo/bar/qux', 'path/to/some/where');

### 新定义别名还是删除别名
如果传入的 $path 不是 null ，说明是正常的别名定义。 对于正常的别名定义，就是往 BaseYii::$aliases[] 里写入信息。 而如果 $path 为 null ，说明是要删除别名:

// 定义别名@foo
Yii::setAlias('@foo', 'path/to/some/where');

// 删除别名@foo
Yii::setAlias('@foo', null);

别名的写入
对于全新的别名，也即其根别名是新的， BaseYii::aliases[$root] 不存在。 那么全新别名的写入分两种情况： 如果全新别名本身就是根别名，那么直接 BaseYii::aliases[$alias] = $path ； 而如果全新的别名并非是一个根别名，即形如 @foo/bar 带有二级、三级等路径的， BaseYii::aliases[$root] = [$alias => $path] 。比如:

>* 如果根别名对应的是一个数组，在新增、覆盖后， Yii会调用PHP的 krsort() 把数组按照键值重新逆向排序。 这可以有效确保长的别名会放在短的类以别名前面， 比如， @foo/bar/qux 和 @foo/bar 同样被放在根别名 @foo 之下， 但长的那个，会被放在前面。
==> 这里就用到了 ksort 函数了。

#### 别名的删除
传入的 $path 为 null 表示要删除别名。 Yii使用PHP的 unset() 注销 BaseYii::$aliases[] 数组中的对应元素， 达到删除别名的目的。

>* 注意删除别名后，不需要调用 krsort() 对数组进行处理。
==> 那说明unset 函数多这个数组进行了处理了？

别名的解析过程可以这么看:

// 无效的别名，别名必须以@打头，别名不能放在中间
// 但是语句不会出错，会认为这是一个路径，一字不变的路径： path/to/@foo/bar
Yii::getAlias('path/to/@foo/bar');

// 定义 @foo @foo/bar @foo/bar/qux 3个别名
Yii::setAlias('@foo', 'path/to/foo');
Yii::setAlias('@foo/bar', 'path/2/bar');
Yii::setAlias('@foo/bar/qux', 'path/to/qux');

// 找不到 @foobar根别名，抛出异常
Yii::getAlias('@foobar/index.php');

// 匹配@foo，相当于 path/to/foo/qux/index.php
Yii::getAlias('@foo/qux/index.php');

// 匹配@foo/bar/qux，相当于 path/to/qux/2/index.php
Yii::getAlias('@foo/bar/qux/2/index.php');

// 匹配@foo/bar，相当于 path/to/bar/2/2/index.php
Yii::getAlias('@foo/bar/2/index.php');

**使用别名时，要将别名放在最前面，不能放在中间。**


## Yii的类自动加载机制

Yii借助了PHP的类自动加载机制高效实现了类的定位、导入，这一机制兼容 PSR-4 的标准。

在Yii中，类仅在调用时才会被加载，特别是核心类，其定位非常快，这也是Yii高效高性能的一个重要体现。

### 自动加载机制的实现
Yii的类自动加载，依赖于PHP的 spl_autoload_register() ， 注册一个自己的自动加载函数（autoloader），并插入到自动加载函数栈的最前面，确保Yii的autoloader会被最先调用。

```php
<?php
defined('YII_DEBUG') or define('YII_DEBUG', false);
defined('YII_ENV') or define('YII_ENV', 'prod');

// 这个是第三方的autoloader
require(__DIR__ . '/../../vendor/autoload.php');

// 这个是Yii的Autoloader，放在最后面，确保其插入的autoloader会放在最前面
require(__DIR__ . '/../../vendor/yiisoft/yii2/Yii.php');
// 后面不应再有autoloader了

require(__DIR__ . '/../../common/config/aliases.php');

$config = yii\helpers\ArrayHelper::merge(
    require(__DIR__ . '/../../common/config/main.php'),
    require(__DIR__ . '/../../common/config/main-local.php'),
    require(__DIR__ . '/../config/main.php'),
    require(__DIR__ . '/../config/main-local.php')
);

$application = new yii\web\Application($config);
$application->run();
```

看看Yii是如何调用 spl_autoload_register() 注册autoloader的， 这要看 Yii.php 里发生了些什么:

<?php
require(__DIR__ . '/BaseYii.php');
class Yii extends \yii\BaseYii
{
}

// 重点看这个 spl_autoload_register
spl_autoload_register(['Yii', 'autoload'], true, true);

// 下面的语句读取了一个映射表
Yii::$classMap = include(__DIR__ . '/classes.php');

Yii::$container = new yii\di\Container;


Yii::$classMap 这个映射表，保存了一系列的类名与其所在PHP文件的映射关系，比如:
return [
  'yii\base\Action' => YII2_PATH . '/base/Action.php',
  'yii\base\ActionEvent' => YII2_PATH . '/base/ActionEvent.php',

  ... ...

  'yii\widgets\PjaxAsset' => YII2_PATH . '/widgets/PjaxAsset.php',
  'yii\widgets\Spaceless' => YII2_PATH . '/widgets/Spaceless.php',
];

### 运用自动加载机制
在入口脚本中，除了Yii自己的autoloader，还有一个第三方的autoloader:

require(__DIR__ . '/../../vendor/autoload.php');

这个其实是Composer提供的autoloader。Yii使用Composer来作为包依赖管理器，因此，建议保留Composer的autoloader，尽管Yii的autoloader也能自动加载使用Composer安装的第三方库、扩展等，而且更为高效。

## 环境和配置文件

每个Yii环境就是一组配置文件， 包含了入口脚本 index.php 和各类配置文件。 其实他们都放在 /path/to/digpage.com/environments 目录下面

环境目录下的 index.php 并不是通常所说的web入口脚本，它其实是个定义文件。 定义了可以使用的环境。

###  环境配置的生效规则

原则是：

>* 前台、后台和命令行的配置文件间，互不干扰，各管各的。没有先后顺序一说。 因为Yii在任意时间，要么是在跑前台，要么是在跑后台。还记得么？他们是不同的应用，他们是独立的。 但是，这里有个common，通用于前台、后台等。common的内容被前台或后台的覆盖。
>* local和不带local的。明显的，local的是本地配置文件，不带local的是团队间通用的配置。 因此，local的覆盖不带local的。
>* params, main。这2类文件表示的配置内容并不重叠，他们逻辑上不存在谁覆盖谁的问题。 如果看看源代码，可以发现，params只是main配置的一部分。 而main的内容，是作为参数传递给应用的构造函数。 因此，这两者不存在谁覆盖谁的问题。


### 环境的使用
环境在具体使用上，把握这么几个原则：

>* 与前后台无关，且与环境无关的配置项，写到 digpage.com\common\config\main.php 中去。 不要写到环境中去，也不要写到前台或后台的配置文件中去。 比如，当使用 FileCache 作为缓存时，这是与环境无关、与前后台无关的， 或者说所有环境下，前后台的配置都相同。 有关配置项要写到 digpage.com\common\config\main.php 中去。
>* 无关环境的配置，不要写到环境中去，写到应用的配置中去。 如，应用的ID，无论是开发环境还是产品环境，ID是不会变的。但前后台ID是不同的。 因此，ID的配置项写到 digpage.com\frontend\config\main.php 中去。 而不要写到 digpage.com\environment\frontend\config\main-local.php 中去。
>* 与环境有关，但与前后台无关的配置项，要写到环境的 common 配置中去。 比如，有的应用前后台使用的数据库是一致的，因此，其 db 配置项应该是一样的。 但在开发时，所使用的数据库服务与产品时的数据库服务肯定是不一样的。 这种情况下，要所配置项写到 digpage.com\environments\dev\common\main-local.php
>* 环境配置文件只提供框架。凡是环境配置文件，对于敏感信息，如，数据库地址、用户名、密码、API Key等信息， 一律留空，供团队成员在调用 init 后自行填写。
>* 本地配置绝不提交代码库。因此，所有的应用目录（frontend, backend等，并非environment目录）下的， 所有 *-local.php 都不提交代码库。这点Yii已经通过 .gitignore 为我们做好了。 关于 .gitignore 的有关信息，可以看看 Git文档 的有关内容。
==> 环境、应用、前台后台命令行

###　注意 cookieValidationKey
每次调用 init 脚本切换、更新环境配置时， cookieValidationKey 都会被重新生成的随机串所覆盖。
==> 原先保存在用户机器上的cookie在下次访问时，会全部失效。 
==> 一个简单表现就是已经设置了自动登录的用户，在更新 cookieValidationKey 后，全部需要重新登录。

当你的应用采用分布式架构提供服务，同时运行在多个节点的时候。 有的负载均衡策略会将同一用户的先后2次请求随机分配给不同的节点进行处理。 而如果这两个节点的 cookieValidationKey 不一致，那么就会出现用户就会收到很奇怪的错误信息。
==> 这也是一个坑，可能掉进去还莫名其妙

## 配置项（Configuration）
1. Yii应用自身和其他几乎一切类对象的创建、初始化、配置都要用到配置项。 
2. 配置项是针对对象而言的，也就是说，配置项一定是用于配置某一个对象，用于初始化或配置对象的属性。 

一个配置项采用下面的格式:
[
    'class' => 'path\to\ClassName',
    'propertyName' => 'propertyValue',
    'on eventName' => $eventHandler,
    'as behaviorName' => $behaviorConfig,
]


// 使用 new 时指定了类型，配置数组中就不应再有 class 元素
$connection = new \yii\db\Connection([
    'dsn' => $dsn,
    'username' => $username,
    'password' => $password,
]);

// 使用 Yii::createObject()时，如果第一个参数指定了类型，也不应在配置数
// 组中设定 class
$db = Yii::createObject('yii\db\Connection', [
     'dsn' => 'mysql:host=127.0.0.1;dbname=demo',
     'username' => 'root',
     'password' => '',
     'charset' => 'utf8',
]);

// 对现有的对象重新配置时，也不应在配置数组中设定 class
Yii::configure($db, [
     'dsn' => 'mysql:host=127.0.0.1;dbname=demo',
     'username' => 'root',
     'password' => '',
     'charset' => 'utf8',
]);

# Yii 模式
Yii中使用了当前Web开发中最为主流和成熟的设计模式。包括
1. MVC模式、
2. 依赖注入（Denpdency Injection, DI）
3. 服务定位器（Service Locator）等种模式。
==> 依赖注入 和 Service Locator 应该是新有的，Yii 1.0里我没见过

## MVC
MVC的意义在于指导开发者将数据与表现解耦，提高代码，特别是模型部分代码的复用性。

目前几乎所有的Web开发框架都建立在MVC模式之上。 当然，最近几年也出现了一些诸如MVP, MVVM之类的新的设计模式。 但从技术的成熟程度和使用的广泛程度来讲，MVC仍是主流。
==> 微软的xaml 就是MVVM，用来开发android似乎不错

>* Model是指数据模型，是对客观事物的抽象。对于Model，主要是数据、业务逻辑和业务规则。
==> 这是MVC中比较稳定的部分，一般成品后不会改变。 开发初期的最重要任务，主要也是实现Model的部分。这一部分写得好，后面就可以改得少，开发起来就快。

>* View是指视图，也就是呈现给用户的一个界面，是model的具体表现形式，也是收集用户输入的地方。
==> 换句话说，对于用户而言，只有View是可见的、可操作的。
==> 他往往是MVC中变化频繁的部分，也是客户经常要求改来改去的地方。

>* Contorller指的是控制器，主要负责与model和view打交道。Contorller用于决定使用哪些Model，对Model执行什么操作，为视图准备哪些数据，是MVC中沟通的桥梁。
==> 换句话说，model和view之间一般不直接打交道，他们老死不相往来。
==> view中不会对model作任何操作， model不会输出任何用于表现的东西。

Model的几个原则：
1. Model一般要对输入数据作过滤、验证和规范化等预处理。 特别是对于需要保存进数据库的，一定要对所有的输入数据作预处理。 这些预处理，有的其实是业务逻辑。
2. 注意与Controller区分开。好的设计，应当是胖Model，瘦Controller。

==> 我一般的写法，都是直接在Controller 里用sql处理数据了，特别是复杂的sql。
==> 因为主要的业务逻辑都是sql来实现的，为什么没有把SQL独立出来解耦的模式设计呢？
==> 以前的那套DAI系统应该就是这样的设计。把业务SQL全部封装。

对于Controller，主要是响应用户请求，决定使用什么视图，需要准备什么数据用来显示。
以下是有关Controller的设计原则：

1. 用于处理用户请求。 因此，对于reqeust的访问代码应该放在Controller里面，比如 $_GET $_POST 等。 但仅限于获取用户请求数据，不应该对数据有任何操作或预处理，这些工作应该交由Models来完成。
2. 调用Models的读方法，获取数据，直接传递给视图，供显示。 当涉及到多个Model时，有关的逻辑应当交给Model来完成。
3. 调用Models的类方法，对Models进行写操作。
4. 调用视图渲染函数等，形成对用户Reqeust的Response。


## Model设计参考
在MVC中，Model排第一，是有一定暗示的。一是Model是整个架构中，代码量最大，复用程度最高， 也是最体现程序员设计功力的地方。

### Model应当集中整个应用的数据和业务逻辑

### 基础Model应当尽可能细化
在一个应用中，特别是对于大型复杂应用，Model间关系可能比较复杂。在构造应用时，特别是基础Model时， 要从足够小的粒度来设计。 一般而言，粒度越小，复用的可能性就越高。此时，就要考虑采取继承、封装等措施了。

### 分层次设计Model
从设计流程上，数据库结构设计与Model的设计是紧密相关的。先有数据库结构设计，后有Model设计。
==> 一般而言，最单元、粒度最小的Model就是根据每个数据库表所生成的Model，这往往是个Active Record。

### 仔细为Model方法命名

### MVC与前后端的配合
MVC从本质来讲，这是一种解决问题的思路和办法。 从实践来讲，这是一种久经考验的有效方式。

# 依赖注入和依赖注入容器
为了降低代码耦合程度，提高项目的可维护性，Yii采用多许多当下最流行又相对成熟的设计模式，包括了依赖注入（Denpdency Injection, DI）和服务定位器（Service Locator）两种模式。

关于依赖注入与服务定位器， [Inversion of Control Containers and the Dependency Injection pattern ](http://martinfowler.com/articles/injection.html)给出了很详细的讲解

## 有关概念

### 依赖倒置原则（Dependence Inversion Principle, DIP）
DIP是一种软件设计的指导思想。传统软件设计中，上层代码依赖于下层代码，当下层出现变动时， 上层代码也要相应变化，维护成本较高。而DIP的核心思想是上层定义接口，下层实现这个接口， 从而使得下层依赖于上层，降低耦合度，提高整个系统的弹性。这是一种经实践证明的有效策略。
==> 这不就是我理解的UI、交互驱动吗？

### 控制反转（Inversion of Control, IoC）
IoC就是DIP的一种具体思路，DIP只是一种理念、思想，而IoC是一种实现DIP的方法。 IoC的核心是将类（上层）所依赖的单元（下层）的实例化过程交由第三方来实现。 一个简单的特征，就是类中不对所依赖的单元有诸如 $component = new yii\component\SomeClass（） 的实例化语句。

### 依赖注入（Dependence Injection, DI）
DI是IoC的一种设计模式，是一种套路，按照DI的套路，就可以实现IoC，就能符合DIP原则。 DI的核心是把类所依赖的单元的实例化过程，放到类的外面去实现。

### 控制反转容器（IoC Container）
当项目比较大时，依赖关系可能会很复杂。 而IoC Container提供了动态地创建、注入依赖单元，映射依赖关系等功能，减少了许多代码量。 Yii 设计了一个 yii\di\Container 来实现了 DI Container。
==> Xamarin里全是这些

### 服务定位器（Service Locator）
Service Locator是IoC的另一种实现方式， 其核心是把所有可能用到的依赖单元交由Service Locator进行实例化和创建、配置， 把类对依赖单元的依赖，转换成类对Service Locator的依赖。 DI 与 Service Locator并不冲突，两者可以结合使用。 目前，Yii2.0把这DI和Service Locator这两个东西结合起来使用，或者说通过DI容器，实现了Service Locator。

### 依赖注入
#### 构造函数注入
```php
// 这是构造函数注入的例子
class Comment extend yii\db\ActiveRecord
{
    // 用于引用发送邮件的库
    private $_eMailSender;

    // 构造函数注入
    public function __construct($emailSender)
    {
        ...
        $this->_eMailSender = $emailSender;
        ...
    }

    // 当有新的评价，即 save() 方法被调用之后中，会触发以下方法
    public function afterInsert()
    {
        ...
        //
        $this->_eMailSender->send(...);
        ...
    }
}

// 实例化两种不同的邮件服务，当然，他们都实现了EmailSenderInterface
sender1 = new GmailSender();
sender2 = new MyEmailSender();

// 用构造函数将GmailSender注入
$comment1 = new Comment(sender1);
// 使用Gmail发送邮件
$comment1.save();

// 用构造函数将MyEmailSender注入
$comment2 = new Comment(sender2);
// 使用MyEmailSender发送邮件
$comment2.save();
```
从此以后，无论要使用何何种邮件服务，只需写出新的EmailSenderInterface实现即可， Comment类的代码不再需要作任何更改，多爽的一件事，扩展起来、测试起来都省心省力。
==> 这段代码，说测试方便倒是真的。但要说，使用的直观性来说，差了很多。
==> 甚至都不如修改配置，测试配置来的直观

#### DI容器
依赖单元的实例化代码是一个重复、繁琐的过程。 一个Web应用的某一组件会依赖于若干单元，这些单元又有可能依赖于更低层级的单元， 从而形成依赖嵌套的情形。那么，这些依赖单元的实例化、注入过程的代码可能会比较长，前后关系也需要特别地注意， 必须将被依赖的放在需要注入依赖的前面进行实例化。 这实在是一件既没技术含量，又吃力不出成果的工作。
==> 这倒是真的

Yii的DI容器是 yii\di\Container ，这个容器继承了发明人的高智商， 他知道如何对对象及对象的所有依赖，和这些依赖的依赖，进行实例化和配置。

##### DI容器中的内容
###### DI容器中实例的表示

yii\di\Instance 本质上是DI容器中对于某一个类实例的引用，它的代码看起来并不复杂:
```php
class Instance
{
    // 仅有的属性，用于保存类名、接口名或者别名
    public $id;

    // 构造函数，仅将传入的ID赋值给 $id 属性
    protected function __construct($id)
    {
    }

    // 静态方法创建一个Instance实例
    public static function of($id)
    {
        return new static($id);
    }

    // 静态方法，用于将引用解析成实际的对象，并确保这个对象的类型
    public static function ensure($reference, $type = null, $container = null)
    {
    }

    // 获取这个实例所引用的实际对象，事实上它调用的是
    // yii\di\Container::get()来获取实际对象
    public function get($container = null)
    {
    }
}
```
对于 yii\di\Instance ，我们要了解：

1. 表示的是容器中的内容，代表的是对于实际对象的引用。
2. DI容器可以通过他获取所引用的实际对象。
3. 类仅有的一个属性 id 一般表示的是实例的类型。

DI容器的数据结构

在DI容器中，维护了5个数组，这是DI容器功能实现的基础:
```php
// 用于保存单例Singleton对象，以对象类型为键
private $_singletons = [];

// 用于保存依赖的定义，以对象类型为键
private $_definitions = [];

// 用于保存构造函数的参数，以对象类型为键
private $_params = [];

// 用于缓存ReflectionClass对象，以类名或接口名为键
private $_reflections = [];

// 用于缓存依赖信息，以类名或接口名为键
private $_dependencies = [];
```
##### 注册依赖
使用DI容器，首先要告诉容器，类型及类型之间的依赖关系，声明一这关系的过程称为注册依赖。

使用 yii\di\Container::set() 和 yii\di\Container::setSinglton() 可以注册依赖。

这两个函数功能类似没有太大区别，只是 set() 用于在每次请求时构造新的实例返回， 而 setSingleton() 只维护一个单例，每次请求时都返回同一对象。

##### 对象的实例化
对象的实例化过程要比依赖的定义过程复杂得多。
###### 解析依赖信息

容器在获取实例之前，必须解析依赖信息。 这一过程会涉及到DI容器中尚未提到的另外2个数组 $_reflections 和 $_dependencies 。 

yii\di\Container::getDependencies() 方法用于获取依赖信息。

这个 yii\di\Container::getDependencies() 方法实质上就是通过PHP5 的反射机制， 通过类的构造函数的参数分析他所依赖的单元。然后统统缓存起来备用。
==> 难道继承的父类不用？

为什么是通过构造函数来分析其依赖的单元呢？ 因为这个DI容器设计出来的目的就是为了实例化对象及该对象所依赖的一切单元。 也就是说，DI容器必然构造类的实例，必然调用构造函数，那么必然为构造函数准备并传入相应的依赖单元。 这也是我们开头讲到的构造函数依赖注入的后续延伸应用。
==> DI容器必然构造类的实例

使用DI容器的目的是自动实例化，只是实例化而已，就意味着只需要调用构造函数。 至于setter注入可以在实例化后操作嘛。

```php
protected function resolveDependencies($dependencies, $reflection = null)
{
    foreach ($dependencies as $index => $dependency) {

        // 前面getDependencies() 函数往 $_dependencies[] 中
        // 写入的是一个 Instance 数组
        if ($dependency instanceof Instance) {
            if ($dependency->id !== null) {

                // 向容器索要所依赖的实例，递归调用 yii\di\Container::get()
                $dependencies[$index] = $this->get($dependency->id);
            } elseif ($reflection !== null) {
                $name = $reflection->getConstructor()
                    ->getParameters()[$index]->getName();
                $class = $reflection->getName();
                throw new InvalidConfigException(
                "Missing required parameter \"$name\" when instantiating \"$class\".");
            }
        }
    }
    return $dependencies;
}
```
Instance ==>这是个什么鬼？类似于Java 的Object？

###### 实例的创建

解析完依赖信息，就万事俱备了，那么东风也该来了。实例的创建，秘密就在 yii\di\Container::build() 函数中:

```php
protected function build($class, $params, $config)
{
    // 调用上面提到的getDependencies来获取并缓存依赖信息，留意这里 list 的用法
    list ($reflection, $dependencies) = $this->getDependencies($class);

    // 用传入的 $params 的内容补充、覆盖到依赖信息中
    foreach ($params as $index => $param) {
        $dependencies[$index] = $param;
    }

    // 这个语句是两个条件：
    // 一是要创建的类是一个 yii\base\Object 类，
    // 留意我们在《Yii基础》一篇中讲到，这个类对于构造函数的参数是有一定要求的。
    // 二是依赖信息不为空，也就是要么已经注册过依赖，
    // 要么为build() 传入构造函数参数。
    if (!empty($dependencies) && is_a($class, 'yii\base\Object', true)) {
        // 按照 Object 类的要求，构造函数的最后一个参数为 $config 数组
        $dependencies[count($dependencies) - 1] = $config;

        // 解析依赖信息，如果有依赖单元需要提前实例化，会在这一步完成
        $dependencies = $this->resolveDependencies($dependencies, $reflection);

        // 实例化这个对象
        return $reflection->newInstanceArgs($dependencies);
    } else {
        // 会出现异常的情况有二：
        // 一是依赖信息为空，也就是你前面又没注册过，
        // 现在又不提供构造函数参数，你让Yii怎么实例化？
        // 二是要构造的类，根本就不是 Object 类。
        $dependencies = $this->resolveDependencies($dependencies, $reflection);
        $object = $reflection->newInstanceArgs($dependencies);
        foreach ($config as $name => $value) {
            $object->$name = $value;
        }
        return $object;
    }
}
```
==> $this->getDependencies 找到 这些类的名字
==> $this->resolveDependencies 如果需要，创建 instance

从这个 yii\di\Container::build() 来看：
1. DI容器只支持 yii\base\Object 类。也就是说，你只能向DI容器索要 yii\base\Object 及其子类。 再换句话说，如果你想你的类可以放在DI容器里，那么必须继承自 yii\base\Object 类。 但Yii中几乎开发者在开发过程中需要用到的类，都是继承自这个类。 一个例外就是上面提到的 yii\di\Instance 类。但这个类是供Yii框架自己使用的，开发者无需操作这个类。
==> 这里回答了：Instance 是个什么鬼，是Yii的一个类
2. 递归获取依赖单元的依赖在于 dependencies = $this->resolveDependencies($dependencies, $reflection) 中。
3. getDependencies() 和 resolveDependencies() 为 build() 所用。 也就是说，只有在创建实例的过程中，DI容器才会去解析依赖信息、缓存依赖信息。

###### 容器内容实例化的大致过程

与注册依赖时使用 set() 和 setSingleton() 对应，获取依赖实例化对象使用 yii\di\Container::get() ，其代码如下:

```php
public function get($class, $params = [], $config = [])
{
    // 已经有一个完成实例化的单例，直接引用这个单例
    if (isset($this->_singletons[$class])) {
        return $this->_singletons[$class];

    // 是个尚未注册过的依赖，说明它不依赖其他单元，或者依赖信息不用定义，
    // 则根据传入的参数创建一个实例
    } elseif (!isset($this->_definitions[$class])) {
        return $this->build($class, $params, $config);
    }

    // 注意这里创建了 $_definitions[$class] 数组的副本
    $definition = $this->_definitions[$class];

    // 依赖的定义是个 PHP callable，调用之
    if (is_callable($definition, true)) {
        $params = $this->resolveDependencies($this->mergeParams($class,
            $params));
        $object = call_user_func($definition, $this, $params, $config);

    // 依赖的定义是个数组，合并相关的配置和参数，创建之
    } elseif (is_array($definition)) {
        $concrete = $definition['class'];
        unset($definition['class']);

        // 合并将依赖定义中配置数组和参数数组与传入的配置数组和参数数组合并
        $config = array_merge($definition, $config);
        $params = $this->mergeParams($class, $params);

        if ($concrete === $class) {
            // 这是递归终止的重要条件
            $object = $this->build($class, $params, $config);
        } else {
            // 这里实现了递归解析
            $object = $this->get($concrete, $params, $config);
        }

    // 依赖的定义是个对象则应当保存为单例
    } elseif (is_object($definition)) {
        return $this->_singletons[$class] = $definition;
    } else {
        throw new InvalidConfigException(
            "Unexpected object definition type: " . gettype($definition));
    }

    // 依赖的定义已经定义为单例的，应当实例化该对象
    if (array_key_exists($class, $this->_singletons)) {
        $this->_singletons[$class] = $object;
    }

    return $object;
}
```

get() 用于返回一个对象或一个别名所代表的对象。可以是已经注册好依赖的，也可以是没有注册过依赖的。 无论是哪种情况，Yii均会自动解析将要获取的对象对外部的依赖。

get() 接受3个参数：

1. $class 表示将要创建或者获取的对象。可以是一个类名、接口名、别名。
2. $params 是一个用于这个要创建的对象的构造函数的参数，其参数顺序要与构造函数的定义一致。 通常用于未定义的依赖。
3. $config 是一个配置数组，用于配置获取的对象。通常用于未定义的依赖，或覆盖原来依赖中定义好的配置。


get() 不直接实例化对象，也不直接解析依赖信息。而是通过 build() 来实例化对象和解析依赖。

get() 会根据依赖定义，递归调用自身去获取依赖单元。 因此，在整个实例化过程中，一共有两个地方会产生递归：一是 get() ， 二是 build() 中的 resolveDependencies() 。

从 get() 的代码可以看出：
>* 对于已经实例化的单例，使用 get() 时只能返回已经实例化好的实例， $params 参数和 $config 参数失去作用。这点要注意，Yii不会提示你，所给出的参数不会发生作用的。 有的时候发现明明已经给定配置数组了，怎么配置不起作用呀？就要考虑是不是因为这个原因了。
==> 这也是一个坑，而且隐藏的比较深

>* 对于定义为数组的依赖，在合并配置数组和构造函数参数数组过程中， 定义中定义好的两个数组会被传入的 $config 和 $params 的同名元素所覆盖， 这就提供了获取不同实例的可能。

>* 在定义依赖时，无论是使用 set() 还是使用 setSingleton() 只要依赖定义为特定对象或特定实例的， Yii均将其视为单例。在获取时，也将返回这一单例。

###### 实例分析
```php
use yii\di\Container;

// 创建一个DI容器
$container = new Container;

// 为Connection指定一个数组作为依赖，当需要Connection的实例时，
// 使用这个数组进行创建
$container->set('yii\db\Connection', [
    'dsn' => '...',
]);

// 在需要使用接口 UserFinderInterface 时，采用UserFinder类实现
$container->set('app\models\UserFinderInterface', [
    'class' => 'app\models\UserFinder',
]);

// 为UserLister定义一个别名
$container->set('userLister', 'app\models\UserLister');

// 获取这个UserList的实例
$lister = $container->get('userLister');
```

采用DI容器的办法，首先各 set() 语句没有前后关系的要求， set() 只是写入特定的数据结构， 并未涉及具体依赖关系的解析。所以，前后关系不重要，先定义什么依赖，后定义什么依赖没有关系。

其次，上面根本没有在DI容器中定义 UserFinder 对于 Connection 的依赖。 但是DI容器通过对 UserFinder 构造函数的分析，能了解到这个类会对 Connection 依赖。这个过程是自动的。

==> 这样真的很好吗？如果 Connection 需要是 singleton 就做成 singleton嘛。大家都知道它是singleton

经过上面的几个 set() 语句之后，DI容器的 $_params 数组是空的， $_singletons 数组也是空的。 $_definintions 数组却有了新的内容:
```php
$_definitions = [
    'yii\db\Connection' => [
        'class' => 'yii\db\Connection',    // 注意这里
        'dsn' => ...
    ],
    'app\models\UserFinderInterface' => ['class' => 'app\models\UserFinder'],
    'userLister' => ['class' => 'app\models\UserLister']    // 注意这里
];
```
==> 这倒是一个很好的思路，把这五个 数组 打印出来看看。

但是，对于典型的Web应用而言， 有许多模块其实应当注册为单例的，比如上面的 yii\db\Connection 。 一个Web应用一般使用一个数据库连接，特殊情况下会用多几个，所以这些数据库连接一般是给定不同别名加以区分后， 分别以单例形式放在容器中的。

# 服务定位器（Service Locator）
服务定位器（Service Locator）

跟DI容器类似，引入Service Locator目的也在于解耦。有许多成熟的设计模式也可用于解耦，但在Web应用上， Service Locator绝对占有一席之地。 对于Web开发而言，Service Locator天然地适合使用， 主要就是因为Service Locator模式非常贴合Web这种基于服务和组件的应用的运作特点。 这一模式的优点有：
==> 解耦，这个倒是优点很多

1. Service Locator充当了一个运行时的链接器的角色，可以在运行时动态地修改一个类所要选用的服务， 而不必对类作任何的修改。
2. 一个类可以在运行时，有针对性地增减、替换所要用到的服务，从而得到一定程度的优化。
实现服务提供方、服务使用方完全的解耦，便于独立测试和代码跨框架复用。

## Service Locator的基本功能
在Yii中Service Locator由 yii\di\ServiceLocator 来实现。 从代码组织上，Yii将Service Locator放到与DI同一层次来对待，都组织在 yii\di 命名空间下。

Service Locator 通过 __get() __isset() has() 等方法， 扩展了 yii\base\Component 的最基本功能
提供了对于服务和组件的属性化支持。
==> 属性化
==> 这个东西应该是：Component的依赖性注入

Service Locator维护了两个数组， $_components 和 $_definitions 。这两个数组均是以服务或组件的ID为键的数组。

$_definitions 用于保存这些组件或服务的定义。这个定义可以是：

1. 配置数组。在向Service Locator索要服务或组件时，这个数组会被用于创建服务或组件的实例。 与DI容器的要求类似，当定义是配置数组时，要求配置数组必须要有 class 元素，表示要创建的是什么类。不然你让Yii调用哪个构造函数？
2. PHP callable。每当向Service Locator索要实例时，这个PHP callable都会被调用，其返回值，就是所要的对象。 对于这个PHP callable有一定的形式要求，一是它要返回一个服务或组件的实例。 二是它不接受任何的参数。 至于具体原因，后面会讲到。
3. 对象。这个更直接，每当你索要某个特定实例时，直接把这个对象给你就是了。
4. 类名。即，使得 is_callable($definition, true) 为真的定义。


==> 服务或组件的ID在Service Locator中是唯一的，用于区别彼此。
==> 在任何情况下，Service Locator中同一ID只有一个实例、一个定义。
==> 也就是说，Service Locator中，所有的服务和组件，只保存一个单例。
==> 这也是正常的逻辑，既然称为服务定位器，你只要给定一个ID，它必然返回一个确定的实例。这一点跟DI容器是一样的。

## 访问Service Locator中的服务
Service Locator重载了 __get() 使得可以像访问类的属性一样访问已经实例化好的服务和组件。
==> 重载 __get()，就是属性化的关键。

```php
// 创建一个Service Locator
$serviceLocator = new yii\di\ServiceLocator;

// 注册一个 cache 服务
$serviceLocator->set('cache', [
    'class' => 'yii\cache\MemCache',
    'servers' => [
        ... ...
    ],
]);

// 使用访问属性的方法访问这个 cache 服务
$serviceLocator->cache->flushValues();

// 上面的方法等效于下面这个
$serviceLocator->get('cache')->flushValues();
```

>* 在Service Locator中，并未重载 __set() 。所以，Service Locator中的服务和组件看起来就好像只读属性一样。 要向Service Locator中“写”入服务和组件，没有 setter 可以使用，需要调用 yii\di\ServiceLocator::set() 对服务和组件进行注册。

## 通过Service Locator获取实例

## 在Yii应用中使用Service Locator和DI容器
```php
<?php

require(__DIR__ . '/BaseYii.php');

class Yii extends \yii\BaseYii
{
}

spl_autoload_register(['Yii', 'autoload'], true, true);
Yii::$classMap = include(__DIR__ . '/classes.php');

// 重点看这里。创建一个DI 容器，并由 Yii::$container 引用
Yii::$container = new yii\di\Container;
```

==> 这里创建了一个DI容器，并由 Yii::$container 引用。
==> 在Yii应用中，我们可以随时使用 Yii::$container 来访问DI容器。 
==> 一般情况下，不要自己创建DI容器，使用 Yii::$container 完全足够。

## Application的本质

所有的Application也都是Service Locator。
==> PHP 如果可以在IDE调试的话，像C#、Java那样，一眼就可以看清继承关系了。

在Application的构造函数中， yii\base\Application::__construct()
```php
public function __construct($config = [])
{
    Yii::$app = $this;
    ... ...
}
```

第一行代码就把Application当前的实例，赋值给 Yii::$app 了。 
这意味着Yii应用创建之后，可以随时通过 Yii::$app 来访问应用自身，也就是访问Service Locator。

## 实例创建方法

Service Locator和DI容器的亲密关系就隐藏在 yii\di\ServiceLocator::get() 获取实例时， 调用的 Yii::createObject() 中。

```php
// static::$container就是上面说的引用了DI容器的静态变量

public static function createObject($type, array $params = [])
{
    // 字符串，代表一个类名、接口名、别名。
    if (is_string($type)) {
        return static::$container->get($type, $params);

    // 是个数组，代表配置数组，必须含有 class 元素。
    } elseif (is_array($type) && isset($type['class'])) {
        $class = $type['class'];
        unset($type['class']);

        // 调用DI容器的get() 来获取、创建实例
        return static::$container->get($class, $params, $type);

    // 是个PHP callable则调用其返回一个具体实例。
    } elseif (is_callable($type, true)) {

        // 是个PHP callable，那就调用它，并将其返回值作为服务或组件的实例返回
        return call_user_func($type, $params);

    // 是个数组但没有 class 元素，抛出异常
    } elseif (is_array($type)) {
        throw new InvalidConfigException(
        'Object configuration must be an array containing a "class" element.');

    // 其他情况，抛出异常
    } else {
        throw new InvalidConfigException(
            "Unsupported configuration type: " . gettype($type));
    }
}
```
==> 这个 createObject() 提供了一个向DI容器获取实例的接口，对于不同的定义，除了PHP callable外， createObject() 都是调用了DI容器的 yii\di\Container::get() ，来获取实例的。 Yii::createObject() 就是也是Service Locator构建于DI容器之上的证明。

==> 而Yii中所有的Module， 包括Application都是Service Locator，因此，它们也都构建在DI容器之上。
同时，在Yii框架代码中，只要创建实例，就是调用 Yii::createObject() 这个方法来实现。 
==> 可以说，Yii中所有的实例（除了Application，DI容器自身等入口脚本中实例化的），都是通过DI容器来获取的。

Yii 的基类 yii\BaseYii ，所有的成员变量和方法都是静态的， 其中的DI容器是个静态成员变量 $container 。
==>  因此，DI容器就形成了最常见形式的单例模式，在内存中仅有一份，
==> 所有的Service Locator （Module和Application）都共用这个DI容器。
==> 这不就类似与C++项目中的log模块嘛

当Service Locator中服务或组件的定义是一个PHP callable时，对其形式有一定要求。 一是返回一个实例，二是不接收任何参数。

# 路由(Route)

Web开发中不可避免的要使用到URL。用得最多的，就是生成一个指向应用中其他某个页面的URL了。 开发者需要一个简洁的、集中的、统一的方法来完成这一过程。

YIi中所有的用户请求都是发送给入口脚本 index.php 来处理的。那么，Yii需要提供一种高效的分派 请求的方法，来判断请求应当采用哪个 controller 哪个 action 进行处理。

结合以上2点需求，Yii为开发者提供了路由和URL管理组件。

所谓路由是指URL中用于标识用于处理用户请求的module, controller, action的部分， 一般情况下由 r 查询参数来指定。 如 http://www.digpage.com/index.php?r=post/view&id=100 ， 表示这个请求将由PostController 的 actionView来处理。
==> 路由是 module, controller, action

同时，Yii也提供了一种美化URL的功能。
这个功能的实现是依赖于一个称为 urlManager 的应用组件。

1. 使用 urlManager 开发者可以解析用户的请求，并指派相应的module, controller和action来进行处理
2. 还可以根据预义的路由规则，生成需要的URL返回给用户使用。 
==> 简而言之，urlManger具有解析请求以便确定指派谁来处理请求和根据路由规则生成URL 2个功能。

## 美化URL
一般情况下，Yii应用生成和接受形如 http://www.digpage.com/index.php?r=post/view&id=100 的URL。这个URL分成几个部分：

1. 表示主机信息的 http://www.digapge.com
2. 表示入口脚本的 index.php
3. 表示路由的 r=post/view
4. 表示普通查询参数的 id=100


Nginx：
```
location / {
    try_files $uri $uri/ /index.php?$args;
}
```
==> 即然这样，URL中有没有指定 index.php 已经不重要了，反正都是请求的它

如果将URL省略掉入口脚本，并将路由信息转换成路径，上面的URL就会变成： http://www.digpage.com/post/view?id=100 

我们把 URL http://www.digpage.comindex.php?r=post/view&id=100 变成 http://www.digpage.com/post/view/Route.html 的过程就称为URL美化。

Yii有专门的 yii\web\UrlManager 来进行处理，其中：

1. 隐藏入口脚本可以通过 yii\web\UrlManager::showScriptName = false 来实现
==> 这一步不是通过web 服务器的配置来实现？
2. 路由的路径化可以通过 yii\web\UrlManager::enablePrettyUrl = true 来实现
3. 参数的路径化可以通过路由规则来实现
4. 假后缀(fake suffix) .html 可以通过 yii\web\UrlManager::suffix = '.html' 来实现

## 路由规则
路由规则是指 urlManager 用于解析请求或生成URL的规则。

一个路由规则必须实现 yii\web\UrlRuleInterface 接口，这个接口定义了两个方法：

1. 用于解析请求的 yii\web\UrlRuleInterface::parseRequest()
2. 用于生成URL的 yii\web\UrlRuleInterface::createUrl()

Yii中，使用 yii\web\UrlRule 来表示路由规则。

以几个相对简单、典型的路由规则的为例， 先有个感性认识:
```php
'rules' => [
    // 为路由指定了一个别名，以 post 的复数形式来表示 post/index 路由
    'posts' => 'post/index',

    // id 是命名参数，post/100 形式的URL，其实是 post/view&id=100
    'post/<id:\d+>' => 'post/view',

    // controller action 和 id 以命名参数形式出现
    '<controller:(post|comment)>/<id:\d+>/<action:(create|update|delete)>'
        => '<controller>/<action>',

    // 包含了 HTTP 方法限定，仅限于DELETE方法
    'DELETE <controller:\w+>/<id:\d+>' => '<controller>/delete',

    // 需要将 Web Server 配置成可以接收 *.digpage.com 域名的请求
    'http://<user:\w+>.digpage.com/<lang:\w+>/profile' => 'user/profile',
]
```

```php
'rules' => [
    // 为路由指定了一个别名，以 post 的复数形式来表示 post/index 路由
    'posts' => 'post/index',

    // id 是命名参数，post/100 形式的URL，其实是 post/view&id=100
    'post/<id:\d+>' => 'post/view',

    // controller action 和 id 以命名参数形式出现
    '<controller:(post|comment)>/<id:\d+>/<action:(create|update|delete)>'
        => '<controller>/<action>',

    // 包含了 HTTP 方法限定，仅限于DELETE方法
    'DELETE <controller:\w+>/<id:\d+>' => '<controller>/delete',

    // 需要将 Web Server 配置成可以接收 *.digpage.com 域名的请求
    'http://<user:\w+>.digpage.com/<lang:\w+>/profile' => 'user/profile',
]
```
==> 包含 HTTP 方法 的第一次见
==> 限定域名的也第一次见

数组的键相当于请求（需要解析的或将要生成的），而元素的值则对应的路由， 即 controller/action 。
请求部分可称为pattern，路由部分则可称为route。 

>* pattern 是从正则表达式变形而来。去除了两端的 / # 等分隔符。 特别注意别在pattern两端画蛇添足加上分隔符。
>* pattern 中可以使用正则表达式的命名参数，以供route部分引用。这个命名参数也是变形了的。 对于原来 (?P<name>pattern) 的命名参数，要变形成 <name:pattern> 。
>* pattern 中可以使用HTTP方法限定。
>* route 不应再含有正则表达式，但是可以按 <name> 的形式引用命名参数。

```php
class UrlRule extends Object implements UrlRuleInterface
{
    // 用于 $mode 表示路由规则的2种工作模式：仅用于解析请求和仅用于生成URL。
    // 任意不为1或2的值均表示两种模式同时适用，
    // 一般未设定或为0时即表示两种模式均适用。
    const PARSING_ONLY = 1;
    const CREATION_ONLY = 2;

    // 路由规则名称
    public $name;

    // 用于解析请求或生成URL的模式，通常是正则表达式
    public $pattern;

    // 用于解析或创建URL时，处理主机信息的部分，如 http://www.digpage.com
    public $host;

    // 指向controller 和 action 的路由
    public $route;

    // 以一组键值对数组指定若干GET参数，在当前规则用于解析请求时，
    // 这些GET参数会被注入到 $_GET 中去
    public $defaults = [];

    // 指定URL的后缀，通常是诸如 ".html" 等，
    // 使得一个URL看起来好像指向一个静态页面。
    // 如果这个值未设定，使用 UrlManager::suffix 的值。
    public $suffix;

    // 指定当前规则适用的HTTP方法，如 GET, POST, DELETE 等。
    // 可以使用数组表示同时适用于多个方法。
    // 如果未设定，表明当前规则适用于所有方法。
    // 当然，这个属性仅在解析请求时有效，在生成URL时是无效的。
    public $verb;

    // 表明当前规则的工作模式，取值可以是 0, PARSING_ONLY, CREATION_ONLY。
    // 未设定时等同于0。
    public $mode;

    // 表明URL中的参数是否需要进行url编码，默认是进行。
    public $encodeParams = true;

    // 用于生成新URL的模板
    private $_template;

    // 一个用于匹配路由部分的正则表达式，用于生成URL
    private $_routeRule;

    // 用于保存一组匹配参数的正则表达式，用于生成URL
    private $_paramRules = [];

    // 保存一组路由中使用的参数
    private $_routeParams = [];

    // 初始化
    public function init() {...}

    // 用于解析请求，由UrlRequestInterface接口要求
    public function parseRequest($manager, $request) {...}

    // 用于生成URL，由UrlRequestInterface接口要求
    public function createUrl($manager, $route, $params) {...}
}
```


以上面的 ['post/<action:\w+>/<id:\d+>' => 'post/<action>'] 为例，经过 init() 处理后，我们最终得到了:
```php
$urlRule->route = 'post/<action>';
$urlRule->pattern = '#^post/(?P<action>\w+)(/(?P<id>\d+))?$#u';
$urlRule->_template = '/post/<action>/<id>/';
$urlRule->_routeRule = '#^post/(?P<action>\w+)$#';
$urlRule->_routeParams = ['action' => '<action>'];
$urlRule->_paramRules = ['id' => '#^\d+$#u'];
// $tr 和 $tr2 作为局部变量已经完成历史使命光荣退伍了
```
==> 这个东西既是用来解析浏览器发来的URL，也是在项目里生成一个URL的。是双向的。
==> 路由、模式、模板、路由规则、路由参数、参数规则

## 创建URL
URL的创建就 UrlRule 层面来讲，是由 yii\web\UrlRule::createUrl() 负责的。
```php
public function createUrl($manager, $route, $params)
{
    // 判断规则是否仅限于解析请求，而不适用于创建URL
    if ($this->mode === self::PARSING_ONLY) {
        return false;
    }
    $tr = [];

    // 如果传入的路由与规则定义的路由不一致，
    // 如 post/view 与 post/<action> 并不一致
    if ($route !== $this->route) {

        // 使用 $_routeRule 对 $route 作匹配测试
        if ($this->_routeRule !== null && preg_match($this->_routeRule,
            $route, $matches)) {

            // 遍历所有的 _routeParams
            foreach ($this->_routeParams as $name => $token) {
                // 如果该路由规则提供了默认的路由参数，
                // 且该参数值与传入的路由相同，则可以省略
                if (isset($this->defaults[$name]) &&
                    strcmp($this->defaults[$name], $matches[$name]) === 0) {
                    $tr[$token] = '';
                } else {
                    $tr[$token] = $matches[$name];
                }
            }
        // 传入的路由完全不能匹配该规则，返回
        } else {
            return false;
        }
    }

    // 遍历所有的默认参数
    foreach ($this->defaults as $name => $value) {
        // 如果默认参数是路由参数，如 <action>
        if (isset($this->_routeParams[$name])) {
            continue;
        }

        // 默认参数并非路由参数，那么看看传入的 $params 里是否提供该参数的值。
        // 如果未提供，说明这个规则不适用，直接返回。
        if (!isset($params[$name])) {
            return false;

        // 如果 $params 提供了该参数，且参数值一致，则 $params 可省略该参数
        } elseif (strcmp($params[$name], $value) === 0) {
            unset($params[$name]);

            // 且如果有该参数的转换规则，也可置为空。等下一转换就消除了。
            if (isset($this->_paramRules[$name])) {
                $tr["<$name>"] = '';
            }

        // 如果 $params 提供了该参数，但又与默认参数值不一致，
        // 且规则也未定义该参数的正则，那么规则无法处理这个参数。
        } elseif (!isset($this->_paramRules[$name])) {
            return false;
        }
    }

    // 遍历所有的参数匹配规则
    foreach ($this->_paramRules as $name => $rule) {

        // 如果 $params 传入了同名参数，且该参数不是数组，且该参数匹配规则，
        // 则使用该参数匹配规则作为转换规则，并从 $params 中去掉该参数
        if (isset($params[$name]) && !is_array($params[$name])
            && ($rule === '' || preg_match($rule, $params[$name]))) {
            $tr["<$name>"] = $this->encodeParams ?
                urlencode($params[$name]) : $params[$name];
            unset($params[$name]);

        // 否则一旦没有设置该参数的默认值或 $params 提供了该参数，
        // 说明规则又不匹配了
        } elseif (!isset($this->defaults[$name]) || isset($params[$name])) {
            return false;
        }
    }

    // 使用 $tr 对 $_template 时行转换，并去除多余的 '/'
    $url = trim(strtr($this->_template, $tr), '/');

    // 将 $url 中的多个 '/' 变成一个
    if ($this->host !== null) {
        // 再短的 host 也不会短于 8
        $pos = strpos($url, '/', 8);
        if ($pos !== false) {
            $url = substr($url, 0, $pos) . preg_replace('#/+#', '/',
                substr($url, $pos));
        }
    } elseif (strpos($url, '//') !== false) {
        $url = preg_replace('#/+#', '/', $url);
    }

    // 加上 .html 之类的假后缀
    if ($url !== '') {
        $url .= ($this->suffix === null ? $manager->suffix : $this->suffix);
    }

    // 加上查询参数们
    if (!empty($params) && ($query = http_build_query($params)) !== '') {
        $url .= '?' . $query;
    }
    return $url;
}
```

从创建URL的过程来看，重点是完成这么几项工作：
1. 看规则是否适用，主要标准是路由与规则定义的是否匹配，规则通过默认值或正则所定义的参数，是否都提供了。
2. 看看当前要创建的URL是否与规则定义的默认的路由参数和查询参数一致，对于一致的，可以省略。
3. 看将这些与默认值一致的，规则已经定义了的参数从 $params 删除，余下的，转换成最终URL的查询参数串。

## 解析URL
先从路由规则 yii\web\UrlRule::parseRequest() 的代码入手:
```php
public function parseRequest($manager, $request)
{
    // 当前路由规则仅限于创建URL，直接返回 false。
    // 该方法返回false表示当前规则不适用于当前的URL。
    if ($this->mode === self::CREATION_ONLY) {
        return false;
    }

    // 如果规则定义了适用的HTTP方法，则要看当前请求采用的方法是否可以接受
    if (!empty($this->verb) && !in_array($request->getMethod(),
        $this->verb, true)) {
        return false;
    }

    // 获取URL中入口脚本之后、查询参数 ? 号之前的全部内容，即为PATH_INFO
    $pathInfo = $request->getPathInfo();
    // 取得配置的 .html 等假后缀，留意 (string)null 转成空串
    $suffix = (string) ($this->suffix === null ? $manager->suffix :
        $this->suffix);

    // 有假后缀且有PATH_INFO
    if ($suffix !== '' && $pathInfo !== '') {
        $n = strlen($suffix);

        // 当前请求的 PATH_INFO 以该假后缀结尾，留意 -$n 的用法
        if (substr_compare($pathInfo, $suffix, -$n, $n) === 0) {
            $pathInfo = substr($pathInfo, 0, -$n);

            // 整个PATH_INFO 仅包含一个假后缀，这是无效的。
            if ($pathInfo === '') {
                return false;
            }

        // 应用配置了假后缀，但是当前URL却不包含该后缀，返回false
        } else {
            return false;
        }
    }

    // 规则定义了主机信息，即 http://www.digpage.com 之类，那要把主机信息接回去。
    if ($this->host !== null) {
        $pathInfo = strtolower($request->getHostInfo()) .
            ($pathInfo === '' ? '' : '/' . $pathInfo);
    }

    // 当前URL是否匹配规则，留意这个pattern是经过 init() 转换的
    if (!preg_match($this->pattern, $pathInfo, $matches)) {
        return false;
    }

    // 遍历规则定义的默认参数，如果当前URL中没有，则加入到 $matches 中待统一处理，
    // 默认值在这里发挥作用了，虽然没有，但仍视为捕获到了。
    foreach ($this->defaults as $name => $value) {
        if (!isset($matches[$name]) || $matches[$name] === '') {
            $matches[$name] = $value;
        }
    }
    $params = $this->defaults;
    $tr = [];

    // 遍历所有匹配项，注意这个 $name 的由来是 (?P<name>...) 的功劳
    foreach ($matches as $name => $value) {
        // 如果是匹配一个路由参数
        if (isset($this->_routeParams[$name])) {
            $tr[$this->_routeParams[$name]] = $value;
            unset($params[$name]);

        // 如果是匹配一个查询参数
        } elseif (isset($this->_paramRules[$name])) {
            // 这里可能会覆盖掉 $defaults 定义的默认值
            $params[$name] = $value;
        }
    }

    // 使用 $tr 进行转换
    if ($this->_routeRule !== null) {
        $route = strtr($this->route, $tr);
    } else {
        $route = $this->route;
    }
    Yii::trace("Request parsed with URL rule: {$this->name}", __METHOD__);
    return [$route, $params];
}
```

## 从Web Server说起
以Nginx为例：
```
location / {
    try_files $uri $uri/ /index.php?$args;
}

# fastcgi.conf
fastcgi_split_path_info ^(.+?\.php)(/.*)$;
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
```
try_files 会依次尝试处理：

1. /post/view.html ，这个如果真有一个也就罢了，但其实多半不存在。
2. /post/view.html/ ，这个目录一般也不存在。
3. /index.php ，这个正是入口脚本，可以处理。至此，Nginx与Yii顺利交接。

由于请求最终交给入口脚本来处理，且我们隐藏了URL中入口脚本名，上述请求还原回来的话， 应该是 http://www.digapge.com/index.php/post/view.html 。
==> 这 post/view.html 就是PATH_INFO了。

在Yii从Web Server取得控制权之后，就是我们大显身手的时候了。在解析过程中，UrlRule主要做了这么几件事：

1. 通过 PATH_INFO 还原请求，如去除假后缀，开头接上主机信息等。还原后的请求为 post/view 。
2. 看看当前请求是否匹配规则，这个匹配包含了主机、路由、参数等各方面的匹配。如不匹配，说明规则不适用， 返回 false 。在这个例子中，规则并未定义主机信息方面的规则， 规则中 $pattern = '#^post/(?P<action>\w+)(/(?P<id>\d+))?$#u' 。这与还原后的请求完全匹配。 如果URL没有使用默认值 id = 100 ，如 post/view/101.html ，也是同样匹配的。
3. 看看请求是否提供了规则已定义了默认值的所有参数，如果未提供，视为请求提供了这些参数，且他的值为默认值。 这里URL中并未提供 id 参数，所以，视为他提供了 id = 100 的参数。简单粗暴而有效。
4. 使用规则定义的路由进行转换，生成新的路由。 再把上一步及当前所有参数作为路由的参数，共同组装成一个完整路由。

# Url管理
在Web开发中，对于URL有一些共性的需求，如：

1. 统一、简洁的URL创建方式
2. URL的伪静态化（美化）处理
3. 从URL中解析出相应的路由信息，引导应用执行后续处理

这些功能在 前面我们讲的 UrlRule 层面已经得到了一定程度的实现。 但从层次上来讲，UrlRule 更偏向于基础一些，直接使用 UrlRule 相对而言还不是很方便。

Yii把Web应用中对于URL的常用要求抽象到了urlManager中， 并作为Web应用的核心组件，更便于开发者使用。

## urlManager概览
urlManager组件由 yii\web\UrlManager 类定义:
```php
class UrlManager extends Component
{

    // 用于表明urlManager是否启用URL美化功能，在Yii1.1中称为path格式URL，
    // Yii2.0中改称美化。
    // 默认不启用。但实际使用中，特别是产品环境，一般都会启用。
    public $enablePrettyUrl = false;

    // 是否启用严格解析，如启用严格解析，要求当前请求应至少匹配1个路由规则，
    // 否则认为是无效路由。
    // 这个选项仅在 enablePrettyUrl 启用后才有效。
    public $enableStrictParsing = false;

    // 保存所有路由规则的配置数组，并不在这里保存路由规则的实例
    public $rules = [];

    // 指定续接在URL后面的一个后缀，如 .html 之类的。仅在 enablePrettyUrl 启用时有效。
    public $suffix;

    // 指定是否在URL在保留入口脚本 index.php
    public $showScriptName = true;

    // 指定不启用 enablePrettyUrl 情况下，URL中用于表示路由的查询参数，默认为 r
    public $routeParam = 'r';

    // 指定应用的缓存组件ID，编译过的路由规则将通过这个缓存组件进行缓存。
    // 由于应用的缓存组件默认为 cache ，所以这里也默认为 cache 。
    // 如果不想使用缓存，需显式地置为 false
    public $cache = 'cache';

    // 路由规则的默认配置，注意上面的 rules[] 中的同名规则，优先于这个默认配置的规则。
    public $ruleConfig = ['class' => 'yii\web\UrlRule'];

    private $_baseUrl;
    private $_scriptUrl;
    private $_hostInfo;

    // urlManager 初始化
    public function init()
    {
        parent::init();
        // 如果未启用 enablePrettyUrl 或者没有指定任何的路由规则，
        // 这个urlManager不需要进一步初始化。
        if (!$this->enablePrettyUrl || empty($this->rules)) {
            return;
        }

        // 初始化前， $this->cache 是缓存组件的ID，是个字符串，需要获取其实例。
        if (is_string($this->cache)) {
            // 如果获取不到实例，说明应用不提供缓存功能，
            // 那么置这个 $this->cache 为false
            $this->cache = Yii::$app->get($this->cache, false);
        }

        // 如果顺利引用到了缓存组件，那么就将路由规则缓存起来
        if ($this->cache instanceof Cache) {

            // 以当前urlManager类的类名为缓存的键
            $cacheKey = __CLASS__;
            // urlManager所有路由规则转换为json格式编码后的HASH值，
            // 用于确保缓存中的路由规则没有变化。
            // 即外部没有对已经缓存起来的路由规则有增加、修改、
            // 删除、调整前后位置等操作。
            $hash = md5(json_encode($this->rules));

            // cache中是一个数组, 0号元素用于缓存创建好的路由规则，
            // 1号元素用于保存HASH值。这个判断用于确认是否有缓存、且缓存仍有效。
            // 是的话，直接使用缓存中的内容作为当前的路由规则数组。
            if (($data = $this->cache->get($cacheKey)) !== false
                && isset($data[1]) && $data[1] === $hash) {
                $this->rules = $data[0];

            // 如果尚未缓存或路由规则已经被修改导致缓存失效，
            // 那么重新创建路由规则并缓存。
            } else {
                $this->rules = $this->buildRules($this->rules);
                $this->cache->set($cacheKey, [$this->rules, $hash]);
            }

        // 要么是应用不提供缓存功能，要么是开发者将 $this->cache 手动置为false，
        // 总之，就是不使用缓存。那么就直接创建吧，也无需缓存了。
        } else {
            $this->rules = $this->buildRules($this->rules);
        }
    }

    // 增加新的规则
    public function addRules($rules, $append = true){ ... }

    // 创建路由规则
    protected function buildRules($rules){ ... }

    // 用于解析请求
    public function parseRequest($request){ ... }

    // 这2个用于创建URL
    public function createUrl($params){ ... }
    public function createAbsoluteUrl($params, $scheme = null){ ... }
}
```

在urlManager的使用上，用得最多的配置项就是：

1. $enablePrettyUrl ，是否开启URL美化功能。关于美化功能，我们在 路由(Route) 部分已经介绍过了。 注意如果 $enablePrettyUrl 不开启，表明使用原始的格式，那么所有路由规则都是无效的。
2. $showScriptName ，是否在URL中显示入口脚本。是对美化功能的进一步补充。
3. suffix 设置一个 .html 之类的假后缀，是对美化功能的进一步补充。
4. rules 保存路由规则们的声明，注意并非保存其实例。
5. $enableStrictParsing 是否开启严格解析。该选项仅在开启美化功能后生效。在开启严格解析模式时， 所有请求必须匹配 $rules[] 所声明的至少一个路由规则。 如果未开启，请求的PATH_INFO部分将作为所请求的路由进行后续处理。

在 UrlManager::init() 初始化过程中，可以发现 urlManager 使用了应用所提供的缓存组件（有果有的话）， 对所有路由规则的实例进行缓存。

从架构上来讲，将所有请求交由入口脚本统一接收，再分发到相应模块进行处理的这种方式， 就注定了入口脚本有产生性能瓶颈的可能。
==> 这是个问题，不知道有没有解决方案？

目前这种单一入口脚本的设计成为事实上的标准，几乎所有的Web开发框架都采用这种方式。 但这同时也对各框架的性能提出挑战。

Yii很好地平稳了性能与开发便利性，通过将路由规则进行缓存来克服这个瓶颈。
==> 这就是 urlManager 里缓冲的意义了。

# 请求(Reqeust)
## 获取用户请求

PHP并未提供集中的、统一的界面以获取用户请求，而是分散在 $_SERVER $_POST 等变量和其他代码中。 
对于任何Yii应用而言，初始化后第一件正事，就是获取用户请求。 这个代码在 yii\base\Application::run() 中:

```php
public function run()
{
    try {

        $this->state = self::STATE_BEFORE_REQUEST;
        $this->trigger(self::EVENT_BEFORE_REQUEST);

        $this->state = self::STATE_HANDLING_REQUEST;

        // 获取用户请求，并进行处理，处理的过程也是产生响应内容的过程
        $response = $this->handleRequest($this->getRequest());

        $this->state = self::STATE_AFTER_REQUEST;
        $this->trigger(self::EVENT_AFTER_REQUEST);

        $this->state = self::STATE_SENDING_RESPONSE;

        // 将响应内容发送回用户
        $response->send();

        $this->state = self::STATE_END;

        return $response->exitStatus;

    } catch (ExitException $e) {

        $this->end($e->statusCode, isset($response) ? $response : null);
        return $e->statusCode;

    }
}
```
$this->getRequest() 就是用于获取用户请求。其实这是一个getter，用于获取Application的request组件 (component) 。Yii用这个组件来代表用户请求，他承载着所有的用户输入信息。

Yii应用有命令行(Console)应用和Web应用之分。因此，这个Request类其实涉及到了以下的类：

1. yii\base\Request Request类基类
2. yii\console\Request 表示Console应用的的Request
3. yii\web\Request 表示Web应用的Request

### 基类Request
```php
abstract class Request extends Component
{
    // 属性scriptFile，用于表示入口脚本
    private $_scriptFile;

    // 属性isConsoleRequest，用于表示是否是命令行应用
    private $_isConsoleRequest;


    // 虚函数，要求子类来实现
    // 这个函数的功能主要是为了把Request解析成路由和相应的参数
    abstract public function resolve();

    // isConsoleRequest属性的getter函数
    // 使用 PHP_SAPI 常量判断当前应用是否是命令行应用
    public function getIsConsoleRequest()
    {
        // 一切 PHP_SAPI 不为 'cli' 的，都不是命令行
        return $this->_isConsoleRequest !== null ?
            $this->_isConsoleRequest : PHP_SAPI === 'cli';
    }

    // isConsoleRequest属性的setter函数
    public function setIsConsoleRequest($value)
    {
        $this->_isConsoleRequest = $value;
    }

    // scriptFile属性的getter函数
    // 通过 $_SERVER['SCRIPT_FILENAME'] 来获取入口脚本名
    public function getScriptFile()
    {
        if ($this->_scriptFile === null) {
            if (isset($_SERVER['SCRIPT_FILENAME'])) {
                $this->setScriptFile($_SERVER['SCRIPT_FILENAME']);
            } else {
                throw new InvalidConfigException(
                    'Unable to determine the entry script file path.');
            }
        }

        return $this->_scriptFile;
    }

    // scriptFile属性的setter函数
    public function setScriptFile($value)
    {
        $scriptFile = realpath(Yii::getAlias($value));
        if ($scriptFile !== false && is_file($scriptFile)) {
            $this->_scriptFile = $scriptFile;
        } else {
            throw new InvalidConfigException(
                'Unable to determine the entry script file path.');
        }
    }
}
```

### 命令行应用Request
命令行应用Request由 yii\console\Request 负责实现

```php
class Request extends \yii\base\Request
{
    // 属性 params，用于表示命令行参数
    private $_params;

    // params属性的getter函数
    // 通过 $_SERVER['argv'] 来获取命令行参数
    public function getParams()
    {
        if (!isset($this->_params)) {
            if (isset($_SERVER['argv'])) {
                $this->_params = $_SERVER['argv'];

                // 删除数组的第一个元素，这个元素是PHP脚本名。
                // 因此，属性params中全部是参数，不带脚本名
                array_shift($this->_params);
            } else {
                $this->_params = [];
            }
        }

        return $this->_params;
    }

    // params属性的setter函数
    public function setParams($params)
    {
        $this->_params = $params;
    }

    // 父类虚函数的实现
    public function resolve()
    {
        // 获取全部的命令行参数
        $rawParams = $this->getParams();

        // 第一个命令行参数作为路由
        if (isset($rawParams[0])) {
            $route = $rawParams[0];
            array_shift($rawParams);
        } else {
            $route = '';
        }

        $params = [];

        // 遍历剩余的全部命令行参数
        foreach ($rawParams as $param) {

            // 正则匹配每一个参数
            if (preg_match('/^--(\w+)(=(.*))?$/', $param, $matches)) {

                // 参数名
                $name = $matches[1];

                // yii\console\Application::OPTION_APPCONFIG = 'appconfig'
                if ($name !== Application::OPTION_APPCONFIG) {
                    $params[$name] = isset($matches[3]) ? $matches[3] : true;
                }

            // 无名参数，直接作为参数值
            } else {
                $params[] = $param;
            }
        }

        return [$route, $params];
    }
}
```
相比较于 yii\base\Request ， yii\console\Request 提供了一个 params 属性， 该属性以数组形式保存了入口脚本 Yii 的命令行参数。这是通过 $_SERVER['argv'] 获取的。 

注意 
>* params 属性不保存入口脚本名。入口脚本名由基类的 scriptName 属性保存。

同时， yii\console\Request 还实现了父类的 resolve() 虚函数， 这个函数主要做了这么几件事：

>* 将 params 属性的第一个元素作为路由。如果入口脚本未提供任何参数，也即 params 是个空数组， 那么将路由置为一个空字符串。
>* 遍历 params 中剩余的参数，使用正则匹配Yii应用的参数名和参数值，看看是不是 --参数名=参数值 形式。 其中，以 -- 打头的任意字母、数字、下划线的组合，就是参数名。 紧跟参数名的 = 后面的内容，则为参数值。 对于仅有参数名，没有参数值的，视参数值为 true 。
>* 如果正则匹配不成功，则将这个命令行参数作为Yii应用的一个无名参数的值。
>* 如果第二步中的参数名为 appconfig 则忽略该参数，Console Application会专门针对该参数进行处理。
>* 上面步骤中的参数和参数值，被保存进一个数组中。数组的键表示参数名，数组的值表示参数值。
>* 最终 resolve() 返回一个数组，第一个元素是一个表示路由的字符串，第二元素则是参数数组。 该方法由Application在处理Request时调用。

关于 appconfig 参数的问题，只要在调用 yii 时，指定了 appconfig 参数， 就表明不使用默认的参数配置文件，而使用该参数所指定的配置文件。相关的代码在 yii\console\Application 中:

```php
// 定义一个常量
const OPTION_APPCONFIG = 'appconfig';

// yii\console\Application类的构造函数
public function __construct($config = [])
{
    // 重点看这句，会调用loadConfig() 成员函数
    $config = $this->loadConfig($config);
    parent::__construct($config);
}

// 如果指定的配置文件存在，那么返回其配置数组
// 否则，返回构造函数调用时的数组
protected function loadConfig($config)
{
    if (!empty($_SERVER['argv'])) {

        // 设定了一个字符串 "--appconfig="
        $option = '--' . self::OPTION_APPCONFIG . '=';

        // 遍历所有命令行参数，看看能不能找到上面说的这个字符串
        foreach ($_SERVER['argv'] as $param) {
            if (strpos($param, $option) !== false) {

                // 截取参数值部分
                $path = substr($param, strlen($option));
                if (!empty($path) && is_file($file = Yii::getAlias($path))) {

                    // 将指定文件的内容引入进来
                    return require($file);
                } else {
                    die("The configuration file does not exist: $path\n");
                }
            }
        }
    }

    return $config;
}
```

## Web应用Request

Web应用Request由 yii\web\Request 实现，这个类的代码将近1400行，主要是一些功能的封装罢了， 原理上没有很复杂的东西。

只是涉及到许多HTTP的有关知识，读者朋友们可以自行查看相关的规范文档， 如 [HTTP 1.1 协议](https://tools.ietf.org/html/rfc2616) ， [CGI 1.1](https://tools.ietf.org/html/rfc3875.html) 规范 等。

### 请求的方法

根据 HTTP 1.1 协议 ，HTTP的请求可以有：GET, POST, PUT等8种方法 (Request Method)。除了用不到的 CONNECT 外，Yii支持全部的HTTP请求方法。


要获取当前用户请求的方法，可以使用 yii\web\Request::getMethod()
```php
// 返回当前请求的方法，请留意方法名称是大小写敏感的，按规范应转换为大写字母
public function getMethod()
{
    // $this->methodParam 默认值为 '_method'
    // 如果指定 $_POST['_method'] ，表示使用POST请求来模拟其他方法的请求。
    // 此时 $_POST['_method'] 即为所模拟的请求类型。
    if (isset($_POST[$this->methodParam])) {
        return strtoupper($_POST[$this->methodParam]);

    // 或者使用 $_SERVER['HTTP_X_HTTP_METHOD_OVERRIDE'] 的值作为方法名。
    } elseif (isset($_SERVER['HTTP_X_HTTP_METHOD_OVERRIDE'])) {
        return strtoupper($_SERVER['HTTP_X_HTTP_METHOD_OVERRIDE']);

    // 或者使用 $_SERVER['REQUEST_METHOD'] 作为方法名，未指定时，默认为 GET 方法
    } else {
        return isset($_SERVER['REQUEST_METHOD']) ?
            strtoupper($_SERVER['REQUEST_METHOD']) : 'GET';
    }
}
```
这个方法使用了3种方法来获取当前用户的请求，优先级从高到低依次为：

1. 当使用POST请求来模拟其他请求时，以 $_POST['_method'] 作为当前请求的方法；
2. 否则，如果存在 X_HTTP_METHOD_OVERRIDE HTTP头时，以该HTTP头所指定的方法作为请求方法， 如 X-HTTP-Method-Override: PUT 表示该请求所要执行的是 PUT 方法；
3. 如果 X_HTTP_METHOD_OVERRIDE 不存在，则以 REQUEST_METHOD 的值作为当前请求的方法。 如果连 REQUEST_METHOD 也不存在，则视该请求是一个 GET 请求。

> 前面两种方法，主要是针对一些只支持GET和POST等有限方法的User Agent而设计的。
==> 有哪些？

其中第一种方法是从Ruby on Rails中借鉴过来的， 通过在发送POST请求时，加入一个 $_POST['_method'] 的隐藏字段，来表示所要模拟的方法， 如PUT，DELETE等。这样，就可以使得这些功能有限的User Agent也可以正常与服务器交互。 这种方法胜在简便，随手就来。

第二种方法则是使用 X_HTTP_METHOD_OVERRIDE HTTP头的办法来指定所使用的请求类型。 这种方法胜在直接明了，约定俗成，更为规范、合理。

至于 `REQUEST_METHOD` 是 CGI 1.1 规范 所定义的环境变量， 专门用来表明当前请求方法的。上面的代码只是在未指定时默认为GET请求罢了。

Yii为我们封装了许多方法专门用于执行这些判断：

>* getIsAjax() 是否是AJAX请求，这其实不是HTTP请求方法，但是实际使用上，这个是用得最多的。
>* getIsDelete() 是否是DELETE请求
>* getIsFlash() 是否是Adobe Flash 或 Adobe Flex 发出的请求，这其实也不是HTTP请求方法。
>* getIsGet() 是否是一个GET请求
>* getIsHead() 是否是一个HEAD请求
>* getIsOptions() 是否是一个OPTIONS请求
>* getIsPatch() 是否是PATCH请求
>* getIsPjax() 是否是一个PJAX请求，这也并非是HTTP请求方法。
>* getIsPost() 是否是一个POST请求
>* getIsPut() 是否是一个PUT请求

除了这3个之外的其余7个方法，正好对应于HTTP 1.1 协议定义的7个方法。

> 而CONNECT方法由于Web开发在用不到，主要用于HTTP代理， 因此，Yii也就没有为其设计一个所谓的 isConnect() 了，这是无用功。

看看具体实现:
```php
// 这个SO EASY，啥也不说了，Yii实现的7个HTTP方法都是这个路子。
public function getIsOptions()
{
    // 注意在getMethod()时，输出的是全部大写的字符串
    return $this->getMethod() === 'OPTIONS';
}

// AJAX请求是通过 X_REQUESTED_WITH 消息头来判断的
public function getIsAjax()
{
    // 注意这里的XMLHttpRequest没有全部大写
    return isset($_SERVER['HTTP_X_REQUESTED_WITH']) &&
        $_SERVER['HTTP_X_REQUESTED_WITH'] === 'XMLHttpRequest';
}

// PJAX请求是AJAX请求的一种，增加了X_PJAX消息头的定义
public function getIsPjax()
{
    return $this->getIsAjax() && !empty($_SERVER['HTTP_X_PJAX']);
}

// HTTP_USER_AGENT消息头中包含 'Shockwave' 或 'Flash' 字眼的（不区分大小写），
// 就认为是FLASH请求
public function getIsFlash()
{
    return isset($_SERVER['HTTP_USER_AGENT'])
        && (stripos($_SERVER['HTTP_USER_AGENT'], 'Shockwave') !== false
        || stripos($_SERVER['HTTP_USER_AGENT'], 'Flash') !== false);
}
```

### 请求的参数
PHP有众所周知的 $_GET 和 $_POST 等。相应地，Yii提供了一系列的方法用于获取请求参数:

```php
// 用于获取GET参数，可以指定参数名和默认值
public function get($name = null, $defaultValue = null)
{
    if ($name === null) {
        return $this->getQueryParams();
    } else {
        return $this->getQueryParam($name, $defaultValue);
    }
}

// 用于获取所有的GET参数
// 所有的GET参数保存在 $_GET 或 $this->_queryParams 中。
public function getQueryParams()
{
    if ($this->_queryParams === null) {
        // 请留意这里并未使用 $this->_queryParams = $_GET 进行缓存。
        // 说明一旦指定了 $_queryParams 则 $_GET 会失效。
        return $_GET;
    }

    return $this->_queryParams;
}

// 根据参数名获取单一的GET参数，不存在时，返回指定的默认值
public function getQueryParam($name, $defaultValue = null)
{
    $params = $this->getQueryParams();
    return isset($params[$name]) ? $params[$name] : $defaultValue;
}

// 类以于get()，用于获取POST参数，也可以指定参数名和默认值
public function post($name = null, $defaultValue = null)
{
    if ($name === null) {
        return $this->getBodyParams();
    } else {
        return $this->getBodyParam($name, $defaultValue);
    }
}

// 根据参数名获取单一的POST参数，不存在时，返回指定的默认值
public function getBodyParam($name, $defaultValue = null)
{
    $params = $this->getBodyParams();
    return isset($params[$name]) ? $params[$name] : $defaultValue;
}

// 获取所有POST参数，所有POST参数保存在 $this->_bodyParams 中
public function getBodyParams()
{
    if ($this->_bodyParams === null) {
        // 如果是使用 POST 请求模拟其他请求的
        if (isset($_POST[$this->methodParam])) {
            $this->_bodyParams = $_POST;

            // 将 $_POST['_method'] 删掉，剩余的$_POST就是了
            unset($this->_bodyParams[$this->methodParam]);
            return $this->_bodyParams;
        }

        // 获取Content Type
        // 对于 'application/json; charset=UTF-8'，得到的是 'application/json'
        $contentType = $this->getContentType();
        if (($pos = strpos($contentType, ';')) !== false) {
            $contentType = substr($contentType, 0, $pos);
        }

        // 根据Content Type 选择相应的解析器对请求体进行解析
        if (isset($this->parsers[$contentType])) {

            // 创建解析器实例
            $parser = Yii::createObject($this->parsers[$contentType]);
            if (!($parser instanceof RequestParserInterface)) {
                throw new InvalidConfigException(
                "The '$contentType' request parser is invalid.
                It must implement the yii\\web\\RequestParserInterface.");
            }

            // 将请求体解析到 $this->_bodyParams
            $this->_bodyParams = $parser->parse($this->getRawBody(), $contentType);

        // 如果没有与Content Type对应的解析器，使用通用解析器
        } elseif (isset($this->parsers['*'])) {
            $parser = Yii::createObject($this->parsers['*']);
            if (!($parser instanceof RequestParserInterface)) {
                throw new InvalidConfigException(
                "The fallback request parser is invalid.
                It must implement the yii\\web\\RequestParserInterface.");
            }
            $this->_bodyParams = $parser->parse($this->getRawBody(),
                $contentType);

        // 连通用解析器也没有
        // 看看是不是POST请求，如果是，PHP已经将请求参数放到$_POST中了，直接用就OK了
        } elseif ($this->getMethod() === 'POST') {
            $this->_bodyParams = $_POST;

        // 以上情况都不是，那就使用PHP的 mb_parse_str() 进行解析
        } else {
            $this->_bodyParams = [];
            mb_parse_str($this->getRawBody(), $this->_bodyParams);
        }
    }

    return $this->_bodyParams;
}
```
在上面的代码中，将所有的请求参数划分为两类，
>* 一类是包含在URL中的，称为查询参数（Query Parameter），或GET参数。 
>* 另一类是包含在请求体中的，需要根据请求体的内容类型（Content Type）进行解析，称为POST参数。

#### 内容类型（Content-Type）
获取内容类型，使用 getContentType()
```php
public function getContentType()
{
    if (isset($_SERVER["CONTENT_TYPE"])) {
        return $_SERVER["CONTENT_TYPE"];
    } elseif (isset($_SERVER["HTTP_CONTENT_TYPE"])) {
        return $_SERVER["HTTP_CONTENT_TYPE"];
    }

    return null;
}
```

根据 CGI 1.1 规范 ， 内容类型由 CONTENT_TYPE 环境变量来表示。 而根据 HTTP 1.1 协议 ， 内容类型则是放在 CONTENT_TYPE 头部中，然后由PHP赋值给 $_SERVER['HTTP_CONTENT_TYPE'] 。 这里一般没有冲突，因此发现哪个用哪个，就怕客户端没有给出（这种情况返回 null ）。
==> CGI规范和HTTP 协议，应该看看了
==> 这其实都是 PHP  的网络部分

#### 请求解析器
在 getPostParams() 中，根据不同的Content Type 创建了相应的内容解析器对请求体进行解析。 yii\web\Request 使用成员变量 public $parsers 来保存一系列的解析器。 这个变量在配置时进行指定:
```php
'request' => [
    ... ...
    'parsers' => [
        'application/json' => 'yii\web\JsonParser',
    ],
]
```
$parsers 是一个数组，数组的键是Content Type，如 applicaion/json 之类。 而数组的值则是对应于特定Content Type 的解析器，如 yii\web\JsonParser 。 这也是Yii实现的唯一一个现成的Parser，其他Content-Type，需要开发者自己写了。
==> 有其他，第三方实现的吗？

而且，可以以 * 为键指定一个解析器。那么该解析器将在一个Content Type找不到任何匹配的解析器后被使用。
==> 这如果没有日志的话，真的坑很大啊。

#### 请求体

在 yii\web\Reqeust::getBodyParams() 和 yii\web\RequestParserInterface::parse() 中， 我们可以看到，需要将请求体传入 parse() 进行解析，且请求体由 yii\web\Request::getRawBody() 可得。

```php
public function getRawBody()
{
    if ($this->_rawBody === null) {
        $this->_rawBody = file_get_contents('php://input');
    }

    return $this->_rawBody;
}
```
这个方法使用了 php://input 来获取请求体，这个 php://input 有这么几个特点：

1. php://input 是个只读流，用于获取请求体。
2. php://input 是返回整个HTTP请求中，除去HTTP头部的全部原始内容，而不管是什么Content Type（或称为编码方式）。相比较之下，$_POST 只支持 application/x-www-form-urlencoded 和 multipart/form-data-encoded 两种Content Type。其中前一种就是简单的HTML表单以 method="post" 提交时的形式，后一种主要是用于上传文档。因此，对于诸如 application/json 等Content Type，这往往是在AJAX场景下使用，那么使用 $_POST 得到的是空的内容，这时就必须使用 php://input 。
==> $_POST 原来是这样的 $_POST啊
3. 相比较于 $HTTP_RAW_POST_DATA ， php://input 无需额外地在php.ini中 激活 always-populate-raw-post-data ，而且对于内存的压力也比较小。
==> 说明$HTTP_RAW_POST_DATA也是一种类似的方法
4. 当编码方式为 multipart/form-data-encoded 时， php://input 是无效的。这种情况一般为上传文档。 这种情况可以使用传统的 $_FILES 或者 yii\web\UploadedFile 。

### 请求的头部
yii\web\Request 使用一个成员变量 private $_headers 来存储请求头。 而这个 $_header 其实是一个 yii\web\HeaderCollection ，这是一个集合类的基本数据结构， 实现了SPL的 IteratorAggregate , ArrayAccess 和 Countable 等接口。 因此，这个集合可以进行迭代、像数组一样进行访问、可被用于 conut() 函数等。

获取请求的头部是由 yii\web\Request::getHeaders() 来实现的:
```php
public function getHeaders()
{
    if ($this->_headers === null) {

        // 实例化为一个HeaderCollection
        $this->_headers = new HeaderCollection;

        // 使用 getallheaders() 获取请求头部，以数组形式返回
        if (function_exists('getallheaders')) {
            $headers = getallheaders();

        // 使用 http_get_request_headers() 获取请求头部，以数组形式返回
        } elseif (function_exists('http_get_request_headers')) {
            $headers = http_get_request_headers();

        // 使用 $_SERVER 数组获取头部
        } else {
            foreach ($_SERVER as $name => $value) {

                // 针对所有 $_SERVER['HTTP_*'] 元素
                if (strncmp($name, 'HTTP_', 5) === 0) {
                    // 将 HTTP_HEADER_NAME 转换成 Header-Name 的形式
                    $name = str_replace(' ', '-',
                        ucwords(strtolower(str_replace('_', ' ',
                            substr($name, 5)))));
                    $this->_headers->add($name, $value);
                }
            }

            return $this->_headers;
        }

        // 将数组形式的请求头部变成集合的元素
        foreach ($headers as $name => $value) {
            $this->_headers->add($name, $value);
        }
    }

    return $this->_headers;
}
```
这里用3种方法来尝试获取请求的头部：

1. getallheaders() ，这个方法仅在将PHP作为Apache的一个模块运行时有效。
2. http_get_request_headers() ，要求PHP启用HTTP扩展。
==> PHP的HTTP扩展是指什么？
3. $_SERVER 数组的方法，需要遍历整个数组，并将所有以 HTTP_* 元素加入到集合中去。 并且，要将所有 HTTP_HEADER_NAME 转换成 Header-Name 的形式。
==> 就是根据不同的PHP环境，采用有效的方法来获取请求头部，如此而已。

### 请求的解析
我们前面就说过了，无论是命令行应用还是Web应用，他们的请求都要实现接口要求的 resolve() ， 以便明确这个用户请求的路由和参数。下面就是 yii\web\Request::resolve() 的代码:
```php
public function resolve()
{
    // 使用urlManager来解析请求
    $result = Yii::$app->getUrlManager()->parseRequest($this);
    if ($result !== false) {
        list ($route, $params) = $result;

        // 将解析出来的参数与 $_GET 参数进行合并
        $_GET = array_merge($_GET, $params);

        return [$route, $_GET];
    } else {
        throw new NotFoundHttpException(Yii::t('yii', 'Page not found.'));
    }
}
```
#### 获取路径信息
```php
// 这个方法其实是调用 resolvePathInfo() 来获取路径信息的
public function getPathInfo()
{
    if ($this->_pathInfo === null) {
        $this->_pathInfo = $this->resolvePathInfo();
    }

    return $this->_pathInfo;
}

// 这个才是重点
protected function resolvePathInfo()
{
    // 这个 getUrl() 调用的是 resolveRequestUri() 来获取当前的URL
    $pathInfo = $this->getUrl();

    // 去除URL中的查询参数部分，即 ? 及之后的内容
    if (($pos = strpos($pathInfo, '?')) !== false) {
        $pathInfo = substr($pathInfo, 0, $pos);
    }

    // 使用PHP urldecode() 进行解码，所有 %## 转成对应的字符， + 转成空格
    $pathInfo = urldecode($pathInfo);

    // 这个正则列举了各种编码方式，通过排除这些编码，来确认是 UTF-8 编码
    // 出处可参考 http://w3.org/International/questions/qa-forms-utf-8.html
    if (!preg_match('%^(?:
        [\x09\x0A\x0D\x20-\x7E]              # ASCII
        | [\xC2-\xDF][\x80-\xBF]             # non-overlong 2-byte
        | \xE0[\xA0-\xBF][\x80-\xBF]         # excluding overlongs
        | [\xE1-\xEC\xEE\xEF][\x80-\xBF]{2}  # straight 3-byte
        | \xED[\x80-\x9F][\x80-\xBF]         # excluding surrogates
        | \xF0[\x90-\xBF][\x80-\xBF]{2}      # planes 1-3
        | [\xF1-\xF3][\x80-\xBF]{3}          # planes 4-15
        | \xF4[\x80-\x8F][\x80-\xBF]{2}      # plane 16
        )*$%xs', $pathInfo)
    ) {
        $pathInfo = utf8_encode($pathInfo);
    }

    // 获取当前脚本的URL
    $scriptUrl = $this->getScriptUrl();

    // 获取Base URL
    $baseUrl = $this->getBaseUrl();

    if (strpos($pathInfo, $scriptUrl) === 0) {
        $pathInfo = substr($pathInfo, strlen($scriptUrl));
    } elseif ($baseUrl === '' || strpos($pathInfo, $baseUrl) === 0) {
        $pathInfo = substr($pathInfo, strlen($baseUrl));
    } elseif (isset($_SERVER['PHP_SELF']) && strpos($_SERVER['PHP_SELF'],
        $scriptUrl) === 0) {
        $pathInfo = substr($_SERVER['PHP_SELF'], strlen($scriptUrl));
    } else {
        throw new InvalidConfigException(
            'Unable to determine the path info of the current request.');
    }

    // 去除 $pathInfo 前的 '/'
    if ($pathInfo[0] === '/') {
        $pathInfo = substr($pathInfo, 1);
    }

    return (string) $pathInfo;
}
```
==> 这个正则很需要功力啊！

##### REQUEST_URI
由HTTP 1.1 协议定义，指访问某个页面的URI，去除开头的协议、主机、端口等信息。 如 http://www.digpage.com:8080/index.php/foo/bar?queryParams ， REQUEST_URI为 /index.php/foo/bar?queryParams 。

##### X-REWRITE-URL
当使用以开启了ISAPI_Rewrite 的IIS作为服务器时，ISAPI_Rewrite会在未对原始URI作任何修改前， 将原始的 REQUEST_URI 以 X-REWRITE-URL HTTP头保存起来。

##### PATH_INFO
CGI 1.1 规范所定义的环境变量。 从形式上看， http://www.digpage.com:8080/index.php</PATH_INFO>?queryParams 。 它是整个URI中，在脚本标识之后、查询参数 ? 之前的部分。 对于Apache，需要设置 AcceptPathInfo On ，且在一个URL没有 </PATH_INFO> 部分的时候， PATH_INFO 无效。特殊的情况，如 http://www.digpage.com/index.php/ ， PATH_INFO 为 / 。 而对于Nginx，则需要设置：
```
fastcgi_split_path_info ^(.+?\.php)(/.*)$;
fastcgi_param PATH_INFO $fastcgi_path_info;
```

##### ORIG_PATH_INFO
在PHP文档中对它的解释语焉不详，“指未经PHP处理过的原始的PATH_INFO”。 这个在Apache和Nginx需要配置一番才行，但一般用不到，已经有PATH_INFO可以用了嘛。而在IIS中则有点怪， 对于 http://www.digpage.com/index.php/ ORIG_PATH_INFO 为 /index.php/ ； 对于 http://www.digapge.com/index.php ORIG_PATH_INFO 为 /index.php 。

#### 入口脚本路径
yii\web\Request::getScriptUrl() 用于获取入口脚本的相对路径，也涉及到不同环境下PHP的不同表现。 我们还是先从代码入手

```php
// 这个方法用于获取当前入口脚本的相对路径
public function getScriptUrl()
{
    if ($this->_scriptUrl === null) {

        // $this->getScriptFile() 用的是 $_SERVER['SCRIPT_FILENAME']
        $scriptFile = $this->getScriptFile();
        $scriptName = basename($scriptFile);

        // 下面的这些判断分支代码，为各主流PHP framework所用，
        // Yii, Zend, Symfony等都是大同小异。
        if (basename($_SERVER['SCRIPT_NAME']) === $scriptName) {
            $this->_scriptUrl = $_SERVER['SCRIPT_NAME'];
        } elseif (basename($_SERVER['PHP_SELF']) === $scriptName) {
            $this->_scriptUrl = $_SERVER['PHP_SELF'];
        } elseif (isset($_SERVER['ORIG_SCRIPT_NAME']) &&
            basename($_SERVER['ORIG_SCRIPT_NAME']) === $scriptName) {
            $this->_scriptUrl = $_SERVER['ORIG_SCRIPT_NAME'];
        } elseif (($pos = strpos($_SERVER['PHP_SELF'], '/' . $scriptName))
            !== false) {
            $this->_scriptUrl = substr($_SERVER['SCRIPT_NAME'], 0, $pos)
                . '/' . $scriptName;
        } elseif (!empty($_SERVER['DOCUMENT_ROOT'])
            && strpos($scriptFile, $_SERVER['DOCUMENT_ROOT']) === 0) {
            $this->_scriptUrl = str_replace('\\', '/',
                str_replace($_SERVER['DOCUMENT_ROOT'], '', $scriptFile));
        } else {
            throw new InvalidConfigException(
                'Unable to determine the entry script URL.');
        }
    }

    return $this->_scriptUrl;
}
```
##### SCRIPT_FILENAME
当前脚本的实际物理路径，比如 /var/www/digpage.com/frontend/web/index.php ， 或WIN平台的 D:\www\digpage.com\frontend\web\index.php 。 以Nginx为例，一般情况下，SCRIPT_FILENAME有以下配置项：
```
fastcgi_split_path_info ^(.+?\.php)(/.*)$;
# 使用 document root 来得到物理路径
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name
```

##### SCRIPT_NAME
CGI 1.1 规范所定义的环境变量，用于标识CGI脚本（而非脚本的输出）， 如 http://www.digapge.com/path/index.php 中的 /path/index.php 。 仍以Nginx为例，SCRIPT_NAME一般情况下有 fastcgi_param SCRIPT_NAME $fastcgi_script_name 的设置。 绝大多数情况下，使用 SCRIPT_NAME 即可获取当前脚本。

##### PHP_SELF

PHP_SELF 是PHP自己实现的一个 $_SERVER 变量，是相对于文档根目录（document root）而言的。 对于 http://www.digpage.com/path/index.php?queryParams ，PHP_SELF 为 /path/index.php 。 一般SCRIPT_NAME 与 PHP_SELF 无异。但是，在 PHP.INI 中，如 cgi.fix_pathinfo=1 （默认即为1）时， 对于形如 http://www.digpage.com/path/index.php/post/view/123 ， 则PHP_SELF 为 /path/index.php/post/view/123 。 而根据 CGI 1.1 规范，SCRIPT_NAME 仅为 /path/index.php ，至于剩余的 /post/view/123 则为PATH_INFO。

##### ORIG_SCRIPT_NAME
当PHP以CGI模式运行时，默认会对一些环境变量进行调整。 首当其冲的，就是 SCRIPT_NAME 的内容会变成 php.cgi 等二进制文件，而不再是CGI脚本文件。 当然，设置 cgi.fix_pathinfo=0 可以关闭这一默认行为。但这导致的副作用比较大，影响范围过大，不宜使用。 但天无绝人之路，九死之地总留一线生机，那就是ORIG_SCRIPT_NAME，他保留了调整前 SCRIPT_NAME 的内容。 也就是说，在CGI模式下，可以使用 ORIG_SCRIPT_NAME 来获取想要的SCRIPT_NAME。 请留意使用 ORIG_SCRIPT_NAME 前一定要先确认它是否存在。

交待完这些背景知识后，我们再来看看 yii\web\Request::getScriptUrl() 的逻辑：

>* 先调用 yii\web\Request::getScriptFile() ， 通过 basename($_SERVER['SCRIPT_FILENAME']) 获取脚本文件名。一般都是我们的入口脚本 index.php 。

>* 绝大多数情况下， base($_SERVER('SCRIPT_NAME')) 是与第一步获取的 index.php 相同的。 如果这样的话，则认为这个 SCRIPT_NAME 就是我们所要的脚本URL。

这也是规范的定义。但是既然称为规范，说明并非是事实。 事实是由Web服务器来实现的，也就是说Web服务器可能进行修改。

另外，对于运行于CGI模式的PHP而言，使用 SCRIPT_NAME 也无法获得脚本名。

>* 那么我们转而向PHP_SELF求助，这个是PHP内部实现的，不受Web服务器的影响。一般这个PHP_SELF也是可堪一用的， 但也不是放之四海而皆准，对于带有PATH_INFO的URI， basename() 获取的并不是脚本名。

>* 于是我们再转向 ORIG_SCRIPT_NAME 求助，如果PHP是运行于CGI模式，那么就可行。

>* 再不成功，可能PHP并非运行于CGI模式（否则第4步就可以成功），且URI中带有PATH_INFO（否则第二步就可以成功）。 对于这种情形，PHP_SELF的前面一截就是我们要的脚本URL 。

>* 万一以上情况都不符合，说明当前PHP运行的环境诡异莫测。 那只能寄希望于将 SCRIPT_FILENAME 中前面那截可能是Document Root的部分去掉，余下的作为脚本URL了。 前提是要有Document Root，且SCRIPT_FILENAME前面的部分可以匹配上。


# 数据类型
各DBMS间，最明显、最常见的差异就在于所支持、实现的数据类型不同。
> Yii的一个重要任务，就是消 除这些区别，提供一个统一的开发界面供开发者使用。

## 抽象数据类型
Yii不但需要搞定各DBMS间数据类型的差异，还需要搞定PHP与DBMS间数据类 类型的差异。因此，Yii引入了一个逻辑层面的数据类型，来统一PHP与DBMS，以及各DBMS之间数据类型 的差异。

把这个逻辑层面的数据类型称为抽象类型，在 yii\db\Schema 中进行定义:
```php
abstract class Schema extends Object
{
    // 预定义16种抽象字段类型
    const TYPE_PK = 'pk';
    const TYPE_BIGPK = 'bigpk';
    const TYPE_STRING = 'string';
    const TYPE_TEXT = 'text';
    const TYPE_SMALLINT = 'smallint';
    const TYPE_INTEGER = 'integer';
    const TYPE_BIGINT = 'bigint';
    const TYPE_FLOAT = 'float';
    const TYPE_DECIMAL = 'decimal';
    const TYPE_DATETIME = 'datetime';
    const TYPE_TIMESTAMP = 'timestamp';
    const TYPE_TIME = 'time';
    const TYPE_DATE = 'date';
    const TYPE_BINARY = 'binary';
    const TYPE_BOOLEAN = 'boolean';
    const TYPE_MONEY = 'money';

    // ... ...
}
```

## 数据类型转换
