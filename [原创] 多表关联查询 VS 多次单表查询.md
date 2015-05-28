## 多表关联查询 VS 多次单表查询 ##

工作快一年了，去年一直是前端，虽然是济南的一家小公司，但技术实力还是有的，不少同事都跳槽去了百度阿里。当年的我，自认为前端水平还是可以的，有过帮队友杀死无数BUG的辉煌战绩，总监对我也还算认可，于是乎，在几位前辈纷纷跳槽阿里之后，我的命运却突然发生了转折……

是的，公司业绩越做越好，核心技术成员却跑了一半多。总监一直对我不错，我也很听信总监的话，他觉得我还是有天赋的，于是，我终于踏上了后端这条不归路。期间的各种辛酸我在这里就不多说了，下面切入正题。

### 随处可见的表关联 ###

三个月的努力，我也算是团队的核心成员了，能够独立负责一块任务，也还算进步挺快，当然也不得不再次感谢总监对我的信任和栽培。下面说一下项目背景（这只是一个DEMO，我会尽可能脱离具体业务，毕竟这是受保护的）：

有一张 Article 表，这几乎是任何系统都必备的。

	@Entity
	public class Article {

    	@Id
    	@GeneratedValue(strategy = GenerationType.IDENTITY)
		private int id;

    	private String title;

    	private String content;

		// Getter and Setter
	}

当然，Article 表还可能拥有其他很多属性，譬如：autor、publishDate、column 等等，我们可以选择用继承是方式来实现，也很简单，但我不喜欢这种硬编码的方式，它的动态可调性仍然很差。我的选择是，将更多的权限留给生产阶段的后台管理系统，而不是开发阶段的程序员实现。

这个时候，我们需要关联一张 Field 表，实现一对多的单向关联。

	@Entity
	public class Article {

    	@Id
    	@GeneratedValue(strategy = GenerationType.IDENTITY)
		private int id;

	    @OneToMany
	    @JoinTable
	    private List<Field> fieldList;

		// Getter and Setter
	}

而 Field 表我们可以这样写：

	@Entity
	public class Field {

    	@Id
    	@GeneratedValue(strategy = GenerationType.IDENTITY)
		private int id;
		
		private String name;

    	private String value;

		// Getter and Setter
	}

这样利用 Hibernate 的自动建表，一对多的单向表关联就实现了，而且添加了一张中间表，主键则是利用了自增的 ID。当然还有 Getter/Setter 我在这里都省略了。

### 无所不能的 Hibernate ORM 却遭遇懒加载 ###

实体类有了，DAO层我选用的是 JPA 模板，JPA确实是非常好用的小玩意，没用过的同学可以试试，它可以帮你摆脱很多数据库访问时的低级趣味，解放双手。JPA的用法这里我就不再累述，下面直接进入业务逻辑：

	@Service
	public class ArticleServiceImpl implements ArticleService {
					
	    @Autowired
	    ArticleDao articleDao;

	    public List<Field> getFieldList(int articleID) {
	        return articleDao.findOne(articleID).getFieldList();
	    }

		// 此处略去10000字
	}

Hibernate ORM 我不知道大家是怎么理解的，但是我很喜欢它，毕竟是一名前端出身，半路出家，SQL语句是我们这类人共同的硬伤（实在是对不起我们敬爱的 Oracle 教授，当年我确实没想到我居然有用上它的一天）。但是自从认识了 Hibernate ORM 后，我能感觉到整个天空都不一样了，简洁的 HQL 语句就如同面向对象一般，非常实用。好的，接下来我们可以试着运行一下这段业务逻辑，那么，问题来了——

服务器毫不留情的抛出了一个异常：

> __unable to evaluate the expression Method threw 'org.hibernate.LazyInitializationException' exception.__

LazyInitializationException 是个什么鬼？好吧，我不知道，我来到那缥缈仙境的深处询问度娘。

