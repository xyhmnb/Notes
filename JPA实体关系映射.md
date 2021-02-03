> 在使用JPA的过程中，经常遇到多表之间的关系，像@ManyToOne，@OneToOne这些。而这些注解参数使用不当，会造成极大性能损失

#### @JoinColumn

用来指定与所操作实体或实体集合相关联的数据库表的列字段。如果不写@JoinColumn，hibernate会自动生成关联字段

#### FetchType（加载策略）

fetch有两种类型，一种是EAGER（急加载），另一种是LAZY（懒加载）。

* EAGER，表示取出这条数据时，它关联的数据也同时取出放入内存中
* LAZY，表示取出这条数据时，它关联的数据并不取出，在同一个session中，什么时候用就什么时候取（再次访问数据库）。在session外就不能取数据了，而EAGER是在内存里，所以不受session影响

#### MappedBy（声明关系的维护方）

```markdown
//被维护方
@ManyToMany(mappedBy = "memberDomains", cascade={CascadeType.MERGE,CascadeType.DETACH} , fetch =  FetchType.EAGER)
private Set<LabelDictDomain> labelDictDomains = new HashSet<>();
//维护方
@ManyToMany(cascade={CascadeType.DETACH} , fetch =  FetchType.LAZY)
private Set<MemberDomain> memberDomains = new HashSet<>();
mappedBy声明于关系的被维护方，声明的值为关系的维护方的关系对象属性名。
```

被维护方不会主动去维护关联关系，真正的关系维护，掌握在维护方手中；维护方一定不能为空，被维护方可以为空。

#### CascadeType（级联关系）

* CascadeType.REMOVE：Cascade remove operation，级联删除操作，删除当前实体时，与它有映射关系的实体也会跟着被删除
* CascadeType.MERGE：Cascade merge operation，级联更新（合并）操作，当前实体中数据改变时，会相应更新到关联的数据
* CascadeType.PERSIST：Cascade persist operation，级联持久化（保存）操作（持久保存拥有方实体时，也会持久保存该实体的所有相关数据）。通俗解释：给当前设置的实体操作另外一个实体的权限

```java
public class Student {
    @ManyToMany(cascade=CascadeType.PERSIST,fetch=FetchType.LAZY)
    private Set<Course> courses = new HashSet<>();
    //其他代码略。
}
此时，若Student实体持有的Course实体在数据库中不存在时，保存该Student时，系统将自动在Course实体对应的数据库中保存这条Course数据。而如果没有这个权限，则无法保存该Course数据。
```

* CascadeType.DETACH：Cascade detach operation，级联脱管/游离操作。如果要删除一个实体，但是它有外键无法删除，你就需要这个权限了。他会撤销所有相关的外键关联
* CascadeType.REFRESH：Cascade refresh operation，级联刷新操作。

```markdown
假设场景 有一个订单,订单里面关联了许多商品,这个订单可以被很多人操作,那么这个时候A对此订单和关联的商品进行了修改,与此同时,B也进行了相同的操作,但是B先一步比A保存了数据,那么当A保存数据的时候,就需要先刷新订单信息及关联的商品信息后,再将订单及商品保存。
```

* **CascadeType.ALL**：Cascade all operations，拥有以上所有级联操作权限。

## 推荐设置

对所有关联使用**FetchType.LAZY**，避免造成加载表

##### @ManyToOne（默认FetchType.EAGER）

最好修改默认值

##### @OneToMany（默认FetchType.LAZY）

##### @OneToOne（默认FetchType.EAGER）

虽然一对一的关联只是加载一个额外的数据库记录，但是如果有多个这样的实体或实体中又加载实体，反复指定关联，积少成多照样会影响性能

##### @ManyToMany（默认FetchType.LAZY）



[参考链接](https://www.jianshu.com/p/e8caafce5445)

[参考链接](https://blog.csdn.net/pengjunlee/article/details/79972059)



[参考连接](https://www.ktanx.com/blog/p/4988)

