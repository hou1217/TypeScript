# TypeScript手册

[TypeScript Handbook](http://www.typescriptlang.org/Handbook)

## 目录

* [基本类型](#基本类型)
  * [Boolean](#boolean)
  * [Number](#number)
  * [String](#string)
  * [Array](#array)
  * [Enum](#enum)
  * [Any](#any)
  * [Void](#void)
* [接口](#接口)
  * [第一个接口例子](#第一个接口例子)
  * [可选属性](#可选属性)
  * [函数类型](#函数类型)
  * [数组类型](#数组类型)
  * [类类型](#类类型)
  * [扩展接口](#扩展接口)
  * [混合类型](#混合类型)
* [类](#类)
  * [类](#Classes)
  * [继承](#继承)
  * [公共与私有的修饰语](#公共与私有修饰语)
  * [存取器](#存取器)
  * [静态属性](#静态属性)
  * [高级技巧](#高级技巧)
* [模块](#模块)
  * [多文件中的模块](#多文件中的模块)
  * [外部模块](#外部模块)
  * [Export =](#export-)
  * [别名](#别名)
  * [可选模块的加载与其它高级加载的场景](#可选模块的加载与其它高级加载的场景)
  * [使用其它JavaScript库](#使用其它JavaScript库)
  * [模块陷井](#模块陷井)

## 基本类型

为了写出有用的程序，我们需要有能力去处理简单的数据单位：数字，字符串，结构，布尔值等。在TypeScript里，包含了与JavaScript中几乎相同的数据类型，此外还有便于我们操作的枚举类型。

### Boolean

最基本的数据类型就是true/false值，在JavaScript和TypeScript里叫做布尔值。

```typescript
var isDone: boolean = false;
```

### Number

与JavaScript一样，所有的数字在TypeScript里都是浮点数。它们的类型是‘number’。

```typescript
var height: number = 6;
```

### String

像其它语言里一样，我们使用‘string’表示文本数据类型。与JavaScript里相同，可以使用双引号（"）或单引号（'）表示字符串。

```typescript
var name: string = "bob";
name = "smith";
```

### Array

TypeScript像JavaScript一样，允许你操作数组数据。可以用两种方式定义数组。第一种，可以在元素类型后面接‘[]’，表示由此此类元素构成的一个数组：

```typescript
var list:number[] = [1, 2 ,3];
```

第二种方式是使用数组泛型，Array<元素类型>：

```typescript
var list:Array<number> = [1, 2, 3];
```

### Enum

‘enum’类型是对标准JavaScript数据类型的一个补充。像其它语言，如C#，使用枚举类型可以为一组数值赋予友好的名字。

```typescript
enum Color {Red, Green, Blue};
var c: Color = Color.Green;
```

默认情况下，从0开始为元素编号。你也可以手动的指定成员的数值。例如，我们将上面的例子改成从1开始编号：

```typescript
enum Color {Red = 1, Green, Blue};
var c: Color = Color.Green;
```

或者，全部都采用手动赋值：

```typescript
enum Color {Red = 1, Green = 2, Blue = 4};
var c: Color = Color.Green;
```

枚举类型一个便利的特点是，你可以从枚举的值取出它的名字。例如，我们知道数值2，但是不确认它映射到Color里的哪个名字，我们可以查找相应的名字：

```typescript
enum Color {Red = 1, Green, Blue};
var colorName: string = Color[2];

alert(colorName);
```

### Any

有时，我们可能会为暂时还不清楚的变量指定类型。这些值可能来自于动态的内容，比如第三方程序库。这种情况下，我们不希望类型检查器对这些值进行检查或者说让它们直接通过编译阶段的检查。这时，我们可以使用‘any’类型来标记这些变量：

```typescript
var notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay，definitely a boolean
```

在对现有代码进行改写的时候，‘any’类型是十分有用的，它允许你在编译时可选择地包含或移除类型检查。

当你仅仅知道一部分数据的类型时，‘any’类型也是很有用的。比如，你有一个数组，它包含了不同的数据类型：

```typescript
var list:any[] = [1, true, "free"];

list[1] = 100;
```

### Void

某种程度上来说，‘void’类型与‘any’类型是相反的，表示没有任何类型。当一个函数不返回任何值是，你通常会见到其返回值类型是‘void’：

```typescript
function warnUser(): void {
    alert("This is my warning message");
}
```

## 接口

TypeScript的核心原则之一是对值所具有的‘shape’进行类型检查。它有时被称做鸭子类型或结构性子类型。在TypeScript里，接口负责为这样的类型命名，它可以为你的代码或第三方代码定义契约。

### 第一个接口例子

下面来看一个很简单的例子：

```typescript
function printLabel(labelledObj: {label: string}) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

类型检查器查看‘printLabel’的调用。‘printLabel’有一个参数，这个参数应该是一个对象，它拥有一个名字是‘label’并且类型为‘string’的属性。需要注意的是，我们传入的对象可能有很多个属性，但是编译器要求至少存在‘label’属性，并且其类型是‘string’：

```typescript
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

LabelledValue接口可以用来描述上面例子里的契约：应该具有一个类型是‘string’，名字是‘label’的属性。需要注意的是，在这里我们并不能像在其它语言里一样，说labelledObj实现了LabelledValue接口。我们关注的只是值的‘shape’。只要传入的对象满足上面提到的必须条件，那么就会通过检查。

### 可选属性

接口里的属性并不是全部是必须的。有些是在某些条件下存在，而有的则根本不需要。这在使用‘option bags’模式时很有用，即给函数传入的对象中仅存在一部分属性。

下面是应用了‘option bags’的例子：

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```

带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个‘?’符号。

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获使用了不存在的属性时的错误。比如，我们故意错误的拼写传入‘createSquare’的属性名collor，我们会得到一个错误提示。

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.collor;  // Type-checker can catch the mistyped name here
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```

### 函数类型

接口可以描述大部分JavaScript中的对象类型。除了描述带有属性的普通对象外，接口也可以表示函数类型。

我们可以给接口定义一个*调用签名*来描述函数类型。它就像是一个只有参数列表和返回值类型的函数定义。

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

这样定义后，我们可以像使用其它接口一样使用这个函数接口。下例展示了如何创建一个函数类型的变量，并赋予一个同类型的函数值。

```typescript
var mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  var result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。比如，我们也用下面的代码重写上面的例子：

```typescript
var mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  var result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

函数的参数是参照其位置依次的检查。函数的返回值类型是通过其返回值推断出来的（此例是false和true）。如果让这个函数返回数字或字符串，类型检查器会警告我们函数的返回值类型与SearchFunc接口中的定义不匹配。

### 数组类型

与使用接口描述函数类型差不多，我们也可以描述数组类型。数组类型具有一个‘index’类型表示索引的类型与相应的返回值类型。

```typescript
interface StringArray {
  [index: number]: string;
}

var myArray: StringArray;
myArray = ["Bob", "Fred"];
```

共有两种支持的索引类型：字符串和数字。你可以为一个数组同时指定这两种索引类型，但是有一个限制，数字索引返回值的类型必须是字符串索引返回值的类型的子类型。

索引签名可以很好的描述数组和‘字典’模式，它们也强制所有属性都与索引返回值类型相匹配。下面的例子里，length属性不匹配索引返回值类型，所以类型检查器给出一个错误提示：

```typescript
interface Dictionary {
  [index: string]: string;
  length: number;    // error, the type of 'length' is not a subtype of the indexer
}
```

### 类类型

#### 实现接口

与在C#或Java里接口的基本作用一样，在TypeScript里它可以明确的强制一个类去符合某种契约。

```typescript
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

你也可以在接口中描述一个方法，在类里实现它，如同下面的‘setTime’方法一样：

```typescript
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

接口描述了类的公共部分，而不是公共和私有两部分。它不会帮你检查类是否具有某些私有成员。

#### 静态成员与类实例的差别

当你操作类和接口的时候，你要知道类是具有两个类型的：静态部分的类型和实例的类型。你会注意到，当你用构造器签名去定义一个接口并试图定义一个类去实现这个接口时会得到一个错误：

```typescript
interface ClockInterface {
    new (hour: number, minute: number);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

这里因为当一个类实现了一个接口时，只对其实例部分进行类型检查。‘constructor’存在于类的静态部分，所以不在检查的范围内。

取而代之，我们应该直接操作类的静态部分。看下面的例子：

```typescript
interface ClockStatic {
    new (hour: number, minute: number);
}

class Clock {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

var cs: ClockStatic = Clock;
var newClock = new cs(7, 30);
```

### 扩展接口

和类一样，接口也可以相互扩展。扩展接口时会将其它接口里的属性拷贝到这个接口里，因此允许你把接口拆分成单独的可重用的组件。

```typescript
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

一个接口可以继承多个接口，创建出多个接口的组合接口。

```typescript
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

### 混合类型

先前我们提过，接口可以描述在JavaScript里存在的丰富的类型。因为JavaScript其动态灵活的特点，有时你会希望一个对象可以同时具有上面提到的多种类型。

一个例子就是，一个对象可以同时做为函数和对象使用，并带有额外的属性。

```typescript
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

var c: Counter;
c(10);
c.reset();
c.interval = 5.0;
```

使用第三方库的时候，你可能会上面那样去详细的定义类型。

## 类

传统的JavaScript程序使用函数和基于原型的继承来创建可重用的组件，但这对于熟悉使用面向对象方式的程序员来说有些棘手，因为他们用的是基于类的继承并且对象是从类构建出来的。从JavaScript的下个版本ECMAScript6开始，JavaScript程序将可以使用这种基于类的面向对象方法。在TypeScript里，我们允许开发者现在就使用这些特性，并且编译后的JavaScript可以在所有主流浏览器和平台上运行，而不需要等到下个JavaScript版本。

### Classes

下面看一个类的例子：

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter = new Greeter("world");
```

如果你使用过C#或Java，你会对这种语法非常熟悉。我们声明一个‘Greeter’类。这个类有3个成员：一个叫做‘greeting’的属性，一个构造函数和一个‘greet’方法.

你会注意到，我们在引用任何一个类成员的时候都用了‘this’，它表示我们访问的是类的成员。

最后一行，我们使用‘new’构造了Greeter类的一个实例。它会调用之前定义的构造函数，创建一个Greeter类型的新对象，并执行构造函数初始化它。

### 继承

在TypeScript里，我们可以使用常用的面向对象模式。当然，基于类的程序设计中最基本的模式是允许使用继承来扩展一个类。

看下面的例子：

```typescript
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(meters: number = 0) {
        alert(this.name + " moved " + meters + "m.");
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(meters = 5) {
        alert("Slithering...");
        super.move(meters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(meters = 45) {
        alert("Galloping...");
        super.move(meters);
    }
}

var sam = new Snake("Sammy the Python");
var tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

这个例子展示了TypeScript中继承的一些特征，与其它语言类似。我们使用‘extends’来创建子类。你可以看到‘Horse’和‘Snake’类是基类‘Animal’的子类，并且可以访问其属性和方法。

这个例子也说明了，在子类里可以重写父类的方法。‘Snake’类和‘Horse’类都创建了‘move’方法，重写了从‘Animal’继承来的‘move’方法，使得‘move’方法根据不同的类而具有不同的功能。

### 公共与私有的修饰语

#### 默认为public

你会注意到，上面的例子中我们没有用‘public’去修饰任何类成员的可见性。在C#里需要使用‘public’明确地指定成员的可见性。在TypeScript里，所有成员都默认为public。

你也可以把成员标记为‘private’来控制什么是可以在类外部访问的。我们重写一下上面的‘Animal’类：

```typescript
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
    move(meters: number) {
        alert(this.name + " moved " + meters + "m.");
    }
}
```

#### 理解private

TypeScript使用的是结构性类型系统。当我们比较两种不同的类型时，并不在乎它们从哪儿来的，如果它们中每个成员的类型都是兼容的，我们就说这两个类型是兼容的。

当我们比较具有‘private’成员的类型的时候，会稍有不同。如果一个类型中有个私有成员，那么只有另外一个类型中也存在这样一个私有成员并且它们必须来自同一处定义，我们才认为这两个类型是兼容的。

下面来看一个例子，详细的解释了这点：

```typescript
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
  constructor() { super("Rhino"); }
}

class Employee {
    private name:string;
    constructor(theName: string) { this.name = theName; }
}

var animal = new Animal("Goat");
var rhino = new Rhino();
var employee = new Employee("Bob");

animal = rhino;
animal = employee; //error: Animal and Employee are not compatible
```

这个例子中有‘Animal’和‘Rhino’两个类，‘Rhino’是‘Animal’类的子类。还有一个‘Employee’类，其类型看上去与‘Animal’是相同的。我们创建了几个这些类的实例，并相互赋值来看看会发生什么。因为‘Animal’和‘Rhino’共享了来自‘Animal’里的私有成员定义‘private name: string’，因此它们是兼容的。然而‘Employee’却不是这样。当把‘Employee’赋值给‘Animal’的时候，得到一个错误，说它们的类型不兼容。尽管‘Employee’里也有一个私有成员‘name’，但它明显不是‘Animal’里面定义的那个。

#### 参数属性

‘public’和‘private’关键字也提供了一种简便的方法通过参数属性来创建和初始化类成员。参数属性让你一步就可以创建并初始化一个类成员。下面的代码改写了上面的例子。注意，我们删除了‘name’并直接使用‘private name: string’参数在构造函数里创建并初始化‘name’成员。

```typescript
class Animal {
    constructor(private name: string) { }
    move(meters: number) {
        alert(this.name + " moved " + meters + "m.");
    }
}
```

### 存取器

TypeScript支持getters/setters来截取对对象成员的访问。它能帮助你有效的控制对对象成员的访问。

下面来看如何把一类改写成使用‘get’和‘set’。首先是没用使用存取器的例子：

```typescript
class Employee {
    fullName: string;
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

我们可以随意的设置fullName，这是非常方便的，但是这也可能会带来麻烦。

下面这个版本里，我们先检查用户密码是否正确，然后再允许其修改employee。我们把对fullName的直接访问改成了可以检查密码的‘set’方法。我们也加了一个get方法，让上面的例子仍然可以工作。

```typescript
var passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }
  
    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            alert("Error: Unauthorized update of employee!");
        }
    }
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

我们可以修改一下密码，来验证一下存取器是否是工作的。当密码不对时，会提示我们没有权限去修改employee。

注意：若要使用存取器，要求设置编译器输出目标为ECMAScript 5。

### 静态属性

到目前为止，我们只讨论了类的实例成员，那些仅当类被实例化的时候才会被初始化的属性。我们也可以创建类的静态成员，这些属性存在于类本身上面而不是类的实例上。在这个例子里，我们使用‘static’定义‘origin’，因为它是所有网格都会用到的属性。每个实例想要访问这个属性的时候，都要在origin前面加上类名。如同在实例属性上使用‘this.’前缀来访问属性一样，这里我们使用‘Grid.’来访问静态属性。

```typescript
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        var xDist = (point.x - Grid.origin.x);
        var yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

var grid1 = new Grid(1.0);  // 1x scale
var grid2 = new Grid(5.0);  // 5x scale

alert(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
alert(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

### 高级技巧

#### 构造函数

当你在TypeScript里定义类的时候，实际上同时定义了很多东西。首先是类的实例的类型。

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter: Greeter;
greeter = new Greeter("world");
alert(greeter.greet());
```

在这里，我们写了‘var greeter: Greeter’，意思是Greeter类实例的类型是Greeter。这对于用过其它面向对象语言的程序员来讲已经是老习惯了。

我们也创建了一个叫做*构造函数*的值。这个函数会在我们使用‘new’创建类实例的时候被调用。下面我们来看看，上面的代码被编译成JavaScript后是什么样子的：

```javascript
var Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();

var greeter;
greeter = new Greeter("world");
alert(greeter.greet());
```

上面的代码里，‘var Greeter’将被赋值为构造函数。当我们使用‘new’并执行这个函数后，便会得到一个类的实例。这个构造函数也包含了类的所有静态属性。换个角度说，我们可以认为类具有实例部分与静态部分这两个部分。

让我们来改写一下这个例子，看看它们之前的区别：

```typescript
class Greeter {
    static standardGreeting = "Hello, there";
    greeting: string;
    greet() {
        if (this.greeting) {
            return "Hello, " + this.greeting;
        }
        else {
            return Greeter.standardGreeting;
        }
    }
}

var greeter1: Greeter;
greeter1 = new Greeter();
alert(greeter1.greet());

var greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there!";
var greeter2:Greeter = new greeterMaker();
alert(greeter2.greet());
```

这个例子里，‘greeter1’与之前看到的一样。

再之后，我们直接使用类。我们创建了一个叫做‘greeterMaker’的变量。这个变量保存了这个类或者说保存了类构造函数。然后我们使用‘typeof Greeter’，意思是取Greeter类的类型，而不是实例的类型。或者理确切的说，"告诉我Greeter标识符的类型"，也就是构造函数的类型。这个类型包含了类的所有静态成员和构造函数。之后，就和前面一样，我们在‘greeterMaker’上使用‘new’，创建‘Greeter’的实例。

#### 把类当做接口使用

如上一节里所讲的，类定义会创建两个东西：类实例的类型和一个构造函数。因为类可以创建出类型，所以你能够在可以使用接口的地方使用类。

```typescript
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

var point3d: Point3d = {x: 1, y: 2, z: 3};
```

## 模块

这节会列出多种在TypeScript里组织代码的方法。我们将介绍内部和外部模块，以及如何在适合的地方使用它们。我们也会涉及到一些高级主题，如怎么使用外部模块，当使用TypeScript模块时如何避免常见的陷井。

**第一步**

让我们先写一段程序，我们将在整个小节中都使用这个例子。我们定义几个简单的字符串验证器，好比你会使用它们来验证表单里的用户输入或验证外部数据。

*所有的验证器都在一个文件里*

```typescript
interface StringValidator {
    isAcceptable(s: string): boolean;
}

var lettersRegexp = /^[A-Za-z]+$/;
var numberRegexp = /^[0-9]+$/;

class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}

class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: StringValidator; } = {};
validators['ZIP code'] = new ZipCodeValidator();
validators['Letters only'] = new LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

**增加模块支持**

随着我们增加更多的验证器，我们想要将它们组织在一起来保持对它们的追踪记录并且不用担心与其它对象产生命名冲突。我们把验证器包裹到一个模块里，而不是把它们放在全局命名空间下。

这个例子里，我们把所有验证器相关的类型都放到一个叫做*Validation*的模块里。由于我们想在模块外这些接口和类都是可见的，我们需要使用*export*。相反地，变量*lettersRegexp*和*numberRegexp*是具体实现，所以没有使用export，因此它们在模块外部是不可见的。在测试代码的底部，使用模块导出内容的时候需要增加一些限制，比如*Validation.LettersOnlyValidator*。

*模块化的验证器*

```typescript
module Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    var lettersRegexp = /^[A-Za-z]+$/;
    var numberRegexp = /^[0-9]+$/;

    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }

    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

### 多文件中的模块

当应用变得越来越大时，我们需要将代码分散到不同的文件中以便于维护。

现在，我们把Validation模块分散多个文件中。尽管是分开的文件，它们仍可以操作同一个模块，并且使用的时候就好像它们是定义在一个文件中一样。因为在不同文件之间存在依赖关系，我们加入了引用标签来告诉编译器文件之间的关系。我们的测试代码保持不变。

**多文件内部模块**

*Validation.ts*

```typescript
module Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
```

*LettersOnlyValidator.ts*

```typescript
/// <reference path="Validation.ts" />
module Validation {
    var lettersRegexp = /^[A-Za-z]+$/;
    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }
}
```

*ZipCodeValidator.ts*

```typescript
/// <reference path="Validation.ts" />
module Validation {
    var numberRegexp = /^[0-9]+$/;
    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}
```

*Test.ts*

```typescript
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

涉及到多文件时，我们必须确保所有编译后的代码都加载了。我们有两种方式。

第一种方式，把所有的输入文件编译为一个输出文件，需要使用 *--out* 标记：

```sh
tsc --out sample.js Test.ts
```

编译器会根据源码里的引用标签自动地对输出进行排序。你也可以单独地指定每个文件。

```sh
tsc --out sample.js Validation.ts LettersOnlyValidator.ts ZipCodeValidator.ts Test.ts
```

第二种方式，我们可以编译每一个文件，之后在页面上通过`<script>`标签把所有生成的js文件按正确的顺序都引进来。比如：

*MyTestPage.html (excerpt)*

```html
<script src="Validation.js" type="text/javascript" />
<script src="LettersOnlyValidator.js" type="text/javascript" />
<script src="ZipCodeValidator.js" type="text/javascript" />
<script src="Test.js" type="text/javascript" />
```

### 外部模块

TypeScript中同样存在外部模块的概念。外部模块在两种情况下会用到：node.js和require.js。对于没有使用node.js和require.js的应用来说是不需要使用外部模块的，最好使用上面说的内部模块的方式来组织代码。

在外部模块，不同文件之间的关系是通过imports和exports来指定的。在TypeScript里，任何具有顶级*import*和*export*的文件都会被视为段级模块。

下面，我们把之前的例子改写成外部模块。注意，我们不再使用module关键字 - 文件本身会被视为一个模块并以文件名来区分。

引用标签用*import*来代替，指明了模块之前的依赖关系。*import*语句有两部分：当前文件使用模块时使用的名字，require关键字指定了依赖模块的路径：

```typescript
import someMod = require('someModule');
```

我们通过顶级的*export*关键字指出了哪些对象在模块外是可见的，如同使用*export*定义内部模块的公共接口一样。

为了编译，我们必须在命令行上指明生成模块的目标类型。对于node.js，使用*--module commonjs*。对于require.js，使用*--module amd*。比如：

```sh
ts --module commonjs Test.ts
```

编译的时候，每个外部模块会变成一个单独的文件。如同引用标签，编译器会按照*import*语句编译相应的文件。

*Validation.ts*

```typescript
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

*LettersOnlyValidator.ts*

```typescript
import validation = require('./Validation');
var lettersRegexp = /^[A-Za-z]+$/;
export class LettersOnlyValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
```

*ZipCodeValidator.ts*

```typescript
import validation = require('./Validation');
var numberRegexp = /^[0-9]+$/;
export class ZipCodeValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
```

*Test.ts*

```typescript
import validation = require('./Validation');
import zip = require('./ZipCodeValidator');
import letters = require('./LettersOnlyValidator');

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: validation.StringValidator; } = {};
validators['ZIP code'] = new zip.ZipCodeValidator();
validators['Letters only'] = new letters.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

**生成外部模块的代码**

根据编译时指定的目标模块类型，编译器会生成相应的代码。想要了解更多关于*define*和*require*函数的使用方法，请阅读相应模块加载器的说明文档。

这个例子展示了在导入导出阶段使用的名字是怎么转换成模块加载代码的。

*SimpleModule.ts*

```typescript
import m = require('mod');
export var t = m.something + 1;
```

*AMD/RequireJS SimpleModule.js*

```javascript
define(["require"，"exports"，"mod"]，function(require, exports, m) {
    exports.t = m.something + 1;
});
```

*CommonJS / Node SimpleModule.js*

```javascript
var m = require('mod');
exports.t = m.something + 1;
```

### Export =

在上面的例子中，使用验证器的时候，每个模块只导出一个值。像这种情况，在验证器对象前面再加上限定名就显得累赘了，最好是直接使用一个标识符。

‘export =’语法指定了模块导出的单个对象。它可以是类，接口，模块，函数或枚举类型。当import的时候，直接使用模块导出的标识符，不再需要其它限定名。

下面，我们简化验证器的实现，使用‘export =’语法使每个模块导出单一对象。这会简化对模块的使用 - 我们可以用‘zipValidator’代替‘zip.ZipCodeValidator’。

*Validation.ts*

```typescript
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

*LettersOnlyValidator.ts*

```typescript
import validation = require('./Validation');
var lettersRegexp = /^[A-Za-z]+$/;
class LettersOnlyValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
export = LettersOnlyValidator;
```

*ZipCodeValidator.ts*

```typescript
import validation = require('./Validation');
var numberRegexp = /^[0-9]+$/;
class ZipCodeValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
```

*Test.ts*

```typescript
import validation = require('./Validation');
import zipValidator = require('./ZipCodeValidator');
import lettersValidator = require('./LettersOnlyValidator');

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: validation.StringValidator; } = {};
validators['ZIP code'] = new zipValidator();
validators['Letters only'] = new lettersValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

### 别名

另一种简化模块操作的方法是使用*import q = x.y.z*给常用的模块起一个短的名字。不要与*import x = require('name')*用来加载外部模块的语法弄混了，这里的语法是为指定的符号创建一个别名。你可以用这种方法为任意标识符创建别名，也包括导入的外部模块中的对象。

*创建别名基本方法*

```typescript
module Shapes {
    export module Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
var sq = new polygons.Square(); // Same as 'new Shapes.Polygons.Square()'
```

注意，我们并没有使用*require*关键字，而是直接使用导入符号的限定名赋值。这与使用*var*相似，但它还适用于类型和导入的具有命名空间含义的符号。重要的是，对于值来讲，*import*会产生与原始符号不同的引用，所以改变别名的值并不会影响原始变量的值。

### 可选模块的加载与其它高级加载的场景

有些时候，你只想在某种条件下才去加载一个模块。在TypeScript里，我们可以使用下面的方式来实现它以及其它高级加载的场景，直接调用模块加载器而不必担心类型安全问题。

编译器能探测出一个模块是否在生成的JavaScript里被使用到了。对于那些只做为类型系统部分使用的模块来讲，不会生成对应require代码。挑出未使用的引用有益于性能优化，同时也允许可选择性的加载模块。

这种模式的核心是*import id = require('...')*让我们可以访问外部模块导出的类型。模块加载是动态调用的，像下面if语句展示的那样。它利用了挑出对未使用引用的优化，模块只在需要的时候才去加载。为了让这种方法可行，通过import定义的符号只能在表示类型的位置使用（也就是说那段代码永远不会被编译生成JavaScript）。

为了确保使用正确，我们可以使用*typeof*关键字。在要求是类型的位置使用*typeof*关键字时，会得到类型值，在这个例子里得到的是外部模块的类型。

*Dynamic Module Loading in node.js*

```typescript
declare var require;
import Zip = require('./ZipCodeValidator');
if (needZipValidation) {
    var x: typeof Zip = require('./ZipCodeValidator');
    if (x.isAcceptable('.....')) { /* ... */ }
}
```

*Sample: Dynamic Module Loading in require.js*

```typescript
declare var require;
import Zip = require('./ZipCodeValidator');
if (needZipValidation) {
    require(['./ZipCodeValidator'], (x: typeof Zip) => {
        if (x.isAcceptable('...')) { /* ... */ }
    });
}
```

### 使用其它JavaScript库

为了描述不是用TypeScript写的程序库的类型，我们需要对程序库暴露的API进行声明。由于大部分程序库只提供少数的顶级对象，因此用模块来表示它们是一个好办法。我们叫它声明不是对执行环境的定义。通常会在‘.d.ts’里写这些定义。如果你熟悉C/C++，你可以把它们当做.h文件或‘extern’。让我们看一些内部和外部的例子。

#### 内部环境模块

流行的程序库D3在全局对象‘D3’里定义它的功能。因为这个库通过一个*script*标签加载（不是通过模块加载器），它的声明文件使用内部模块来定义它的类型。为了让TypeScript编译器识别它的类型，我们使用内部环境模块声明。比如：

*D3.d.ts (simplified excerpt)*

```typescript
declare module D3 {
    export interface Selectors {
        select: {
            (selector: string): Selection;
            (element: EventTarget): Selection;
        };
    }

    export interface Event {
        x: number;
        y: number;
    }

    export interface Base extends Selectors {
        event: Event;
    }
}

declare var d3: D3.Base;
```

#### 外部环境模块

在node.js里，大多数的任务可以通过加载一个或多个模块来完成。我们可以使用顶级export声明来为每个模块定义各自的‘.d.ts’文件，但全部放在一个大的文件中会更方便。为此，我们把模块名用引号括起来，方便之后的import。例如：

*node.d.ts (siplified excerpt)*

```typescript
declare module "url" {
    export interface Url {
        protocol?: string;
        hostname?: string;
        pathname?: string;
    }

    export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}

declare module "path" {
    export function normalize(p: string): string;
    export function join(...paths: any[]): string;
    export var sep: string;
}
```

现在我们可以*///<reference path="node.d.ts"/>*, 然后使用*import url = require('url');*加载这个模块。

```typescript
///<reference path="node.d.ts"/>
import url = require("url");
var myUrl = url.parse("http://www.typescriptlang.org");
```

### 模块陷井

这一节，将会介绍使用内部和外部模块时常见的陷井和怎么去避免它。

#### /// <reference> to an external module

一个常见的错误是使用`/// <reference>`引用外部模块文件，应该使用import。要理解这之间的不同，我们首先应该弄清编译器是怎么找到外部模块的类型信息的。

首先，根据*import x = require(...);*声明查找*.ts*文件。这个文件应该是使用了顶级import或export声明的执行文件。

其次，与前一步相似，去查找*.d.ts*文件，不同的是它不是执行文件而是声明文件（同样具有顶级的import或export声明）。

最后是尝试寻找外部模块声明，我们会通过用引号括起来的模块名来声明一个模块。

*myModules.d.ts*

```typescript
// In a .d.ts file or .ts file that is not an external module:
declare module "SomeModule" {
    export function fn(): string;
}
```

*myOtherModule.ts*

```typescript
/// <reference path="myModules.d.ts" />
import m = require("SomeModule");
```

这里的引用标签指定了外部环境模块的位置。这就是一些Typescript例子中引用node.d.ts的方法。

#### 不必要的命名空间

如果你想把内部模块转换为外部模块，它可能会像下面这个文件一件：

*shapes.ts*

```typescript
export module Shapes {
    export class Triangle { /* ... */ }
    export class Square { /* ... */ }
}
```

顶层的模块*Shapes*包裹了*Triangle*和*Square*。这对于使用它的人来说是让人迷惑和讨厌的：

*shapeConsumer.ts*

```typescript
import shapes = require('./shapes');
var t = new shapes.Shapes.Triangle(); // shapes.Shapes?
```

TypeScript里外部模块的一个特点是不同的外部模块永远也不会在相同的作用域内使用相同的名字。因为使用外部模块的人会为它们命名，所以完全没有必要把导出的符号包裹在一个命名空间里。

再次重申，不应该对外部模块使用命名空间，使用命名空间是为了提供逻辑分组和避免命名冲突。外部模块文件本身已经是一个逻辑分组，并且它的名字是由导入这个模块的代码指定，所以没有必要为导出的对象增加额外的模块层。

改进的例子：

*shapes.ts*

```typescript
export class Triangle { /* ... */ }
export class Square { /* ... */ }
```

*shapeConsumer.ts*

```typescript
import shapes = require('./shapes');
var t = new shapes.Triangle(); 
```

#### 外部模块的取舍

就像每个JS文件对应一个模块一样，TypeScript里外部模块文件与生成的JS文件也是一一对应的。这会产生一个效果，就是无法使用*--out*来让编译器合并多个外部模块文件为一个JavaScript文件。