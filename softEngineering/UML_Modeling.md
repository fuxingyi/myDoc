# UML建模

## 分类
- 参与者（ator)
- 用例（use case)
- 类（class）
- 对象（object）
- 接口（interface）
- 子系统（subsystem）
- 包（package）
- 组件（component）
- 结点（node）
- 注释（comment）


## 关系
- 关联（association）：模型元素之间有一定的联系。
   - 组合：整体是由部分组成，整体不可分割，部分不能单独成为整体。例如：桌子与桌子腿的关系。
   - 聚合：整体是由部分组成，整体可分割，部分可以单独成为整体。例如：公司与员工的关系。
- 实现（realization）： 是对抽象的一种实现。例如：JPA（java持久化接口 Java Persistence API)与Hibernate 的关系（Hibernate实现了JPA定义的接口）。
- 泛化（继承）（generalization）：指一般和具体之间的关系。例如：人为一般称呼，学生为具体称呼，人与学生则是一种泛化关系。
- 依赖（dependency）：指一个元素对另一个元素的依赖，此处的依赖包括但不限于：实现（realize）、使用（usage）、实例化（instantiate）、调用（call）、派生（derive）、访问（access)、引入（import）、参数（parameter）等等。
- 约束（constraint）：

## 关系（UML 表示方法）
![关联](./image/%E5%85%B3%E8%81%94.png)
![实现-泛化](./image/%E5%AE%9E%E7%8E%B0-%E6%B3%9B%E5%8C%96.png)
![聚合](./image/%E8%81%9A%E5%90%88.png)