引文出处：[http://cl315917525.iteye.com/blog/796703](http://cl315917525.iteye.com/blog/796703 "http://cl315917525.iteye.com/blog/796703")

> 错误 org.hibernate.LazyInitializationException

> org.hibernate.LazyInitializationException: could not initialize proxy - no Session 

> 原因：hibernate3 many-to-one的默认选项是 lazy = "proxy" 

> 解决方法：<many-to-one> & <set> 中设置 lazy="false" 

> HIBERNATE的持久化对象加载策略：延迟加载，也就是用到的时候才去加载，这样可以提高一些性能。 

> Hibernate的lazy loading 采用了一个HibernateSession来管理session，它的逻辑是每进行一次数据库操作，就开新的session，操作完成后立即关闭该session。这样做的好处是可以严格关闭session，避免菜鸟级的错误，但是hibernate.org并不推荐这么做。因为这不适合lazy loading，也不适合跨方法的事务。 

> 比如在我们的应用中，user->post形成一对多的映射，User中有一个包含post的List。 

> 在User中，有多个属性：name，password，phone等，还有一个List类型的posts。当我们对posts使用lazy laoding的时候，hibernate会在获得User对象的时候，仅仅返回name,password,phone等基本属性，当你访问posts的时候，它才会从数据库中提取posts需要的数据，这就是所谓lazy laoding。

> 但是在我们的系统中，session是被立即关闭的，也就是在读取了name,password,phone等基本属性后，session已经close了，再进行lazy loaiding就会有异常。 

> 解决办法是在close session之前，调用Hibernate.initialize(user.getPosts())，告诉系统，user.getPosts()是需要lazy laoding的。但是这样做会破坏HibernateSession类的封装。

> 后来采用所谓的OpenSessionInView模式，把session的周期交给servlet filter来管理，每当有request进来，就打开一个session，response结束之后再关闭它，这样可以让session存在于整个请求周期中。 

> Hibernate中Lazy延迟加载：Hibernate有关one-to-one和many-to-one在查询中的父亲端lazy问题。

> Hibernate3 在关联上有lazy这个属性，如果是Hibernate2，应该是设置outer-join="false"，然后被关联的对象，在class那个地方设置lazy="true"。

> 首先，对于many-to-one的问题，可以在父亲端的class标签中设置lazy来解决，这样，在查询儿子的时候，不会发送多余的sql 。

> 对于one-to-one，在hibernate2里面，由于one-to-one里面没有lazy的选项，所以只能通过设置outer-join="false"来解决。而hibernate3已经加入了lazy，所以不会有这个问题。 
总体来说，如果你发现你查询儿子的时候，有多余的sql发送，那一定是你对hibernate的误用.. 

> 在hibernate 的one-to-many,many-to-one,many-to-many中，为了效率的提高，我们一般都采用lazy机制，但使用spring的 getHibernateTemplate().save(Object)时，HibernateTemplate试图每次在execute之前去获得 Session，执行完就力争关闭Session 。

> 也就是说Hibernate的Lazy初始化1:n关系时，你必须保证是在同一个Session内部使用这个关系集合，不然Hiernate将抛出 Failed to lazily initialize a collection - no session or session was closed的例外。 

> Hibernate 中的对象的关联(association)的设置还是不够灵活，实际应用中有的地方需要lazy load，有的地方又不需要，其实还有的地方就根本不需要使用association。

> 而在Hibernate中，只能在影射文件中设置一种方式，像我们这样的应用，我是不敢轻易使用open session in view的(慢点总比lock住要好)，只能是要么不设置association，要么就是lazy=true的。

> 以前的分类信息只用了一个many to one的关系，代价还可以忍受，但现在关系越来越复杂了，再多加几个的话，所要付出的performance，带宽等方面的代价恐怕就不能忽略了,即使使用cache提高一点performance，对带宽的浪费也还是不可原谅的。

度娘 balabala 说了一大堆，我也没太过听懂，通俗点说吧，就是 Hibernate 默认开启延迟加载，session 是会被立即关闭的，所以我们需要做的最简单的办法就是关闭懒加载。

### 可怜那一把莫邪剑，只是你不会用 ###