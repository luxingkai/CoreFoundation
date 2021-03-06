# CoreFoundation
基于C的面向OC的接口操作   
CoreFoundation框架的设计理念，编程接口便于使用和重用。    
· 确保在不同框架和代码库之间的代码以及数据共享    
· 使某种程度的操作系统独立性成为可能       
· 支持Unicode字符串的国际化      
· 提供公共API和其他有用的功能，包括插件体系结构、XML属性列表和首选项      

# Opaque type 优势    
对某些人来说，不透明类型可能会阻止直接访问结构的内容，从而造成不必要的限制。与不透明类型相关的开销可能会影响程序性能。但是不透明类型的好处超过了这些表面上的限制。   
不透明类型为底层功能的实现提供了更好的抽象和更大的灵活性。通过隐藏结构的字段等细节，Core Foundation减少了这些细节更改时客户端代码中可能发生错误的机会。此外，不透明类型允许进行优化，如果公开这些优化可能会造成混乱。例如，CFString“正式地”表示一个由UniChar类型的16位字符组成的数组。但是，CFString可以选择将ASCII范围内的字符存储为8位值。复制一个不可变的对象可能(通常也确实)会导致对该对象的共享引用，而不是在内存中创建两个独立的对象(参见Core Foundation的内存管理编程指南)。   
继续以CFString为例，使用不透明类型来存储字符似乎很重要。然而，事实证明，这种存储的CPU成本并不比使用简单的C字符数组高多少，而且内存成本通常更低。另外，不透明并不一定意味着不透明类型永远不能提供直接访问内容的机制。例如，CFString为此提供了CFStringGetCStringPtr函数。
最后，您可以在一定程度上自定义一些不透明类型。例如，集合类型允许您为在集合的每个成员上调用函数定义回调。    

# Object References       
诸如此类的声明是指向定义不透明类型的(私有)结构的不可变和可变版本的指针引用。许多核心基础函数的参数和返回值都采用这些对象引用的类型，并且从不对私有结构进行类型定义    
因为类型ID的值可以在不同的版本之间更改，所以您的代码不应该依赖于存储的或硬编码的类型ID，也不应该硬编码类型ID的任何观察到的属性(例如，它是一个小整数)。

# Polymorphic Functions   
Core Foundation提供了多种多态功能。这些函数可以接受任何Core Foundation对象作为参数，并且(在一个实例中，CFRetain)可以返回任何Core Foundation对象。这些参数和返回值的类型是CFTypeRef，这是一种通用的对象引用类型。CFType类似于面向对象语言中的根类，因为它的函数可以被所有其他对象重用。    

# Varieties of Objects    
根据对象的可编辑性和可扩展性的特点，不透明类型最多有三种基本变体或“风格”:    

不可变和固定大小    
可变和固定大小   
可变大小    
可变对象是可编辑的，这意味着它们的内容可以更改。不可变对象是不可编辑的;一旦它们被创建，它们就不能被改变。任何更改不可变对象的尝试通常都会导致某种错误。一个固定大小的对象有它可以增长到的最大限制;对于CFString，这将是字符的数量，而对于集合，限制将是元素的数量。    

# Naming Conventions    
Core Foundation中一个主要的编程接口约定是使用与符号密切相关的不透明类型的名称作为符号的前缀。对于函数，这个前缀不仅标识函数“所属”的类型，而且通常标识函数操作的目标对象的类型。(这个约定的一个例外是常量，它将“k”放在类型前缀之前。)以下是一些来自头文件的例子:   

# Other Types       
Core Foundation定义了一些数据类型，用于函数的一般使用。其中一些类型的目的是抽象原语值，这些原语值可能会随着处理器地址空间的变化而变化。例如，CFIndex类型用于索引、计数、长度和大小参数。CFOptionFlags类型用于位字段参数，而CFHashCode类型包含从CFHash函数返回的散列结果和某些散列回调。   

# Comparing Objects   
将两个核心基础对象与CFEqual函数进行比较。如果两个对象本质上相等，则函数返回一个布尔真值。“基本的”相等性取决于比较对象的类型。例如，当您比较两个CFString对象时，Core Foundation认为当它们逐个字符匹配时，本质上是相等的，而不考虑它们的编码或可变性属性。当两个CFArray对象具有相同的元素计数且一个数组中的每个元素对象与另一个数组中的对应元素对象本质上相等时，则认为它们是相等的。显然，比较对象必须具有相同的类型(或相同类型的可变或不可变变体)才能被认为是相等的。   

# Inspecting Objects    
Core Foundation对象的一个主要特征是它们基于不透明(或私有)类型;因此，很难直接检查对象的内部数据。但是，基本服务提供了两个可以检查核心基础对象的功能。这些函数返回对象和对象类型的描述。    

# Toll-Free Bridged Types   
在Core Foundation框架和Foundation框架中有许多数据类型可以互换使用。可以互换使用的数据类型也称为免费桥接数据类型。这意味着您可以使用相同的数据结构作为核心基础函数调用的参数，或者作为Objective-C消息调用的接收者。例如，NSLocale(请参阅NSLocale类参考)与它的核心基础对等物CFLocale(请参阅CFLocale参考)是可互换的。   
并非所有数据类型都是免费桥接的，尽管它们的名称可能表明它们是免费的。例如，NSRunLoop不是免费桥接到CFRunLoop, NSBundle不是免费桥接到CFBundle, NSDateFormatter不是免费桥接到CFDateFormatter。表1提供了支持免费桥接的数据类型列表。    






