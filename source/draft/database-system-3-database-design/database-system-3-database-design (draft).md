# Database Design（draft）

本篇将介绍数据库设计相关概念。主要分为两个部分：数据库概念设计和关系型数据库设计。它们分别对应的设计方法是 E-R 模型（Entity-Relationship Model）和标准化（Normalization）。



## 数据库设计和 E-R 模型



### 数据库设计过程

**设计过程**

- 需求分析阶段。与用户和领域专家交流，收集需求，充分地描述出预期的数据库用户需要的数据。这个阶段产出的是**用户需求的规范**。
- 概念设计阶段。选择一个数据模型，将用户需求转换为数据库概念模式。这个阶段主要是专注于描述数据和数据之间的关系。用专业的概念的数据模型去描述数据结构，主要使用 entity-relationship model 和 normalization 方式去设计。这个阶段一般产出的是**实体关系图**（E-R图）。完整的概念设计 schema 还包含**功能需求规范**。用户对数据的操作类型，如修改，查询和取出具体的数据，删除数据。
- 逻辑设计阶段。将抽象的数据模型转换为数据库的实现。将概念模型转换为数据库系统使用的模型，如将 entity-relationship model 转换为 relational model。
- 物理设计阶段。设计物理存储的细节。包括文件组织的形式和选择索引结构。

物理的 schema 相对来说是比较容易改变的，而改变逻辑的 schema 是很难的，可能会影响到大量分散在应用程序中的查询和更新代码。因此，在构建应用程序之前，必须谨慎地和考虑周全地进行数据库设计。

**设计中常见的问题**

- **冗余**（Redundancy）。冗余的信息表示可能导致重复的信息出现不一致。如一些过时的或错误的信息依然存在数据库中。信息应该准确的出现在一个地方。
- **不完全**（Incompleteness）。一个坏的设计可能会使企业的某些方面难以建模或无法建模。不合理的分解关系的属性，导致难以插入一个新增的数据，或者必须其它属性设为 null。如没有单独的课程表，只有课程设置表，当新增课程时，无法插入课程属性到课程设置表。



### E-R 模型

E-R 模型可以将现实世界中企业的含义和相互作用映射到一个概念的模式。E-R 数据模型采用三个基本的概念：实体集，关系集，和属性。

**Entity Sets**

**实体**（Entity）是现实中的一个事情或一个**对象**，它是与其它对象有区别的，如大学中的一个人就是一个实体。一个实体有一组属性，它的属性值可以**唯一标识**一个实体。如，一个人可能有一个身份证号码属性，这个属性值可以唯一的标识一个人。一个实体可以是**具体的**，如一个人或一本书等，也可以是**抽象的**，如一个课程或一个机票预订等。**实体集**（Entity Set）是一组有相同类型的实体，它们共享相同的属性。

**Relationship Sets**

**关系**（Relationship）是几个实体之间的关联。如一个老师实体和一个学生实体之间有一个指导的关系。**关系集**（Relationship Sets）是一组相同类型的关系。多个实体集之间的关系总和构成了关系集。实体集之间的关联称为参与，如 实体集 E1，E2... 参与关系集 R。实体在关系中扮演的功能称为该实体的**角色**。

相同的实体集之间的可能超过一个关系集。

两个实体集之间的称为**二元关系集**（Binary Relationship Set）。然而，关系集可能涉及超过两个实体集。参与一个关系集的实体集的数量称为**Degree** of the relationship set。二元关系集的 degree 是 2，三元关系集的 degree 是 3。

**Attributes**

每一个属性有一组允许的值，称为 domain 或者 value set。每一个实体可以用一组属性和属性值对来描述。

E-R 模型中的属性类型有：

- 简单（Simple）和组合（Composite）属性。组合属性可以分为多个子部分，如 name 属性可以由 first_name, middle_name, 和 last_name 组合而成。
- 单值（Single-valued）和 多值（Multivalued）属性。单值属性表示只能出现一个值，如 ID 属性只能一个，多值属性表示可以有多个值，如 phone_number 可以有多个电话号码。
- 派生属性（Derived）。这类属性的值可以从其它相关属性的值得出。如 age 可以从 date_of_birth 属性值得出。

一个属性为 null 值表示不存在或者未知。

**Constraints**

E-R 模型中主要有两种约束：映射基数和参与约束。

**映射基数**（Mapping Cardinality）主要用于描述二元关系集。映射基数的类型有：

- One-to-one。一个实体 A 最多关联一个实体 B。
- One-to-many。一个实体 A 可以关联多个实体 B。
- Many-to-one。多个实体 A 可以关联一个实体 B。
- Many-to-many。一个实体 A 可以关联多个实体 B。一个实体 B 也可以关联多个实体 A。

