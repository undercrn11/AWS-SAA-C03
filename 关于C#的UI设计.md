# 关于C#的UI设计

System.ComponentModel 命名空间提供用于实现组件和控件的运行时和设计时行为的类。 此命名空间包括用于特性和类型转换器的实现、数据源绑定和组件授权的基类和接口。

此命名空间中的类将划分为以下类别：

- 核心组件类。 Component， IComponent， Container，和IContainer类。
- 组件授权。 License， LicenseManager， LicenseProvider，和LicenseProviderAttribute类。
- 特性。 Attribute 类。
- 说明符和持久性。 TypeDescriptor， EventDescriptor，和PropertyDescriptor类。
- 类型转换器。 TypeConverter 类。



C#资源类型
托管资源： 托管资源是由.NET Common Language Runtime (CLR) 管理的资源。CLR负责自动进行内存分配和释放。例如，托管资源可能包括.NET对象、集合、字符串等。开发人员不需要手动管理这些资源的分配和释放。

非托管资源： 非托管资源是由CLR之外的代码或系统管理的资源。这包括文件句柄、数据库连接、网络连接、原生C/C++分配的内存等。由于CLR不负责非托管资源的管理，开发人员需要手动分配和释放这些资源，以避免资源泄漏。
————————————————