**参与约束**（Participation Constraints）表示一个实体集参与一个关系的数量。主要有两种参与约束：

- Total。它表示一个实体集所有实体都参与了一个关系集。
- Partial。它表示一个实体集部分实体参与了一个关系集。

**Keys**

一个 key （键）是一组属性能够区分不同的实体。Superkey 表示可以区分实体的一组属性。Candidate key 表示最小的 Superkey 即用来区分实体的最小的属性集。Primary key 与 candidate key 是相同的，只是在不同的地方的表述，Primary key 用在数据库的表述中，而另一个用在 E-R 模型中。

**去除冗余的属性**

设计一个数据库的 E-R 模型，通常是先确定有哪些实体集，为实体集选择合适的属性，为实体集建立合适的关系。一些属性同时出现在多个实体集中，为它们建立关系集，并取出重复的属性。



### E-R 图

E-R 图可以图形化地表示数据库地总体的逻辑结构。E-R 图简单而清晰，被广泛使用在 E-R 模型中。

**基本结构**

E-R 图主要的组件有：

- 长方形分为两部分的（Rectangle Divided）：用来表示实体集。
- 菱形（Diamond）：表示关系集。
- 没有划分的长方形（Undivided Rectangle）：表示一个关系集的属性。
- 线（Line）：用来连接实体集和关系集。
- 虚线（Dashed Line）：用来连接一个关系集的属性到另一个关系集。
- 双线（Double Line）：用来指出一个实体集完全参与一个关系集。即表示参与约束。
- 双线菱形（Double Diamond）：表示关系集被弱实体连接的。

- 实体集的主键属性用下划线标识。

一个简单的 E-R 图，如下所示：

<img src="https://taogenjia.com/img/database-system-3-database-design/database-system-3-database-design-1-ER-diagram.png" class="img-center"/>



**E-R 图表示映射基数**

E-R 图使用关系集到实体集之间的有向线（Directed Line）和无向线（Undirected Line）来表示 one-to-one，one-to-many 等映射基数。有向线表示 one，无向线表示 many。如下 E-R 图表示 student 与 instructor 的 many-to-one 关系。

<img src="https://taogenjia.com/img/database-system-3-database-design/database-system-3-database-design-2-mapping-cardinality.png" class="img-center" />

更复杂的映射基数使用 l..h 表示，l 表示最小的基数，h 表示最大的基数。基数的值可以是 0 到无穷， * 表示无穷。常见的表示，如 1..1，0..* 。左边线上的基数表示右边实体集的参与数量，反之亦然。

更多的 E-R 图的表示，如：

- 组合属性（Composite Attributes）

- 角色（Role）

- 非二元关系集（Nonbinary Relationship Sets）

- 弱实体集（Week Entity Sets）

这些 E-R 表示的内容，不详细解释了，如有兴趣可查阅文章最后给出的参考书籍。

一个大学的 E-R 图例子如下图所示：

<img src="https://taogenjia.com/img/database-system-3-database-design/database-system-3-database-design-3-ER-example.png" class="img-center" />



### E-R 模型转换为关系模型

E-R 模型和关系模型都是抽象的逻辑的表示真实世界。要实现数据库必须把 E-R 模型转换为数据库系统对应的数据模型。当前小节，我们描述如何将 E-R 模型转换为关系型数据库的关系模型。主要涉及两个问题：E-R schema 如何转换为 relation schema，E-R schema 中的约束如何转换为 relation schema 的约束。

**强实体集的简单属性的表示**

E-R 模型中的一个 Entity Set 的每个属性对应一个 Relation 的每个元组。

**强实体集的复杂属性的表示**



### E-R 模型设计中的问题

### 扩展的 E-R 特性

### 其它的数据建模方式

### 数据库设计的其它方面



## 关系型数据库设计和标准化

### 好的关系设计的特点

### 原子域和第一范式

### 使用函数依赖进行分解

### 函数依赖理论

### 分解算法

### 使用多值依赖的分解

### 其它范式

### 关系型数据库设计过程

### 时态数据建模



## 小结

E-R 模型专注于实体和它们之间的关系，而标准化专注于去除冗余数据，防止数据修改不完整，标准化有利于保证数据的一致性。完整性约束防止数据错误修改，与标准化类似，它也是为了保证数据的一致性。



范式总结

| 名称 | What |      |
| ---- | ---- | ---- |
| 1NF  |      |      |
| 2NF  |      |      |
| 3NF  |      |      |
| BCNF |      |      |
| 4NF  |      |      |



## References

[1] Database System Concept (6th) by Avi Silberschatz, Henry F. Korth, and S. Sudarshan